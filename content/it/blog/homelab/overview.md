+++
title = "Stack & Infrastruttura"
date = "2025-10-20"
description = "Una panoramica dell'homelab: virtualizzazione su Proxmox, servizi self-hosted e lo stack completo Kubernetes + networking."
tags = ["homelab", "proxmox", "kubernetes"]
draft = false
+++

Il mio homelab è partito come un piccolo server Linux di test e si è evoluto in una **infrastruttura virtualizzata e self-hosted**, costruita per imparare, sperimentare e garantire affidabilità.

## Stack

- **Proxmox VE** — strato di virtualizzazione (KVM + LXC)
- **k3s** — cluster Kubernetes leggero
- **Flux CD** — continuous delivery GitOps
- **SOPS + age** — segreti cifrati nel repository
- **Tailscale** — accesso remoto sicuro e subnet routing

## Servizi

- **Immich** — gestione di foto e video (con CloudNativePG)
- **Nextcloud** — cloud personale, sync file, note, calendario
- **Pi-hole** — filtro DNS a livello di rete

## Networking

- **ingress-nginx** + **MetalLB** (modalità L2) per il traffico in-cluster
- **ExternalDNS** + Cloudflare per la gestione automatica dei record DNS
- **cert-manager** — certificati wildcard DNS-01 via Let's Encrypt
