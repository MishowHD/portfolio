+++
title = "Sito personale & Workflow"
date = "2025-10-20"
description = "Questo sito — costruito con Hugo e hugo-theme-terminal, deployato via GitHub Pages, servito tramite Cloudflare con dominio personalizzato e DNSSEC."
draft = false
+++

Il mio sito personale è costruito con **Hugo**, il tema **hugo-theme-terminal**, e un workflow di deployment automatizzato pensato per velocità, semplicità e manutenibilità a lungo termine.

---

## Stack tecnologico

- Hugo (static site generator)
- Tema hugo-theme-terminal
- Contenuti in Markdown
- Setup multilingua EN/IT
- Repository GitHub per il version control
- GitHub Pages per le build automatizzate
- Cloudflare per CDN, HTTPS e caching
- Dominio custom con DNSSEC abilitato

---

## Workflow di deployment

1. Scrivi i contenuti in Markdown
2. Commit su GitHub
3. GitHub Pages compila il sito con Hugo
4. Cloudflare distribuisce i file globalmente

Questo garantisce: build automatiche, deployment atomici, caching CDN globale, HTTPS di default, zero downtime.

---

## Sicurezza e performance

- Completamente statico (nessun backend, nessun database)
- Servito su HTTPS
- DNSSEC abilitato
- Cache globale via Cloudflare
- Nessuno script di tracking o analytics
