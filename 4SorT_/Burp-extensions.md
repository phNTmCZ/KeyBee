---
title: Burp Suite – 12 essential BApp extensions
tags: (burp-suite, bapp-store, web-pentest, authz, idor, jwt, graphql, race-condition, oast, request-smuggling)
Medium: "Burp Suite on Steroids: 12 Extensions That Feel Like Cheating" by Devansh Patel, MeetCyber (Medium), 2026-09-21
captured: 2026-11-20
---

# Burp Suite – 12 essential BApp extensions

Condensed from the article above. Snippets marked **(upstream)** were checked against the project's GitHub repository; everything else is taken from the article.

**Core idea:** extensions produce *signals*, not findings. Run them in a fixed order (map → expand → break auth → hit hard → go blind) and prove impact manually before reporting anything.

**Minimum set if installing only three:** Autorize, Param Miner, Logger++.

---

## 1. Install

1. Burp → **Extensions** tab → **BApp Store**
2. Search for the name → **Install**
3. Verify under **Extensions → Installed**

- Java extensions work without extra setup.
- Older Python-based extensions need Jython: download the `jython-standalone` JAR and set it once under **Extensions → Extensions settings → Python environment**.
- The BApp Store has 300+ extensions from 250+ authors (article's figure).

---

## 2. Pick by job

| Job | Extension |
|---|---|
| Broken access control | Autorize, AuthMatrix |
| Tokens & sessions | JWT Editor |
| Hidden attack surface | Param Miner, JS Miner |
| GraphQL / APIs | InQL |
| Filters & WAFs | Hackvertor, 403 Bypasser |
| Speed & race conditions | Turbo Intruder |
| Blind / out-of-band | Collaborator Everywhere |

---

## 3. Order of operations

| Step | Phase | Extensions | Rationale |
|---|---|---|---|
| 1 | Map | Logger++, JS Miner, Retire.js | Everything after depends on knowing the surface |
| 2 | Expand | Param Miner, InQL | Find hidden params, headers, API operations |
| 3 | Break auth | Autorize, JWT Editor | Highest severity lives here |
| 4 | Hit hard | Hackvertor, Turbo Intruder | Encoding / filter bypass, high-volume and race testing |
| 5 | Go blind | Collaborator Everywhere, HTTP Request Smuggler | Noisy, so last |

---

## 4. The 12 extensions

| # | Extension | Phase | Edition (per article) |
|---|---|---|---|
| 1 | Logger++ | Map | Community + Pro |
| 2 | Param Miner | Expand | Community + Pro |
| 3 | JS Miner | Map | not stated |
| 4 | Retire.js | Map | not stated |
| 5 | Autorize | Break auth | not stated |
| 6 | JWT Editor | Break auth | Community + Pro |
| 7 | InQL | Expand | Community + Pro |
| 8 | Hackvertor | Hit hard | not stated |
| 9 | Turbo Intruder | Hit hard | Community + Pro |
| 10 | Collaborator Everywhere | Go blind | **Pro** |
| 11 | Active Scan++ (+ Backslash Powered Scanner) | Scan | **Pro** |
| 12 | HTTP Request Smuggler | Go blind | **Pro** |

### 1. Logger++
- One sortable, filterable, exportable table for all traffic: Proxy, Repeater, Scanner and other extensions. Burp's built-in history is per tool and hard to search.
- **Move:** filter for responses containing `error` or status `500`; stack traces leak useful information. Filter syntax in [Snippets](#logger-filters).

### 2. Param Miner
- Guesses parameter names and headers the app accepts but never exposes. Can send tens of thousands of guesses for a single request and watch for behaviour changes.
- **Move:** run **Guess headers** as well as params. This is how web cache poisoning gets found.

### 3. JS Miner
- Extracts endpoints, subdomains, API keys and source-map leftovers from JavaScript bundles.
- **Move:** run it right after the first crawl, before any manual testing.

### 4. Retire.js
- Passive. Flags JavaScript libraries with known CVEs while you browse; no extra traffic.
- **Move:** never report the CVE alone. Prove the vulnerable function is reachable, otherwise it is noise.

### 5. Autorize
- Rated the single most valuable extension on the list.
- Workflow: log in as a **low-privilege** user → paste that session into Autorize → browse as **admin**. Every request is silently replayed with the weak session and Autorize reports which ones still succeeded.
- **Move:** also enable the **unauthenticated** check. Finds broken access control (OWASP #1) while you do other work.
- Related: AuthMatrix.

### 6. JWT Editor
- Decode, edit, re-sign and attack JSON Web Tokens directly in Repeater.
- Covers: `alg: none`, algorithm confusion, weak keys, embedded JWK injection.
- **Move:** before anything advanced, change the role or user ID claim and resend. Works more often than expected.

### 7. InQL
- Pulls the full GraphQL schema and generates every query and mutation, ready to send to Repeater.
- **Move:** skip queries first and go straight to **mutations**; authorization checks are most often missing there.

### 8. Hackvertor
- Tag-based encoder/decoder that converts payloads live inside the request. Syntax in [Snippets](#hackvertor-tags).
- **Move:** chain tags to beat filters. Double encoding and unicode escaping are still effective.

### 9. Turbo Intruder
- Written by James Kettle (PortSwigger). Thousands of requests per second, scripted in Python, so you control the logic and not only the payload list.
- Works in Community Edition and bypasses the Community Intruder throttle.
- **Move:** race conditions. Example from the article: send 30 requests in a single packet to a "redeem coupon" endpoint and check whether the balance arithmetic breaks. Script in [Snippets](#turbo-intruder-race-condition-template).

### 10. Collaborator Everywhere (Pro)
- Automatically injects Burp Collaborator payloads into headers and parameters of in-scope traffic, then waits. Any callback indicates blind SSRF, blind XXE or worse.
- **Move:** pair with **Taborator** so callbacks appear in their own tab instead of a pop-up.
- Community fallback: free OAST services such as Interactsh cover part of the Collaborator gap.

### 11. Active Scan++ (Pro) + Backslash Powered Scanner (Pro)
- Active Scan++ adds checks the stock scanner lacks: host header attacks, edge-case injections, parsing bugs.
- Backslash Powered Scanner probes for unknown injection classes instead of matching known payloads.
- **Move:** treat both as lead generators, never as findings. Both send real, aggressive traffic; keep them off by default.

### 12. HTTP Request Smuggler (Pro)
- Also by James Kettle. Detects and exploits request smuggling / desync and handles the content-length arithmetic.
- **Move:** run only on targets you are explicitly authorised to hit. Desync can disrupt other users' sessions; this is the loudest tool on the list.

### Worth adding later
- Article's list: 403 Bypasser, Upload Scanner, AuthMatrix, Content Type Converter, Copy As, Piper.
- Also mentioned elsewhere in the article: Taborator (callbacks in own tab, see #10), Shadow Repeater (see [section 8](#8-recent-changes-why-older-guides-mislead)).

---

## 5. Snippets

### Logger filters

- From the article: `Response.Body CONTAINS "error"`
- Status filter (syntax per the Logger++ wiki): `Response.Status == 500`
- **(upstream, Logger++ wiki)** Format is `field operator value`, with operators `=`, `==`, `!=`, `<`, `<=`, `>`, `>=`, combined with `&&` / `||` and parentheses. Regex goes between slashes.

```text
Response.Status == 403 && Request.Method == "post"
Response.Status == 200 || (Request.Method == "post" && (Response.Status == 403 || Response.Cookies == true))
Request.path == /\/ws\/.*/
```

`CONTAINS` is used in the article but is not listed on the wiki page checked; if it is rejected, test it in the filter editor of the installed Logger++ version.

### Hackvertor tags

Tags are nested and evaluated **from the innermost tag outward** (upstream).

```text
<@base64><@urlencode>payload</@urlencode></@base64>
```

Result: URL-encode first, then Base64.

```text
<@urlencode><@urlencode>payload</@urlencode></@urlencode>
```

Double URL encoding by nesting the same tag twice (follows from the nesting rule above).

```text
<@find("\\w")>abc</@find>
```

Tag with an argument; arguments can be strings, booleans or numbers (upstream).

Notes:
- The article writes closing tags as `<@/urlencode>`. The current upstream README uses `</@urlencode>`. Use the Hackvertor tab or the Repeater context menu to insert tags rather than typing them, to get the form your installed version expects.
- In Repeater: right-click → **Hackvertor** menu → pick a tag. The tag stays visible in the editor; the server receives the converted request (upstream).
- Custom tags: Hackvertor menubar → **Create custom tag** (upstream).

### Turbo Intruder: race condition template

Upstream example `resources/examples/race-single-packet-attack.py` from `PortSwigger/turbo-intruder` (AGPL-3.0). Scripts run on Jython, so this is Python 2 syntax (`xrange`).

```python
def queueRequests(target, wordlists):

    # if the target supports HTTP/2, use engine=Engine.BURP2 to trigger the single-packet attack
    # if they only support HTTP/1, use Engine.THREADED or Engine.BURP instead
    # for more information, check out https://portswigger.net/research/smashing-the-state-machine
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=1,
                           engine=Engine.BURP2
                           )

    # the 'gate' argument withholds part of each request until openGate is invoked
    # if you see a negative timestamp, the server responded before the request was complete
    for i in xrange(20):
        engine.queue(target.req, gate='race1')

    # once every 'race1' tagged request has been queued
    # invoke engine.openGate() to send them in sync
    engine.openGate('race1')


def handleResponse(req, interesting):
    table.add(req)
```

- Adjust the `xrange(20)` count (the article's example uses 30 requests).
- HTTP/2 target → `Engine.BURP2`; HTTP/1 only → `Engine.THREADED` or `Engine.BURP`.
- Typical target: one-time actions such as redeeming a coupon, then check the resulting balance or state for inconsistencies.

### JWT Editor: attack checklist

The article's advice is to start with claim tampering before the classic attacks.

1. Change the role or user ID claim and resend.
2. `alg: none`
3. Algorithm confusion
4. Weak signing key
5. Embedded JWK injection

---

## 6. Community vs Pro

| Extension / feature | Community | Pro |
|---|---|---|
| Turbo Intruder | Works | Works |
| Logger++, Param Miner, JWT Editor, InQL | Works | Works |
| Burp Collaborator (blind bugs) | Locked | Works |
| Active Scan++, Backslash Powered Scanner | Locked | Works |
| Built-in Intruder | ~1 req/sec | Full speed |

On Community, Turbo Intruder replaces Intruder (it avoids the throttle). Interactsh can substitute for Collaborator in part.

---

## 7. Safety and scope rules

Extensions run inside the proxy with full access to every request, cookie and token, and there is no sandbox.

1. **BApp Store first.** Builds there are reviewed by PortSwigger. A JAR from a random GitHub repo is not; read the source or skip it.
2. **Set target scope before enabling anything.** Extensions obey Burp's target scope.
3. **Keep loud tools off by default.** Active Scan++ and Request Smuggler send real, aggressive traffic. Never against production you do not own.
4. **Collaborator phones home.** OAST payloads leave the network; on a shared server this is client data in transit. Check whether the engagement allows it.
5. **A signal is not a finding.** A 403 that turned into a 200 is not a bypass until you show the data behind it. Verify, then report.
6. **Too many extensions slow Burp down.** Load what the target needs, disable the rest.

Test only what you are authorised to test.

---

## 8. Recent changes (why older guides mislead)

- **Montoya API** is the current basis for Java extensions. The old Extender API behind Jython/JRuby extensions is no longer actively maintained; expect Python-based BApps to age out.
- **BChecks:** custom scan checks written in plain text, no Java required (Pro/DAST). Suited to encoding target-specific logic into scans.
- **Burp AI** (Pro, credit-metered) launched March 2025.
- **Shadow Repeater** (research BApp): watches manual Repeater attacks, tries variations you did not, and reports anything interesting to Organizer.