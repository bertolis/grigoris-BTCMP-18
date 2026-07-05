# Test-Repo

CyberRangeCZ sandbox definition for the CADMUS-BTCMP-18 OSINT Bootcamp.

## Topology

- **vm1** — analyst workstation (`xubuntu-noble`, browsers + OSINT tooling), LAN `192.168.10.10`
- **vm2** — web server (`ubuntu-noble`, Apache + PHP), hosts the lab websites, LAN `192.168.10.20`
- **router** — LAN gateway `192.168.10.1`
- **lan** — `192.168.10.0/24`

## Module 4 websites on VM2

The Module 4 (Social Media Intelligence) challenge sites are deployed to VM2's
Apache document root during provisioning. Source files live in
`provisioning/files/website/` (a copy of `../../module4/challenge/`).

From vm1, the lab is reachable at:

| URL | Site |
|---|---|
| `http://192.168.10.20/` | 403 Forbidden (no landing page, no listing) |
| `http://192.168.10.20/chirp/` | Chirp (social platform) |
| `http://192.168.10.20/agora/` | Agora (forum) |
| `http://192.168.10.20/aif2024/` | Athens Infosec Forum 2024 event site |

The sites are reachable **only by path**. There is no index page at the web
root, and Apache directory listing is disabled (`Options -Indexes`), so hitting
the bare IP returns 403 and does not reveal the available sites.

The mission briefing and 10 questions are **not** served on VM2 — they are
delivered separately (e.g. via the lab portal).

All cross-platform links are relative, so the single docroot serves the whole
challenge tree. To update the sites after editing the source, re-sync (note the
`/index.html` exclude so the source briefing page is never deployed):

```bash
rsync -a --exclude='.DS_Store' --exclude='._*' --exclude='/index.html' \
  ../module4/challenge/ provisioning/files/website/
```

then re-run provisioning.

## Module 8 capstone website on VM2

The Module 8 (Capstone Investigation) dossier is deployed as a path-based subsite under the
same docroot. Source: `provisioning/files/website/case/` (a copy of `../../module8/challenge/`).

From vm1, reachable at:

| URL | Page |
|---|---|
| `http://192.168.10.20/case/` | Capstone dossier — mission briefing + evidence (entry point) |
| `http://192.168.10.20/case/exhibits/…` | Exhibit pages (username results, LinkedOut, GitHub, Chirp, search, red-herring profile) |
| `http://192.168.10.20/case/files/…` | Downloadable artifacts (`application.eml`, profile photo, office photo, CV) |

Unlike Module 4, the Module 8 briefing **is** served (at `/case/index.html`) — the bare IP still
returns 403 because there is no root index page. All links are relative, so it works under the
`/case/` path unchanged. To update after editing the source (keep `index.html`, it is the entry):

```bash
rsync -a --delete --exclude='.DS_Store' --exclude='._*' \
  ../module8/challenge/ provisioning/files/website/case/
```

then re-run provisioning.

> Note: reverse image search of `/case/files/daniel_vos_profile.jpg` requires internet access
> from vm1 (as in Module 5). Confirm the range provides it.
