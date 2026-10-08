# Purple Team Home Lab — portfolio site

A static portfolio documenting a purple-team home lab: building a segmented
enterprise network, running attacks from the adversary's point of view, then
detecting and hardening against each one. Built with
[MkDocs](https://www.mkdocs.org/) +
[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/).

## Local preview

```bash
pip install -r requirements.txt
mkdocs serve           # live-reload at http://127.0.0.1:8000
mkdocs build --strict  # produce ./site, failing on any broken link
```

## Publishing to GitHub Pages

The site deploys automatically via GitHub Actions on every push to `main`
(`.github/workflows/deploy.yml`). One-time setup after the repo exists:

1. Push this repository to GitHub (`main` branch).
2. In the repo: **Settings → Pages → Build and deployment → Source =
   "GitHub Actions"**.
3. The next push to `main` builds and publishes the site; the URL appears in the
   Actions run and under Settings → Pages.

Then fill in the three `TODO` lines in `mkdocs.yml` (`site_url`, `repo_url`, and
the social link) with the real repository/Pages URLs.

## Structure

```
docs/
  index.md                 landing
  lab/                     the environment: architecture, build story, routing core
  methodology.md           the attack → detect → mitigate discipline
  campaigns/               technique clusters, each with its defensive counterpart
  detection.md             detection-engineering inventory + blind spots
  tools.md                 toolbox
  lessons.md               engineering lessons from real debugging
  reference.md             OSI correlation, kill-chain, ports, addressing
mkdocs.yml                 site + theme + nav configuration
```
