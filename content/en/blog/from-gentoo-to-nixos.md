+++
title = "From Gentoo to NixOS — My Desktop Journey"
date = "2026-07-28"
description = "How I went from Arch to Gentoo to NixOS on my desktop — what I loved, what broke, and why declarative won."
tags = ["linux", "gentoo", "nixos", "declarative"]
draft = false
+++

I've been distro-hopping with intent for a while now. Not for fun — each switch taught me something fundamentally different about Linux. This is the story of how I went from Arch to Gentoo to NixOS on my main workstation.

---

## Arch — a solid year as my daily driver

I've been using Linux for a long time across various distributions, so I was already quite experienced when I moved to Arch Linux. Arch became my main operating system for over a year. Rolling release, minimal base install, and the wiki made it a solid daily driver. It taught me how to construct and maintain a system from the ground up. But after more than a year of running it daily, I wanted even deeper control — not just over what packages were installed, but over *how* they were compiled and configured.

---

## Gentoo — learning to love the compile

I moved to Gentoo because I wanted to try a source-based distro. And I genuinely learned to love it.

Compiling everything from source with **Portage** and **USE flags** gave me a level of control that no binary distro can match. I understood what every package on my system was doing because I explicitly chose its features. The `make.conf`, the per-package USE flags, the kernel config — it was all mine.

To mitigate compile times, I also set up and used a **binhost** (pre-compiled binary package repository). I really liked this setup as it drastically improved compilation times for heavy updates while still giving me custom compilation control where needed.

I kept my Portage configuration versioned in git. `/etc/portage/make.conf`, `package.use/`, `package.accept_keywords/` — everything tracked. It felt like the right thing to do.

### But it got heavy

Over time, the friction added up:

- **Dependency conflicts were painful** — resolving circular dependencies or blocked packages was a time sink. Portage's conflict resolution is powerful, but when it fails you're left reading slot conflicts for an hour
- **USE flags fighting back** — there were cases where I needed a specific package, but certain USE flags prevented me from using the features I actually needed. You'd toggle one flag and three other packages would break
- **Long rebuilds** — `emerge -uDN @world` after a USE flag change could mean hours of recompilation
- **Configuration was heavy** — even with git-tracked configs, maintaining the system was a constant effort

Don't get me wrong — Gentoo is an incredible distro. The experience and everything I learned from Gentoo are absolute gold: it taught me more about Linux internals than anything else. But for a daily driver, the maintenance cost became too high.

---

## NixOS — declarative and reproducible

After Gentoo, I tried NixOS. And I'm not going back.

The core idea is simple: your entire system is described in configuration files. Packages, services, users, kernel modules, firewall rules — everything. You declare the desired state, run `nixos-rebuild switch`, and Nix makes it happen.

### What changed for me

- **Configuration drift is gone** — my system *is* my config. If it's not in the Nix files, it doesn't exist on the machine
- **Single source of truth** — I know exactly what's installed and how it's configured. It's almost like Infrastructure as Code, but for your desktop
- **Atomic rollbacks** — every rebuild creates a new generation. If something breaks, I boot the previous one. No more "I changed something and now X doesn't work and I don't remember what I did"
- **No dependency hell** — Nix isolates every package with its own dependency tree. Two packages can depend on different versions of the same library without conflict

### Flakes

I use **Nix flakes** for my entire config. Flakes bring lockfiles and reproducibility — my system builds the same way every time, pinned to exact commits of nixpkgs.

### My config

My full NixOS configuration is public: [github.com/MishowHD/mishow-nix](https://github.com/MishowHD/mishow-nix)

It covers my desktop setup and, as I'll write about separately, my server too.

---

## The takeaway

Each distro taught me something:

- **Arch** taught me how Linux works
- **Gentoo** taught me how Linux is *built*
- **NixOS** taught me how Linux should be *managed*

I still have deep respect for Gentoo. But for a system I rely on every day, the declarative model wins. No drift, no surprises, full reproducibility — that's what I want from my OS.
