# pihole-allowlists

Curated Pi-hole **allowlists** (v6 terminology) for services that aggressive blocklists
(HaGeZi Pro, OISD, TIF) tend to break. One file per service, so you can host them here and
add only what each device actually needs.

> Most streaming/app breakage is a blocked **analytics/measurement** domain, not the video
> CDN. Apps run a verification chain at launch and refuse to play if those domains fail.

## Two ways to use a file

**A. Remote allowlist (auto-updating).** Pi-hole admin -> **Lists** -> add the file's *raw*
URL and set the type to **Allow**, e.g.

```
https://raw.githubusercontent.com/KeysAU/pihole-allowlists/main/allowlists/10play.txt
```

Then run gravity to pull it: `pihole -g`

**B. Copy-paste.** Open the file, copy the domains into **Domains** -> add as **Exact allow**.

## Scope with Groups (recommended for the streaming files)

Allowlisting is **global** by default. The streaming files contain ad/measurement domains
(doubleclick, googlesyndication, etc.) - allowing them unblocks them for *every* device.
Put your TV / streaming box in its own **Group** and attach those lists to that group only,
so the ad domains stay blocked for phones and browsers.

## Regex entries are separate

Hosted lists only parse **exact domains**. Wildcard / regex entries live in
`regex/regex-allow.txt` and must be added manually (**Domains -> Regex allow**, or
`pihole allow-regex 'PATTERN'`). They cannot be pulled from a URL.

## Files

### Australian catch-up TV
| File | Notes |
|---|---|
| `allowlists/au-streaming-shared.txt` | **Add first** - shared backbone for all AU catch-up apps |
| `allowlists/10play.txt` | Network Ten (requires shared) |
| `allowlists/abc-iview.txt` | ABC iView (requires shared + `abc.net.au` regex) |
| `allowlists/sbs-on-demand.txt` | SBS (requires shared + `sbs.com.au` regex) |

### Apple
| File | Notes |
|---|---|
| `allowlists/apple-private-relay.txt` | **Read the warning in the file** - allowing = those devices bypass Pi-hole |
| `allowlists/apple-services.txt` | App Store, Music, ID, Weather, captive check |

### Everyday apps
`google.txt`, `microsoft.txt`, `spotify.txt`, `plex.txt`, `sonarr.txt`, `dropbox.txt`,
`signal.txt`, `canon-printers.txt`, `whatsapp.txt`, `twitter-x.txt`

## Maintenance

Service domains rotate. When something breaks, watch the **Query Log** on the affected
client while you reproduce the problem, then append the newly-blocked domain to the right
file and commit.

## Sources

Domains compiled from the Pi-hole community "Commonly Whitelisted Domains" wiki
(https://discourse.pi-hole.net/t/commonly-whitelisted-domains/212) and a 2026 AU-streaming
allowlist, reorganised into per-service files. Allow entries only override blocklists; they
don't open anything else up. Verify against your own query log - lists drift.
