# OutLayer.ai — Project Documentation

Verifiable off-chain computation for NEAR with Intel TDX attestation and NEAR MPC key derivation.

---

## Tech Stack

| Layer    | Technology                              |
| -------- | --------------------------------------- |
| Backend  | Node.js + Express.js                   |
| Frontend | Vanilla HTML / CSS / JavaScript         |
| Deploy   | Systemd + Nginx (reverse proxy + SSL)  |
| Hosting  | Self-hosted (no CDN except Google Fonts)|

Only dependency: `express ^4.21.0`.

---

## Project Structure

```
outlayer.ai/
├── server.js                   # Express server (port 3001), cache busting
├── package.json
├── nginx.conf                  # Reverse proxy + SSL + gzip + security headers
├── deploy.sh                   # git pull → npm ci → restart service
├── setup.sh                    # Systemd service setup
├── outlayer-landing.service    # Systemd unit
├── public/
│   ├── index.html              # Landing page (home)
│   ├── products.html           # Products page
│   ├── styles.css              # Shared styles (~1500 lines)
│   ├── script.js               # Particles, scroll-reveal, navigation
│   └── outlayer.png            # Logo
└── project.md                  # ← this file
```

---

## Server (`server.js`)

- Port: `process.env.PORT || 3000` (production uses 3001 via systemd).
- **Cache busting**: computes MD5 of `styles.css` + `script.js` at startup, injects `?v=HASH` into HTML.
- Static files from `public/` with `max-age: 1 day`; HTML served with `no-cache`.
- Catch-all `*` → `index.html` (SPA-like routing).

Named routes:

| Route       | File             |
| ----------- | ---------------- |
| `/`         | `index.html`     |
| `/products` | `products.html`  |
| `/connectors` | `connectors.html` (nav label: Integrations) |

---

## Design System

- **Theme**: dark (`--bg: #06060b`)
- **Accents**: Orange `#ff6b35`, Green `#00ff88`, Purple `#a78bfa`, Blue `#38bdf8`
- **Fonts**: Inter (UI), JetBrains Mono (code)
- **Patterns**: glass-morphism cards, gradient text, backdrop-blur
- **Breakpoints**: 1024px / 768px / 480px
- **Animations**: canvas particles, IntersectionObserver fade-in with staggered delays

---

## Pages

### `/` — Landing (index.html)

Main platform landing page.

| Section                   | Description                                                     |
| ------------------------- | --------------------------------------------------------------- |
| Hero                      | "Live on NEAR Mainnet" badge, headline, CTA                    |
| Feature Cards (×6)        | TDX, CKD, Secrets, Blockchain+HTTPS, Monetization, AI-friendly |
| Security Model            | Two layers: Intel TDX + NEAR MPC Network                       |
| How It Works (×4 steps)   | Request → Secrets → TEE Execution → Verified Result             |
| Smart Contract Integration| Code examples (Rust / HTTPS)                                   |
| Monetization              | Micropayment and subscription examples                          |
| Comparison                | Comparison with L2s and other solutions                         |
| Use Cases (×6)            | AI, DeFi, Privacy, Gaming, Infra, Identity                     |
| CTA                       | Links to docs and playground                                    |
| Footer                    | Navigation, social links                                        |

### `/products` — Products (products.html)

Showcase of products built on OutLayer.

| Section             | Description                                               |
| ------------------- | --------------------------------------------------------- |
| Header              | "Built on OutLayer"                                       |
| Voulai              | Private AI trading agents on confidential NEAR Intents; BYO-agent rails; table: DIY agent vs Voulai rails |
| near.email          | Wallet-based encrypted email for NEAR; ECIES/secp256k1   |
| Security Comparison | Table: Gmail vs ProtonMail vs near.email                  |
| TEE Price Oracle    | On-demand oracle with Intel TDX; 13 tokens                |
| Oracle Comparison   | Table: Traditional Oracles vs TEE Price Oracle            |
| near.fm             | AI music generation; OutLayer payment checks + agent treasury |
| Coming Soon         | Placeholder for future products                            |

---

### `/connectors` — Connectors (connectors.html)

Landing for people arriving from the connectors announcement: what an agent can do through connectors and why the owner's limits hold. Plain language first, builders second.

| Section        | Description                                                                 |
| -------------- | --------------------------------------------------------------------------- |
| Hero           | "Your AI agent acts. You set the limits." — CTA to app.outlayer.ai/connectors |
| Demo           | Animated chat (inline script): an agent told to break a $15 limit; the refusals are the Hyperliquid connector's own error strings |
| How It Works   | 3 steps: you set rules → agent asks → enclave decides                        |
| Connectors     | Hyperliquid, Polymarket, Gmail, GitHub, Mercury — "can do" / "you control"  |
| Why You Can Let It | Keys never with the agent; limits outside the prompt; attestation; pay per call |
| For Builders   | Skill URL (skills.outlayer.ai/outlayer/SKILL.md), 3-step start, docs, Telegram |
| CTA + legal    | Connect button; third-party venues, jurisdiction and responsibility notice, link to Terms |

Styles: `/* ===== CONNECTORS PAGE ===== */` in `styles.css` (`cx-*` classes).

<!-- ===== ADD NEW PAGES BELOW USING THE TEMPLATE ===== -->

## Adding a New Page

### Checklist

1. Create `public/<page>.html`
2. Add a route in `server.js`:
   ```js
   app.get('/<page>', (req, res) => {
       const html = injectCacheBuster(
           fs.readFileSync(path.join(publicDir, '<page>.html'), 'utf8')
       );
       res.set('Cache-Control', 'no-cache').send(html);
   });
   ```
3. Add styles in `styles.css` (use a section comment `/* === <Page> === */`)
4. Add a link in the `<nav>` of `index.html` and other pages
5. **Document below** — copy the template and fill it in.

### Template

```markdown
### `/<route>` — Page Name (filename.html)

Short description of the page.

| Section  | Description       |
| -------- | ----------------- |
| ...      | ...               |
```

---

## External Links

| Resource     | URL                                              |
| ------------ | ------------------------------------------------ |
| App          | https://app.outlayer.ai                          |
| API          | https://api.outlayer.ai                          |
| Docs         | https://app.outlayer.ai/docs/getting-started |
| Playground   | https://app.outlayer.ai/playground          |
| GitHub       | https://github.com/out-layer/outlayer        |
| Twitter/X    | https://x.com/out_layer                          |
| Telegram     | https://t.me/out_layer                           |
| near.email   | https://near.email                               |
| Price Oracle | https://price-oracle.outlayer.ai                       |
