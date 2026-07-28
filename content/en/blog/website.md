+++
title = "Personal Website & Workflow"
date = "2025-10-20"
description = "This site — built with Hugo and hugo-theme-terminal, deployed via GitHub Pages, served through Cloudflare with a custom domain and DNSSEC."
draft = false
+++

My personal website is built using **Hugo**, the **hugo-theme-terminal** theme, and an automated deployment workflow designed for speed, simplicity, and long-term maintainability.

I chose an open-source static site generator to keep full control over my content, ensure long-term stability, and avoid dependencies on external platforms or databases.

---

## Tech Stack

- Hugo (static site generator)
- hugo-theme-terminal theme
- Markdown-based content
- Multilingual EN/IT setup
- GitHub repository for version control
- GitHub Pages for automated builds
- Cloudflare for CDN, HTTPS, and caching
- Custom domain with DNSSEC enabled

---

## Why Hugo

- Extremely fast (builds in milliseconds)
- Open-source and actively maintained
- Secure by design (no server-side code, no database)
- Flexible thanks to themes, templates, and shortcodes
- Ideal for long-term maintainability

---

## Deployment Workflow

1. Write content in Markdown
2. Commit changes to GitHub
3. GitHub Pages builds the site using Hugo
4. Cloudflare distributes the static files globally

This gives: automatic builds, atomic deployments, global CDN caching, HTTPS by default, zero downtime.

---

## Security and Performance

- Fully static (no backend, no database)
- Served over HTTPS
- DNSSEC enabled
- Globally cached via Cloudflare
- No tracking or analytics scripts
