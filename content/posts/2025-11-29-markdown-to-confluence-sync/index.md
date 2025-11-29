---
title: "Markdown to Confluence Sync Workflow"
date: 2025-11-29
---

I wanted to jot down a few notes about the [`Sync to Confluence` workflow](https://github.com/danielpaval/markdown-to-confluence/blob/main/.github/workflows/sync-to-confluence.yml) that keeps the `markdown-to-confluence` docs mirrored in Atlassian.

## Why it's handy

Keeping authoring in Markdown means the docs stay right beside the codebase, so assistants like GitHub Copilot (and other IDE-aware agents) can ingest real project guidance without leaving the editor. The same repo search powers autocomplete, inline explanations, and context-aware chat, which is way faster than switching to a browser tab and hunting through Confluence. Non-developers still get the polished Confluence pages produced by the workflow, so stakeholders have a familiar place to read without digging into Git.

## How it's handy

- ✅ Handles diagrams and code nicely:
  - **Mermaid** diagrams render to SVG files before upload, so vector quality survives the sync.
  - **Draw.io** diagrams render to PNGs that keep the editable XML in their metadata, so edits remain round-trippable.
  - **Markdown code** fences turn into native Confluence code blocks, so syntax highlighting and copy/paste ergonomics carry over automatically.
- ✅ Automatically mirrors anything under `docs/` whenever `main` changes, plus it supports manual `workflow_dispatch` runs when you just need a one-off push.
- ✅ Uses `Telefonica/markdown-confluence-sync-action@v2` in `tree` mode so nested docs stay intact without having to handcraft per-file rules.
- ✅ Keeps secrets tidy by pulling the Confluence email/token from `secrets` and everything else (URL, space key, root page) from repo-level `vars`.
- ✅ Adds a prominent automation notice so editors in Confluence know any manual edits will get overwritten on the next sync.

## GitHub Actions Workflow

```yaml
- name: Sync markdown files to Confluence
  uses: Telefonica/markdown-confluence-sync-action@v2
  with:
    mode: tree
    docs-dir: './docs'
    confluence-url: ${{ vars.CONFLUENCE_URL }}
    confluence-root-page-id: ${{ vars.CONFLUENCE_ROOT_PAGE_ID }}
    confluence-space-key: ${{ vars.CONFLUENCE_SPACE_KEY }}
    confluence-authentication: |
      {
        "basic": {
          "email": "${{ secrets.CONFLUENCE_EMAIL }}",
          "apiToken": "${{ secrets.CONFLUENCE_API_TOKEN }}"
        }
      }
    confluence-notice-message: '<p><strong>AUTOMATION NOTICE: This page is synced automatically from GitHub, manual changes will be lost</strong></p>'
    log-level: info
```

If you're wiring up something similar, this file is a solid template: minimal moving parts, a single job, and it keeps the Confluence tree tidy without extra scripting.
