# Personal website — Quarto + GitHub Pages

Source for <https://github.com/benjamintee>.

## Structure

```
.
├── _quarto.yml              # site-wide config: title, navbar, theme, site-url
├── index.qmd                # home / about page
├── projects/
│   ├── index.qmd            # the listing page (auto-generated from posts)
│   ├── _metadata.yml        # defaults applied to every post in this folder
│   └── <project-slug>/
│       ├── index.qmd        # the write-up
│       └── thumbnail.svg    # card image for the listing
├── theme/
│   ├── custom.scss          # light theme overrides
│   ├── custom-dark.scss     # dark theme overrides
│   └── styles.css           # plain CSS tweaks
├── images/                  # profile photo, favicon, shared assets
├── .github/workflows/publish.yml
└── _freeze/                 # cached code output (commit this)
```

## Adding a project

1. `mkdir projects/my-new-project`
2. Create `projects/my-new-project/index.qmd` with front matter:

```yaml
---
title: "Title shown on the card and the page"
description: "One or two sentences for the listing card."
author: "Benjamin Tee"
date: "2026-08-16"
categories: [Python, GIS]
image: thumbnail.svg
---
```

3. Drop a `thumbnail.svg` in the same folder (roughly 1200×800 works well).
4. Write the body in Markdown. Code chunks execute if you use ```` ```{python} ````
   or ```` ```{r} ```` fences.
5. `quarto preview` to check, then commit and push.

Set `draft: true` in the front matter to keep a post out of the listing while
you work on it.

## Local commands

```bash
quarto preview          # live-reloading local server
quarto render           # build once into _site/
quarto render projects/my-new-project/index.qmd   # render a single page
```

## Publishing

Pushing to `main` triggers `.github/workflows/publish.yml`, which renders the
site and deploys it to GitHub Pages. Nothing needs to be built by hand.
