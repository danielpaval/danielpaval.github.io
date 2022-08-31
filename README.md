This is a green attempt to start a Jekyll-based blog on GitHub pages.

# TODO

- Tags
- Archive / Calendar
- New template
- Comments
- Google Analytics
- Landing page content
- Description

# Development

Use `--force-polling` with WSL if  `--livereload` doesn't work:

```bash
bundle exec jekyll serve --force-polling
```

Other useful commands:

```bash
bundle install                    # Install gems
bundle exec github-pages versions # List gems in github-pages group (https://github.com/github/pages-gem)
bundle update github-pages        # Update github-pages gem
```

# Pros & Cons

## Jekyll

Pros

- 

Cons

- Different themes might require deeper changes

## Hugo

Examples

- https://gohugo.io/showcase/hapticmedia/

Cons

- Use Go in templates

## Publii

Cons

- No automatic reload
- Not sure about how easy it is to collaborate via Git
- Some issues with footnotes