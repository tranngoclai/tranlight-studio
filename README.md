# Tranlight Studio

Static website for **Tranlight Studio**, a personal tool that uploads self-produced short videos to the owner's TikTok account as drafts.

Live at **https://studio.tranlight.dev**

## Pages

| Path | Purpose |
|---|---|
| [`/`](https://studio.tranlight.dev/) | Landing page describing the app |
| [`/privacy/`](https://studio.tranlight.dev/privacy/) | Privacy Policy |
| [`/terms/`](https://studio.tranlight.dev/terms/) | Terms of Service |
| [`/callback/`](https://studio.tranlight.dev/callback/) | TikTok OAuth redirect URI |

## How the OAuth callback works

TikTok does not accept `localhost` as a redirect URI, so the login flow bounces through this site:

```text
TikTok authorize  →  https://studio.tranlight.dev/callback/?code=…&state=…
                  →  http://localhost:3455/callback/?code=…&state=…  (local CLI)
```

The callback page forwards the query string to the local CLI, which exchanges the code for tokens. The code is useless on its own — the exchange also requires the CLI's PKCE verifier and the client secret, neither of which ever touches this site.

If you change the local port, update `LOCAL_CALLBACK` in [`callback/index.html`](callback/index.html).

## Project structure

```text
.
├── index.html          # Landing page
├── privacy/index.html  # Privacy Policy
├── terms/index.html    # Terms of Service
├── callback/index.html # OAuth relay to the local CLI
├── styles.css          # Shared styles (light + dark)
├── app-icon.svg        # App icon source
├── app-icon.png        # App icon, 1024×1024 (TikTok developer portal)
├── favicon.png
└── CNAME               # Custom domain for GitHub Pages
```

## Hosting

- **GitHub Pages** serves the `main` branch root, with HTTPS enforced.
- **Cloudflare DNS** has a `CNAME studio → tranngoclai.github.io` record set to *DNS only* (not proxied), so GitHub can issue the certificate.

Any push to `main` redeploys the site.

## Local preview

```bash
python3 -m http.server 8000
# open http://localhost:8000
```

## Regenerating the icon

```bash
rsvg-convert -w 1024 -h 1024 app-icon.svg -o app-icon.png
magick app-icon.png -resize 64x64 favicon.png
```
