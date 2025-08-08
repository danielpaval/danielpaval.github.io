---
title: "Structurizr vs LikeC4"
date: 2025-05-11
draft: true
---

# 2. Export

## 2.1 Static Site

### Structurizr

Prerequisites:
- [Graphviz](https://graphviz.org) dot

Command:

```shell
structurizr export -workspace workspace.dsl -format static
```

![structurizr-export-static.png](/assets/2025-05-11-structurizr-vs-likec4/structurizr-export-static.png)

Conlusions:
- The static export of Structurizr is a very trimmed version of the Lite version, without the top menu, just the navigation feature starting with the top context diagram

### LikeC4

```shell
likec4 build -o ./dist
```

## 2.2 SVG/PNG

### Structurizr

Structurizr CLI can export to SVG or PNG format via a Node.js and Puppeteer [workaround](https://github.com/structurizr/puppeteer):

Prerequisites:
- [Node.js](https://nodejs.org)
- Puppeteer

```shell
node export-diagrams.js http://localhost:8080/workspace/diagrams svg
```

```shell
```

### LikeC4

```shell
likec4 export png -o ./png
```

## 2.3 Mermaid

As Mermaid becomes the factor standard for diagrams-as-code, it is worth looking into converting Structurizr or LikeC4 diagrams in order to align with other Mermaid sources.

### Structurizr

```shell
structurizr export -workspace workspace.dsl -format mermaid
```

Notes
- The Structurizr CLI can export to Mermaid format, but the output rendered by the official Mermaid Chart VS Code extension has some arrows rendered incorrectly and I'm not sure if it the extension or the way the Structurizr CLI is exporting the diagram; however, subsequent Mermaid-to-SVG output seems correct:

```shell
mmdc -i structurizr-SystemContext.mmd -o structurizr-SystemContext.svg
```

## 2.4 Other Formats

### Structurizr

- Stricturizr CLI can export to D2 format, but the output errors out when parsed by the D2 CLI: `err: failed to compile -: multiboard output cannot be written to stdout`