+++
title = "Da Gentoo a NixOS — Il mio percorso desktop"
date = "2026-07-28"
description = "Come sono passato da Arch a Gentoo a NixOS sul mio desktop — cosa ho amato, cosa si è rotto, e perché il dichiarativo ha vinto."
tags = ["linux", "gentoo", "nixos", "dichiarativo"]
draft = false
+++

Faccio distro-hopping con criterio da un po'. Non per divertimento — ogni cambio mi ha insegnato qualcosa di fondamentalmente diverso su Linux. Questa è la storia di come sono passato da Arch a Gentoo a NixOS sulla mia workstation principale.

---

## Arch — più di un anno come daily driver

Uso Linux da molto tempo e ho accumulato una notevole esperienza su diverse distribuzioni prima di passare ad Arch. Arch è diventata la mia distribuzione principale per più di un anno. Rolling release, installazione minimale e la mitica wiki l'hanno resa un daily driver solido. Mi ha insegnato a costruire un sistema da zero e a mantenerlo nel tempo. Ma dopo oltre un anno di utilizzo quotidiano, volevo un livello di controllo ancora più profondo — non solo su cosa installavo, ma su *come* veniva compilato e configurato.

---

## Gentoo — imparare ad amare la compilazione

Sono passato a Gentoo perché volevo provare una distro source-based. E ho genuinamente imparato ad amarla.

Compilare tutto da sorgente con **Portage** e le **USE flag** mi dava un livello di controllo che nessuna distro binaria può offrire. Sapevo cosa faceva ogni pacchetto sul mio sistema perché ne avevo scelto esplicitamente le funzionalità. Il `make.conf`, le USE flag per pacchetto, la config del kernel — era tutto mio.

Tenevo la configurazione di Portage versionata su git. `/etc/portage/make.conf`, `package.use/`, `package.accept_keywords/` — tutto tracciato. Sembrava la cosa giusta da fare.

### Ma è diventato pesante

Col tempo, gli attriti si sono accumulati:

- **I conflitti di dipendenze erano dolorosi** — risolvere dipendenze circolari o pacchetti bloccati era un pozzo di tempo. La risoluzione dei conflitti di Portage è potente, ma quando fallisce ti ritrovi a leggere slot conflict per un'ora
- **Le USE flag che remano contro** — ci sono stati casi in cui avevo bisogno di un pacchetto specifico, ma certe USE flag mi impedivano di usare le funzionalità che mi servivano davvero. Cambi una flag e tre pacchetti si rompono
- **Ricompilazioni lunghe** — `emerge -uDN @world` dopo un cambio di USE flag poteva significare ore di compilazione
- **La configurazione era pesante** — anche con le config tracciate su git, mantenere il sistema era un impegno costante

Non fraintendetemi — Gentoo è una distro incredibile. Mi ha insegnato più di qualsiasi altra cosa sugli internals di Linux. Ma come daily driver, il costo di manutenzione è diventato troppo alto.

---

## NixOS — dichiarativo e riproducibile

Dopo Gentoo, ho provato NixOS. E non torno indietro.

L'idea di base è semplice: l'intero sistema è descritto in file di configurazione. Pacchetti, servizi, utenti, moduli del kernel, regole del firewall — tutto. Dichiari lo stato desiderato, esegui `nixos-rebuild switch`, e Nix lo realizza.

### Cosa è cambiato per me

- **La configuration drift è sparita** — il mio sistema *è* la mia configurazione. Se non è nei file Nix, non esiste sulla macchina
- **Single source of truth** — so esattamente cosa è installato e come è configurato. È quasi Infrastructure as Code, ma per il desktop
- **Rollback atomici** — ogni rebuild crea una nuova generazione. Se qualcosa si rompe, faccio boot sulla precedente. Niente più "ho cambiato qualcosa e ora X non funziona e non mi ricordo cosa ho fatto"
- **Nessun dependency hell** — Nix isola ogni pacchetto con il suo albero di dipendenze. Due pacchetti possono dipendere da versioni diverse della stessa libreria senza conflitti

### Flakes

Uso i **Nix flakes** per tutta la mia configurazione. I flakes portano lockfile e riproducibilità — il mio sistema si compila sempre allo stesso modo, pinnato a commit esatti di nixpkgs.

### La mia config

La mia configurazione NixOS completa è pubblica: [github.com/MishowHD/mishow-nix](https://github.com/MishowHD/mishow-nix)

Copre il mio setup desktop e, come scriverò in un post separato, anche il mio server.

---

## Il takeaway

Ogni distro mi ha insegnato qualcosa:

- **Arch** mi ha insegnato come funziona Linux
- **Gentoo** mi ha insegnato come viene *costruito* Linux
- **NixOS** mi ha insegnato come Linux andrebbe *gestito*

Ho ancora profondo rispetto per Gentoo. Ma per un sistema su cui faccio affidamento ogni giorno, il modello dichiarativo vince. Nessun drift, nessuna sorpresa, riproducibilità totale — è questo che voglio dal mio OS.
