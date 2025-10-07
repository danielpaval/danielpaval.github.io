---
title: "Embedding Apache Superset Dashboards"
date: 2025-10-07
---

A demonstration project showing how to embed Apache Superset dashboards into external applications using the Superset SDK.

## Overview

[Apache Superset](https://superset.apache.org/) is a powerful open-source data exploration and visualization platform. This demo project showcases how to embed Superset dashboards into a Spring Boot application, enabling seamless integration of analytics dashboards into your own applications.

**Repository**: [danielpaval/superset-embed-demo](https://github.com/danielpaval/superset-embed-demo)

## Project Stack

The project combines several technologies:

- **Apache Superset** (Python) - The analytics platform
- **Spring Boot** (Java) - Backend application framework
- **Docker** - Containerized deployment
- **Gradle** - Build automation

## Key Configuration

To enable embedding Superset dashboards, several configuration changes are required:

### Docker Environment

```bash
SUPERSET_FEATURE_EMBEDDED_SUPERSET=true
```

### Superset Configuration

In `superset_config.py`:

```python
TALISMAN_ENABLED = False
OVERRIDE_HTTP_HEADERS = {'X-Frame-Options': 'ALLOWALL'}
ENABLE_CORS = True
CORS_OPTIONS = {
    'supports_credentials': True,
    'allow_headers': ['Content-Type', 'Authorization'],
    'resources': ['*'],
    'origins': ['*']
}
GUEST_ROLE_NAME = "Gamma"
```

## Running the Project

The Docker setup is based on the official Superset Docker Compose configuration with modifications to support embedding:

```bash
docker compose -f .\docker-compose-image-tag.yml up -d
```

## Features

- ✅ Embedded Superset dashboards in external applications
- ✅ CORS configuration for cross-origin requests
- ✅ Guest role support for embedded access
- ✅ Spring Boot backend integration
- ✅ Dockerized deployment

## Use Cases

Embedding Superset dashboards is useful when you want to:

- Integrate analytics into your existing application UI
- Provide customers with branded analytics experiences
- Embed data visualizations in portals or SaaS platforms
- Create custom dashboards with dynamic row-level filters

## References

- [Superset Embedding Documentation](https://superset.apache.org/docs/6.0.0/configuration/networking-settings/#enabling-embedding-via-the-sdk)
- [Embedding with Dynamic Filters](https://blog.coffeeinc.in/embedding-apache-superset-dashboards-with-dynamic-row-level-filters-cc377b50f7ea)

## Next Steps

Future enhancements could include:

- WTF_CSRF_EXEMPT_LIST configuration
- Dynamic row-level security filters
- User authentication integration
- Production-ready security hardening

---

Check out the full source code at [github.com/danielpaval/superset-embed-demo](https://github.com/danielpaval/superset-embed-demo)
