+++
title = "Migrazione Server — Da bootc a NixOS"
date = "2026-07-28"
description = "Perché ho migrato il mio server homelab da bootc su Fedora a NixOS — iptables vs nft, rottura del kernel, uso di kernel LTS e la fine della configuration drift."
tags = ["linux", "nixos", "server", "homelab", "bootc", "kubernetes"]
draft = false
+++

Di recente ho migrato il mio server homelab da un setup **bootc** (bootable container) su Fedora a **NixOS**. Il motivo non è stato filosofico — è stata una rottura reale causata dalla transizione iptables/nft e dalla politica kernel di Fedora.

---

## Il setup con bootc

**bootc** è un modello in cui l'intero OS è definito come un'immagine OCI container. Scrivi un `Containerfile`, costruisci l'immagine, la pusha su un registry, e il server la scarica e ci fa boot. È l'immutable infrastructure portata alle sue conseguenze logiche — il tuo OS ha una pipeline CI/CD.

Il concetto mi piaceva genuinamente:
- OS definito come `Containerfile`
- Aggiornamenti atomici via pull dell'immagine
- Rollback facendo boot sull'immagine precedente
- Tutto versionato e riproducibile

Ma poi il kernel ha rotto le cose.

---

## Il problema iptables / nft

Per capire cosa è successo serve un po' di contesto sul networking Linux.

### La storia

Linux usava **iptables** per il filtraggio dei pacchetti e il NAT. L'iptables originale si chiama ora **iptables-legacy**. Il suo successore è **nftables (nft)** — un framework più moderno e flessibile.

Il problema: non si possono riscrivere dall'oggi al domani tutte le applicazioni che usano iptables per farle usare nft. Quindi è stato creato un **layer di compatibilità**: **iptables-nft**. Fornisce il frontend di iptables (stessa CLI, stessa API) ma usa nft come backend. Le applicazioni legacy continuano a chiamare le API di iptables, ma sotto il cofano stanno modificando regole nft.

### Perché si rompe

Questo layer di compatibilità non è una soluzione a lungo termine. nft continua ad evolversi, e le nuove funzionalità di nft non hanno contropartita iptables. Per il layer di compatibilità, queste sono effettivamente **breaking change** — non per nft in sé, ma per iptables-nft.

Un esempio concreto: nel **kernel 7.1+**, nft ha introdotto una **bitmask nelle regole di NAT** che permette di aggiungere metadati ai pacchetti che attraversano le chain. Tailscale la usa per marcare i propri pacchetti — così da distinguere il traffico generato da Tailscale da quello esterno per il routing.

Questa bitmask **non ha equivalente iptables**. Quindi se fai un `iptables-save`, non sa come interpretare la regola e si rompe. Peggio: se un'applicazione che usa iptables-nft prova a modificare regole in una chain che contiene funzionalità native nft, può corrompere l'intero ruleset nft.

### L'impatto reale

La regola è semplice: o **tutte** le tue applicazioni parlano nft nativo, oppure rischi che quelle che usano iptables-nft si rompano — e nel farlo, rompano le regole nft impostate da altre applicazioni.

Nel mio caso:
- **Tailscale** usa nft nativamente
- **kube-router** e **kube-proxy** usano iptables come frontend

Questi due mondi sono entrati in collisione. kube-router non riusciva a modificare certe regole perché contenevano costrutti nft nativi che non capiva. Il networking del mio Kubernetes si è rotto.

### Perché il mondo server resta conservativo

È per questo che la direzione generale negli ambienti server è restare su **versioni ben testate e LTS del kernel**. Questo dà tempo agli sviluppatori di applicazioni di costruire workaround o migrare a nft nativo. Progetti come kube-router e kube-proxy hanno riconosciuto che passare a nft nativo sarebbe un lavoro mastodontico — non è una cosa che si fa dall'oggi al domani.

### Il problema di Fedora

Qui è dove bootc + Fedora è diventato un problema. Fedora è **rolling release** per quanto riguarda il kernel. Sia Fedora 43 che 44 usano già il **kernel 7.x** (come il 7.2), dove i plugin CNI per Kubernetes — e il software server in generale — non sono ancora testati per i nuovi comportamenti di netfilter.

Il mio setup si è rotto per questo: kube-router su un kernel che ha introdotto funzionalità nft incompatibili con il frontend iptables.

---

## La soluzione: Passaggio a NixOS con kernel LTS

Visto che usavo già NixOS sul desktop e mi trovavo benissimo, passare il server a NixOS è stata la risposta naturale.

Invece di subire Fedora che aggiornava il kernel a release 7+ rompendo i plugin CNI di Kubernetes, con NixOS posso bloccare esplicitamente il server su un kernel LTS stabile come il 6.18 (`pkgs.linuxPackages_lts`) con una singola riga di configurazione:

```nix
boot.kernelPackages = pkgs.linuxPackages_lts;
```

Questo mi ha dato il meglio dei due mondi: un ambiente kernel stabile e conservativo dove `kube-router` e `iptables-nft` funzionano senza sorprese, unito a tutta la potenza di NixOS.

La mia configurazione NixOS è in un singolo repo — [github.com/MishowHD/mishow-nix](https://github.com/MishowHD/mishow-nix) — che copre sia desktop che server.

Uso **k3s** su NixOS con kernel LTS, e finora è roccioso. I vantaggi sono chiari:

- **Stabilità del kernel LTS** — nessun aggiornamento del kernel inaspettato che rompe le regole di rete
- **Single source of truth** — la config del server sono semplicemente altri moduli Nix nello stesso repo
- **Nessuna configuration drift** — se non è nella config, non è sul server
- **Rollback atomici** — se un aggiornamento rompe qualcosa, faccio boot sulla generazione precedente
- **Riproducibile** — posso ricostruire l'esatto stesso server dalla sola configurazione

---

## Cosa ho perso

- **L'eleganza di bootc** — il modello OCI è genuinamente elegante. Costruire il proprio OS come immagine container e pullare gli aggiornamenti è un workflow pulito
- **L'ecosistema Fedora** — Fedora ha ottimi strumenti e una community forte. La politica kernel è l'unico motivo per cui me ne sono andato

---

## Conclusione

Il passaggio è stato guidato da una rottura reale e dolorosa — non dall'ideologia. La transizione iptables/nft è un periodo complicato per il networking Linux, e usare kernel bleeding-edge sui server è rischioso se il tuo stack include qualcosa che parla ancora iptables.

NixOS mi ha dato quello che bootc prometteva — dichiarativo, riproducibile, rollbackabile — permettendomi al contempo di impostare un kernel LTS conservativo. Per il mio server homelab, è stata la scelta giusta.
