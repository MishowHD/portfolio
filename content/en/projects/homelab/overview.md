+++
title = "Stack & Infrastructure"
date = "2025-10-20"
description = "An overview of the homelab: virtualization on Proxmox, self-hosted services, and the full Kubernetes + networking stack."
tags = ["homelab", "proxmox", "kubernetes"]
draft = false
+++

My homelab started as a small Linux test server and gradually evolved into a full **virtualized, self-hosted infrastructure**, built for learning, experimentation, and reliability.

## Stack

- **Proxmox VE** — virtualization layer (KVM + LXC)
- **k3s** — lightweight Kubernetes cluster
- **Flux CD** — GitOps continuous delivery
- **SOPS + age** — encrypted secrets in git
- **Tailscale** — secure remote access and subnet routing

## Services

- **Immich** — photo and video management (backed by CloudNativePG)
- **Nextcloud** — personal cloud, file sync, notes, calendar
- **Pi-hole** — network-wide DNS filtering

## Networking

- **ExternalDNS** + Cloudflare for automatic DNS management
- **cert-manager** — DNS-01 wildcard certificates via Let's Encrypt
