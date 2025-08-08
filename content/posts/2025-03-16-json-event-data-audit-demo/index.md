---
title: "JSON Event Data Audit Demo"
date: 2025-03-16
---

# Context

The opportunity for this analysis originated with the need to ingest highly-dynamic unstructured data produced by
various events and be able to efficiently report on it both programmatically and to (business) users. In an ecommerce
scenario, an event could be a customer registration, an order, a shipment, a return, etc.:

```json
{
  "type": "REGISTRATION",
  "data": {
    "name": "Jane Smith",
    "email": "jane.smith@example.com",
    "phone": "987-654-3210",
    "addresses": [
      {
        "default": true,
        "street": "1742 Waverley Drive",
        "city": "Palo Alto",
        "state": "CA",
        "zip": 94301,
        "country": {
          "name": "United States",
          "code": "US"
        }
      }
    ]
  }
}
```

# TLDR

# Chain of Thought & Prototyping

The following steps roughly show the evolution of my ideas and solution components along the way. In order to proceed
with an objective evaluation of improvements I tried to rely on a core set of non-functional metrics:

- **Storage Size**: How much space does the persisted data take up?
- **Cold Start Report Time**: How long does it take to run a report for the first time?
- **Performance Test Time(s)**: What are the P50 and P95 times for volumes of at least 1M events?

## 1. SQL vs NoSQL

While it might be obvious that unstructured data is not the best friend of a relational database, keeping it in a
document database like MongoDB or Elasticsearch might not be natively optimal out of the box either. For example, to
keep maintenance at a minimum it would be ideal to have a consolidated index mapping in Elasticsearch across all use
cases. However, to prevent
a [mapping "explosion"](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-explosion.html) due to
virtually unlimited event definitions and subsequent revisions, one solution is to post-process the JSON data by
flattening it to a predictable structure using the dot notation familiar from JSONPath:

```json
{
  "type": "REGISTRATION",
  "data": [
    {
      "key": "name",
      "value": "Jane Smith"
    },
    {
      "key": "email",
      "value": "jane.smith@example.com"
    },
    {
      "key": "phone",
      "value": "987-654-3210"
    },
    {
      "key": "addresses[0].default",
      "value": true
    },
    {
      "key": "addresses[0].street",
      "value": "1742 Waverley Drive"
    },
    {
      "key": "addresses[0].city",
      "value": "Palo Alto"
    },
    {
      "key": "addresses[0].state",
      "value": "CA"
    },
    {
      "key": "addresses[0].zip",
      "value": 94301
    },
    {
      "key": "addresses[0].country.name",
      "value": "United States"
    },
    {
      "key": "addresses[0].country.code",
      "value": "US"
    }
  ]
}
```

This keeps relational databases such as Oracle on the table with well planned joins, but Elasticsearch remains the
top contender.

## 2. Elasticsearch

Such a flattened data structure can be covered by
Elasticsearch's
[`flattened` field type](https://www.elastic.co/guide/en/elasticsearch/reference/current/flattened.html).
However, it comes with limitations such as handling everything as string keywords without being able to handle native
number or date queries for such field types in the data JSON. Still, a similar custom flattening effect can be
achieved at application level using the
[`nested` field type](https://www.elastic.co/guide/en/elasticsearch/reference/current/nested.html).

Next, Elasticseach can be approached with multiple strategies:

- (Classic) Indexes
- Data streams

## 3. Elasticsearch Indexes



## 4. Elasticsearch Data Streams

## 5. Reports

