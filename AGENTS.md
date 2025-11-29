## Repo Facts
- Static blog built with Hugo (extended) using the PaperMod theme (module import in `config.yaml`).
- Posts live in `content/posts/<slug>/index.md` bundles; assets sit beside each post (Hugo page bundles).
- Generated site publishes to `gh-pages` via `.github/workflows/github-pages.yml`.

## Local Development
- Run `hugo server --baseURL=http://localhost:1313/` from repo root for live preview.
- Search, tags, and timeline pages exist under `content/search/_index.md`, `content/tags/_index.md`, `content/archives/_index.md`.
- Taxonomies configured in `config.yaml` under `languages.en.taxonomies`.

## Theme Notes (PaperMod)
- Cover images: set per-post `cover.image` in front matter if needed.
- Search needs `outputs.home` to include `JSON` (already enabled).
- Archives layout is named "Timeline" in nav (`menu.main`).

## Authoring Guidelines
- Front matter uses YAML with `title`, `date`, optional `author`, `tags`, `cover`, etc.
- Keep posts in Markdown; diagrams (Mermaid, Draw.io) can live alongside `index.md`.
- Drafts stay hidden if `draft: true`; to show WIP content, publish normally and add a manual note.

## Misc
- Global author metadata lives in `params.author` inside `config.yaml`.
- `AGENTS.md` summarizes context for IDE assistants—keep it updated when workflows or structure change.
- Need deeper docs? Use the Context7 MCP tools to pull the official Hugo or PaperMod documentation directly in-editor before making assumptions (expecially https://github.com/adityatelange/hugo-PaperMod/wiki/Features).
