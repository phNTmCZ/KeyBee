---
title: Evilginx / Gophish AiTM phishing infrastructure — authorized red-team reference
tags: [evilginx, gophish, evilgophish, aitm, phishing-infra, red-team, phishlets, opsec, mfa-bypass, detection]
source: |
  Merged from 5 sources (all public):
  (1) An0nUD4Y "Evilginx-Phishing-Infra-Setup" — GitHub repo notes (securing infra, removing IOCs, phishing TTPs)
  (2) An0nUD4Y infra architecture diagram (GitDiagram render of the repo)
  (3) "Evilginx & Gophish: How hackers launch phishing campaigns" — Outpost24 blog, 2025-11-10
  (4) "Setting Up EvilGoPhish for Realistic Phishing Campaign Simulations" – Razzle Mouse (Raguraman), Medium, 2026-01-06
  (5) "No Password. No MFA Prompt. Just a Stolen Cookie: A Hands-On AiTM Phishing Walkthrough with Evilginx" – Dion Alexander, Medium, 2026-05-14
captured: 2026-11-21
---

# Evilginx / Gophish AiTM phishing infrastructure

> **Scope.** Reference for **authorized** red-team / phishing-simulation engagements only (signed SoW, approved internal exercise, self-owned lab, or in-scope bug bounty). AiTM credential/session capture against systems or people you are not explicitly authorized to test is a crime (CFAA in the US; equivalent laws elsewhere). Every source here states the same. The high-value half of this note is the **detection & defense** section — understanding the tradecraft is what feeds detection engineering.

Merged and deduplicated from 5 public sources. Tool versions/CLI checked against upstream (kgretzky/evilginx2, fin3ss3g0d/evilgophish) and marked **(upstream)**; anything unmarked is from the articles.

---

## 1. The tools

| Tool | Role | Repo |
|---|---|---|
| **Evilginx** (Evilginx2/3) | Transparent **reverse-proxy AiTM** framework by Kuba Gretzky. Sits between victim and the *real* site, relays traffic, and captures credentials **and session cookies/tokens** as they pass — so it defeats non-phishing-resistant MFA (the stolen session cookie is replayed to walk into the authenticated account). Not a cloned login page — it *is* the real site, relayed. | `github.com/kgretzky/evilginx2` |
| **Gophish** | Open-source campaign manager: build/send phishing emails, landing pages, track opens/clicks/submits. Credential-harvesting + awareness training. Does **not** capture session tokens on its own. | `github.com/gophish/gophish` |
| **EvilGoPhish** | Combines **evilginx3 + Gophish** (by fin3ss3g0d): Gophish handles sending + campaign stats/dashboard, evilginx3 handles the AiTM proxy and landing pages. Adds SMS (Twilio) campaigns, Cloudflare Turnstile, operator-chosen RID. | `github.com/fin3ss3g0d/evilgophish` |

**(upstream) Version notes:**
- Current Evilginx is **v3.3.0**; **v3.0+ removed built-in phishlets** and became a framework where you build phishlets per target. **v3.3 added official Gophish integration** using the `kgretzky/gophish` fork.
- EvilGoPhish's **public GitHub version lags the author's sponsor build** ("IS NOT THE LATEST VERSION") — expect missing fixes/features vs. the sponsor release.

---

## 2. Architecture (layered, defense-in-depth)

From the An0nUD4Y GitDiagram — tiers: **Provisioning → Edge Proxy → Phishing Services → Delivery & Data Capture.**

```
Provisioning:   Attacker workstation ──(DNS)──▶ Domain registrar/DNS provider
                                     ──(reputation check)──▶ Categorization APIs (Bluecoat, FortiGuard, Palo Alto…)
                                     ──(cert request)──▶ Let's Encrypt (wildcard cert)
Edge proxy:     Cloudflare (DNS proxy / WAF / TLS terminate) ──▶ Nginx reverse proxy
                (nginx hides Go server fingerprint, enforces UA rules)
Phishing svcs:  Evilginx2/3  +  Gophish (SQLite3)
Delivery/data:  SMTP relay (SendGrid/Mailgun/AWS SES/M365) ──▶ Target inbox
                Sessions/creds ──▶ SQLite DBs  ──▶ Analyst workstation (campaign metrics)
```

**Traffic flow:** Gophish → SMTP relay → target inbox. Target clicks lure → Cloudflare → Nginx → Evilginx → real upstream site. Evilginx captures creds + session cookies. Gophish GUI/API shows campaign stats.

**Design principles:** separate domain mgmt / TLS / proxy / app logic; modular phishlets (one self-contained proxy rule per target site); Gophish + Evilginx as distinct services behind nginx; Infrastructure-as-Code to reduce OPSEC leaks.

---

## 3. Core concepts

- **Phishlet** — YAML config telling Evilginx how to proxy a target: which subdomains/hosts to proxy (`proxy_hosts`), which cookies prove a successful login (`auth_tokens`), which POST fields are creds (`creds`, regex), and the login entrypoint. One per target app. `session: true` on a proxy_host is what makes Evilginx capture cookies there.
- **Lure** — the actual phishing URL (enabled phishlet + configured hostname + unique path). This is what's delivered. Default lure path is a random 8-char string.
- **Session capture** — once the victim completes real auth (incl. MFA), Evilginx holds the validated session cookies; replaying them in a clean browser against the real origin lands in the authenticated account with **no password and no MFA prompt**. Self-hosted apps use per-install cookie names, so custom phishlets use regex `auth_tokens` (e.g. WordPress `wordpress_sec_.*`, `wordpress_logged_in_.*`).
- **Scope vs. scale** — Evilginx is precision tooling (spear-phishing high-value targets, lures sent one at a time); it has no campaign dashboard of its own. Gophish/EvilGoPhish add the campaign-management layer.

---

## 4. Setup workflow (common path across sources)

1. **Host** — cloud VPS with public IP (AWS EC2 / DigitalOcean / Vultr / Linode). Key-based SSH only, restrict SSH to your IP, open 80/443 (Evilginx needs them for serving + Let's Encrypt).
2. **Domain** — register (Namecheap/Hostinger…), or buy an aged/expired high-reputation domain (expireddomains.net). Check categorization before use. A real `.com` beats a cheap lookalike TLD for trust.
3. **DNS** — A record `@` and wildcard `*` → VPS IP; add phishlet subdomain A records. Short TTL while setting up.
4. **Email delivery** — SMTP via a reputable provider (SendGrid/Mailgun/AWS SES, or Google Workspace / M365 — mail from trusted infra bypasses the first filter layer). Set valid **SPF, DKIM, DMARC, MX**. "From Google/Workspace infra" is *not* a trust signal — only sender-domain reputation and content are.
5. **TLS** — wildcard cert via Let's Encrypt (acme.sh or certbot, DNS-01 challenge), covering apex + subdomains. Evilginx auto-provisions Let's Encrypt certs on `phishlets enable`.
6. **Install tools** — clone/build Evilginx (Go); for EvilGoPhish: `git clone github.com/fin3ss3g0d/evilgophish`, `chmod +x setup.sh`, run it.
   - **(upstream)** EvilGoPhish `setup.sh` canonical form has varied by version; current docs show `./setup.sh <root_domain> "<evilginx_subdomains>" <feed_bool> <turnstile_bool> <rid_key>` (the article (4) showed `sudo ./setup.sh domain.com "accounts" false true keyname` — confirm arg order against the README of the version you install).
7. **Evilginx start** — `./evilginx3 -p <phishlets_dir> -g <path>/gophish.db` (the `-g` flag wires Evilginx to the Gophish DB; `-feed` enables live feed; `-t` redirectors dir). **(upstream)** startup flags: `-p/--phishlets`, `-t/--redirectors`, `-g` gophish db, `-feed`, `-turnstile <pub>:<priv>`.
8. **Phishlet + lure** (Evilginx console):
   ```
   phishlets hostname <phishlet> <sub.yourdomain.com>
   phishlets enable <phishlet>        # triggers Let's Encrypt cert fetch
   lures create <phishlet>
   lures get-url <id>                 # the link to deliver
   ```
   **(upstream)** these commands are current in v3.x. `lures get-url <id> param=value` embeds encrypted custom params in the URL.
9. **Gophish integration** — launch the campaign from Gophish, set the landing/URL to the Evilginx lure path so Gophish tracking (opens/clicks) and Evilginx session capture both work. Gophish admin server is bound to localhost / non-default port and never exposed; a reverse proxy (Apache/nginx) fronts 443.

---

## 5. OPSEC / IOC-removal index (from source 1 — authorized engagements)

This is a **categorized index** of the hardening areas the An0nUD4Y notes cover; the exact code edits are in that public repo. Keep it as a pre-engagement checklist. The flip side of each is a **detection opportunity** (see §6).

- **Evilginx IOCs** — remove the `X-Evilginx` header (comment the `req.Header.Set` calls in `core/http_proxy.go`); remove default cert details; modify the unauth-redirect static HTML; obfuscate/minify the injected JS so it isn't statically signatured; change the default 8-char lure path pattern; modify lure/session cookie name+value pattern (default: name `XXXX-XXXX`, value 64 hex chars — a known Push Security detection rule).
- **Gophish IOCs** — remove `X-Gophish-Contact` / `X-Gophish-Signature` headers (`sed` rename); `ServerName` `gophish` → `IGNORE` in `config/config.go`; rename the `rid` GET parameter everywhere (also in evilginx3.3 source); custom 404 + robots.txt; strip test-email signatures; change the self-signed cert org string (e.g. to "Microsoft Corporation"); front with nginx/Postfix to strip Go fingerprint + fix mail headers.
- **Edge / network** — Cloudflare in front (SSL mode Full); nginx reverse proxy with **User-Agent filtering** (block `bot`/`Bot`, or allow-list a single UA); TLS fingerprint evasion (JA3/JA3S/JA4 — utls / proxy); Referrer-Policy `no-referrer`; custom CSP to stop telemetry/canary leaking your domain.
- **Delivery / reputation** — aged domain, correct categorization, valid SPF/DKIM/DMARC (check with Mailgoose), benign warm-up email, `mail-tester.com` for spamminess.
- **Redirectors / bot filtering** — Cloudflare Turnstile as redirector; obfuscated HTML/JS redirectors; UA/ASN blacklists; Evilginx auto-blacklists IPs hitting non-lure paths (scanners hitting `.env`, `.git`, `wp-admin`).
- **Advanced** — URL-path rewriting (`rewrite_urls` — Evilginx **Pro only**, implementable in public); BITB + frame-busting bypass (OktaGinx subfilters); canary-token (CSS/JS) avoidance.

---

## 6. Detection & defense (the high-value part)

### What actually breaks the attack chain (from source 5)
| Attack link | Control that breaks it |
|---|---|
| User enters creds on the proxy | **Phishing-resistant MFA (FIDO2 / WebAuthn)** — binds the credential cryptographically to the real origin, so the browser refuses to release it to the Evilginx domain. The *only* control that stops it at the login step. TOTP / SMS / push do **not** — they authenticate the user, not the origin, and get proxied. |
| Attacker captures the session cookie | **Impossible-travel** alerts on the identity platform (same session appears from two distant locations). |
| Attacker replays the cookie from new infra | **Conditional access** that re-challenges on risky signals (new device/country/anonymizer IP). In M365/Entra this is where the cloud version gets caught. |

Key principle: **the session cookie IS the authentication once stolen** — detection cannot live at the login form, it has to live at the **session layer** (cookie-reuse anomaly detection, conditional access on session properties, FIDO2).

### Server/infra detection (from sources 1 & 6-era research)
- **TLS fingerprinting** — JA3/JA3S/**JA4** can flag the proxy (ja4db.com).
- **IOC headers** — default `X-Evilginx`, `X-Gophish-*` headers; default cert org strings; default lure/cookie patterns (Push Security's Chrome extension rules: cookie name `XXXX-XXXX` + 64-hex value; `/s/<64hex>.js` with content-length 0).
- **Login-page / server fingerprinting** — EvilGoPhish/Gophish servers are identifiable via login pages and Go server fingerprints (hunt.io research on EvilGoPhish; cybercx "identifying Gophish servers").
- **Honeytokens** — AiTM honeytokens on an M365 tenant (zolder.io) to detect proxying.
- **Canary tokens** — CSS/JS canaries that fire when a page is served through a proxy (thinkst; spotit canary research).

### Defensive posture summary
Identity is the perimeter. Deploy FIDO2/WebAuthn for high-value accounts, conditional access with risk-based re-challenge, session-anomaly/impossible-travel detection, and user awareness — but assume awareness alone fails past a certain attacker skill level. A full real-world campaign also adds OSINT, crafted pretexts, and infra rotation (out of scope for the lab sources here).

---

## 7. Key reference links (from the sources)
- Evilginx docs: help.evilginx.com · Mastery course: academy.breakdev.org/evilginx-mastery
- An0nUD4Y phishlets: github.com/An0nUD4Y/Evilginx2-Phishlets (+ "securing infra tips")
- EvilGoPhish: github.com/fin3ss3g0d/evilgophish
- Defense research: catching-transparent-phish (JA-fingerprint), spotit "clipping the canary's wings", zolder.io AiTM honeytokens, hunt.io EvilGoPhish infra analysis
