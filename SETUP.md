# Setup checklist — from this folder to a live GitHub Pages site

Everything below is one-time setup. After it, publishing is `git push`.

---

## 1. Install Quarto locally (Windows)

- Download the Windows installer: <https://quarto.org/docs/get-started/>
- Optional but recommended: the **Quarto** extension for VS Code (gives you
  preview, render buttons, and YAML autocomplete).
- Check it worked — open a terminal in this folder:

```powershell
quarto check
quarto preview
```

`quarto preview` opens the site at <http://localhost:4200> and live-reloads as
you edit. Stop it with `Ctrl+C`.

---

## 2. Replace the placeholders

Search the folder for `YOUR-GITHUB-USERNAME` and `YOUR-HANDLE` and replace them.
They appear in:

| File | What to change |
|---|---|
| `_quarto.yml` | `site-url`, the GitHub and LinkedIn hrefs, `title`, footer |
| `index.qmd` | your bio, links, education |
| `README.md` | the site URL |

Also swap `images/profile.svg` for a real photo (`profile.jpg` works — just
update the `image:` line in `index.qmd`), and replace each
`projects/<slug>/thumbnail.svg` with a real screenshot or map export.

---

## 3. Decide which repo type you want

**Option A — user site (recommended if this is your main personal site)**

- Repo name must be exactly `YOUR-GITHUB-USERNAME.github.io`
- Site URL: `https://YOUR-GITHUB-USERNAME.github.io/`
- In `_quarto.yml`: `site-url: "https://YOUR-GITHUB-USERNAME.github.io"`
- Nothing else to configure.

**Option B — project repo (e.g. a repo called `website`)**

- Site URL: `https://YOUR-GITHUB-USERNAME.github.io/website/`
- In `_quarto.yml`: `site-url: "https://YOUR-GITHUB-USERNAME.github.io/website"`
- Quarto writes relative links, so internal navigation still works. The only
  things that break with the wrong `site-url` are the RSS feed, sitemap, and
  social-preview cards — so set it correctly.

You can have one user site and unlimited project sites on a free account, and
you can rename the repo later (GitHub redirects the old URL).

---

## 4. Create the repo and push

In this folder:

```powershell
git init
git branch -M main
git add .
git commit -m "Initial Quarto site"
git remote add origin https://github.com/YOUR-GITHUB-USERNAME/YOUR-REPO.git
git push -u origin main
```

Create the empty repo on github.com first (no README, no .gitignore — this
folder already has one).

---

## 5. Turn on GitHub Pages

On github.com, in your repo:

1. **Settings → Pages**
   - *Source*: select **GitHub Actions** (not "Deploy from a branch").
2. **Settings → Actions → General**
   - *Workflow permissions*: **Read and write permissions** — needed so the
     workflow can publish the Pages artifact.
3. Go to the **Actions** tab. The `Publish website` run should already be going
   from your first push. It takes about a minute.
4. Back in **Settings → Pages** you will see the live URL.

If the repo is private, Pages requires GitHub Pro (or make the repo public).

---

## 6. How publishing works from now on

```powershell
quarto preview          # write, check locally
git add .
git commit -m "Add flood risk project"
git push
```

The workflow at `.github/workflows/publish.yml` renders the site on GitHub's
runners and deploys it. You never commit the `_site/` folder — it is gitignored.

### About `_freeze/`

`_quarto.yml` sets `execute: freeze: auto`. When you render locally, the output
of any R or Python chunks is cached into a `_freeze/` folder. **Commit that
folder.** It means GitHub's runner does not need R, Python, geopandas or any of
your packages installed — it just reuses your cached output.

If you ever want CI to execute the code itself instead, uncomment the Python
setup block in the workflow and add a `requirements.txt`.

---

## 7. Adding a new project

```powershell
mkdir projects\my-new-project
```

Create `projects\my-new-project\index.qmd`:

```yaml
---
title: "Title shown on the card and the page"
description: "One or two sentences that appear on the listing card."
author: "Benjamin Tee"
date: "2026-08-16"
categories: [Python, GIS]
image: thumbnail.png
---
```

Then write the body in Markdown below the `---`. Drop a `thumbnail.png` (about
1200×800) in the same folder. That is the whole workflow — the listing page
picks it up automatically, sorts it by date, and adds its categories to the
filter sidebar. Nothing in `projects/index.qmd` needs editing.

Use `draft: true` in the front matter to keep something out of the listing
while you work on it.

### Executable code

Fenced chunks written as ```` ```{python} ```` or ```` ```{r} ```` are executed
at render time and their output embedded. Plain ```` ```python ```` fences are
displayed but not run. For interactive maps, `folium`, `leaflet` and `plotly`
output all embed straight into the page.

---

## 8. Optional extras

- **Custom domain**: Settings → Pages → Custom domain, then add a `CNAME` file
  at the repo root containing the domain. Point a `CNAME` DNS record at
  `YOUR-GITHUB-USERNAME.github.io`.
- **Add a Blog section**: copy `projects/` to `blog/`, keep only `index.qmd`
  and `_metadata.yml`, change the title, and add it to the navbar.
- **Add a CV page**: create `cv.qmd`, drop `cv.pdf` in the repo, link to it.
- **Comments on posts**: add `comments: giscus:` config under `format: html` in
  `_quarto.yml` (needs a public repo with Discussions enabled).

---

## Troubleshooting

| Symptom | Fix |
|---|---|
| Actions run fails at "Deploy to Pages" | Settings → Pages → Source must be **GitHub Actions** |
| Site builds but CSS is missing | Wrong `site-url` for a project repo — include the repo name |
| Thumbnail not showing on the card | The `image:` path is relative to the post's own folder |
| A post is missing from the listing | It needs a `date:` and must be named `index.qmd` inside its own folder; check `draft:` |
| CI fails on a code chunk | Render locally and commit `_freeze/` |
