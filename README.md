# Pi-hole Allowlist

![Pi-hole v6](https://img.shields.io/badge/Pi--hole-v6-96060c)
![Lists](https://img.shields.io/badge/lists-25-2496ED)
![Region](https://img.shields.io/badge/region-Australia-success)

A curated set of Pi-hole **allowlists** — one file per service — for the things that
aggressive blocklists (HaGeZi Pro, OISD, TIF) tend to break: Australian catch-up TV,
Apple Private Relay, streaming, social, gaming, and common everyday apps.

Most "the app won't load" problems on a filtered network aren't the video CDN being
blocked — they're a blocked **analytics or measurement** domain. Streaming and mobile apps
run a verification chain at launch and refuse to play if those domains fail to resolve.
These lists allow the minimum needed to keep each service working.

> **Allowlists only override blocklists.** They never open anything you haven't already
> blocked, so adding a list you don't strictly need is harmless.

---

## Contents

- [Quick start](#quick-start)
- [How it works](#how-it-works)
- [Scope with Groups](#scope-with-groups)
- [Regex entries (manual)](#regex-entries-manual)
- [Repository layout](#repository-layout)
- [File reference](#file-reference)
- [Maintenance](#maintenance)
- [Sources](#sources)

---

## Quick start

In the Pi-hole admin UI, go to **Lists**. Under *Add a new list*, paste the whole block
below into the **Address** field (v6 accepts multiple URLs at once), set **Type** to
**Allow**, add a comment such as `KeysAU allowlist`, and click **Add**.

```text
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/au-streaming-shared.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/10play.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/abc-iview.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/sbs-on-demand.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/netflix.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/plex.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/spotify.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/sonarr.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/apple-private-relay.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/apple-services.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/facebook.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/reddit.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/signal.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/twitter-x.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/whatsapp.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/xbox-live.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/origin.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/gta-online.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/epic-games.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/nvidia.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/google.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/microsoft.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/dropbox.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/canon-printers.txt
https://raw.githubusercontent.com/KeysAU/pihole-allowlist/main/allowlists/aliexpress.txt
```

Then apply them — lists don't take effect until gravity runs:

```bash
pihole -g
```

> Prefer the GUI? **Tools → Update Gravity** does the same thing.

---

## How it works

Each file is a plain, newline-delimited list of exact domains with a `#` comment header
that Pi-hole ignores on parse. There are two ways to consume them.

**Remote allowlist — recommended, auto-updating.**
Add the raw URL on the **Lists** page with type **Allow** (see [Quick start](#quick-start)).
Gravity re-pulls on its schedule, so any change pushed to this repo flows to your Pi-hole
automatically.

**Copy-paste — one-off.**
Open a file, copy the domains, and add them under **Domains → Add → Exact allow**. Use this
if you only want a handful and don't want a live dependency on the repo.

---

## Scope with Groups

Allowlisting is **global by default**. Some files contain ad/measurement domains
(`doubleclick`, `googlesyndication`, telemetry hosts, and similar) — allowing them
network-wide unblocks them for *every* device, not just the one that needs them. To keep
them contained:

1. **Groups** — create a group, e.g. `lounge-tv`.
2. **Clients** — add your TV / console / device and assign it to that group.
3. **Lists** — assign the relevant allowlists to that group only.

The ad/telemetry domains then stay blocked on phones and browsers while the device works.

---

## Regex entries (manual)

Pi-hole only parses **exact domains** from a remote list — regex / wildcard entries cannot
be served from a URL. The patterns below live in
[`regex/regex-allow.txt`](regex/regex-allow.txt) and must be added by hand under
**Domains → Add → Regex allow** (or via `pihole allow-regex 'PATTERN'`):

```text
(\.|^)sbs\.com\.au$
(\.|^)abc\.net\.au$
^((alt)[0-9](-))?mtalk\.google\.com$
(\.|^)push\.apple\.com$
^whatsapp-cdn-shv-[0-9]{2}-[a-z]{3}[0-9]\.fbcdn\.net$
^((www|(w[0-9]\.)?web|media((-[a-z]{3}|\.[a-z]{4})[0-9]{1,2}-[0-9](\.|-)(cdn|fna))?)\.)?whatsapp\.(com|net)$
(\.|^)twimg\.com$
[a-z]\.thumbs\.redditmedia\.com
(\.|^)redd\.it$
(\.|^)reddit\.com$
```

---

## Repository layout

```text
pihole-allowlist/
├── README.md
├── allowlists/            # exact-domain lists — safe to add as remote Allow lists
│   └── *.txt              # 25 per-service files
└── regex/
    └── regex-allow.txt    # add manually (Domains → Regex allow)
```

---

## File reference

### Australian catch-up TV

| List | Add first? | Notes |
|------|:----------:|-------|
| [`au-streaming-shared.txt`](allowlists/au-streaming-shared.txt) | **Yes** | Shared backbone (Google IMA/DAI, OzTAM, Conviva, Brightcove) for **all** AU catch-up apps |
| [`10play.txt`](allowlists/10play.txt) | – | Network Ten · requires the shared list |
| [`abc-iview.txt`](allowlists/abc-iview.txt) | – | ABC iView · requires shared list + `abc.net.au` regex |
| [`sbs-on-demand.txt`](allowlists/sbs-on-demand.txt) | – | SBS · covered by shared list + `sbs.com.au` regex |

### Streaming & media

| List | Notes |
|------|-------|
| [`netflix.txt`](allowlists/netflix.txt) | Core API / boot / secure endpoints |
| [`plex.txt`](allowlists/plex.txt) | Plex media server + metadata providers |
| [`spotify.txt`](allowlists/spotify.txt) | iOS + TV app endpoints |
| [`sonarr.txt`](allowlists/sonarr.txt) | Sonarr services |

### Apple

| List | Notes |
|------|-------|
| [`apple-private-relay.txt`](allowlists/apple-private-relay.txt) | **Warning:** allowing this makes Apple devices bypass Pi-hole entirely — add only if you want Private Relay to work |
| [`apple-services.txt`](allowlists/apple-services.txt) | App Store, Music, Apple ID, iOS Weather, captive-portal check |

### Social & messaging

| List | Notes |
|------|-------|
| [`facebook.txt`](allowlists/facebook.txt) | Facebook + Messenger (graph, CDN, MQTT chat) |
| [`reddit.txt`](allowlists/reddit.txt) | Media / static / thumbnails (+ `redd.it` / `reddit.com` regex) |
| [`signal.txt`](allowlists/signal.txt) | Messaging, attachments, CDN |
| [`twitter-x.txt`](allowlists/twitter-x.txt) | `twitter.com` + `x.com` (media via `twimg.com` regex) |
| [`whatsapp.txt`](allowlists/whatsapp.txt) | `wa.me` links (most coverage is via regex) |

### Gaming

| List | Notes |
|------|-------|
| [`xbox-live.txt`](allowlists/xbox-live.txt) | Sign-in, achievements, messaging, Store (Series X/S), EA Play |
| [`origin.txt`](allowlists/origin.txt) | EA / Origin cloud savegame sync |
| [`gta-online.txt`](allowlists/gta-online.txt) | Rockstar telemetry — **blocking it crashes GTA Online** |
| [`epic-games.txt`](allowlists/epic-games.txt) | Store purchases + launcher 2FA login |
| [`nvidia.txt`](allowlists/nvidia.txt) | GeForce Experience + driver / OTA updates |

### System & apps

| List | Notes |
|------|-------|
| [`google.txt`](allowlists/google.txt) | Maps, YouTube, Play, Fonts, Gmail, Android TV, push, **connectivity check** (fixes Android/Chromecast "no internet") |
| [`microsoft.txt`](allowlists/microsoft.txt) | Connectivity, Update, Store, Edge, Office, Skype, Defender, Bing Maps |
| [`dropbox.txt`](allowlists/dropbox.txt) | File downloads |
| [`canon-printers.txt`](allowlists/canon-printers.txt) | Firmware updates |
| [`aliexpress.txt`](allowlists/aliexpress.txt) | Product image CDN |

---

## Maintenance

Service domains rotate over time. When something breaks:

1. Pi-hole admin → **Query Log**, and filter by the affected client (the device IP).
2. Reproduce the problem and look for **Blocked** entries timestamped to that moment.
3. Add the offending domain to the relevant file and commit — gravity picks it up on the
   next update.

> Tip: `pihole disable 10m` pauses blocking while you confirm what an app actually needs.

---

## Sources

Domains compiled from the Pi-hole community
[*Commonly Whitelisted Domains*](https://discourse.pi-hole.net/t/commonly-whitelisted-domains/212)
wiki and a 2026 Australian-streaming allowlist, reorganised into per-service files and
validated against live query logs. Verify against your own setup — lists drift over time.
