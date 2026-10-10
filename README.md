# Azim Khan · FocusOn Interiors

A static, responsive digital visiting card. No framework, build step, API, or runtime package installation. All application CSS and JavaScript, the portrait, the brand mark, and the QR generator are embedded in `index.html`. The local asset copies are supplied for reuse and editing.

## Run

Open `index.html` directly, or from this directory run:

```sh
python3 -m http.server 8000 --bind 0.0.0.0
```

Visit `http://localhost:8000`. Deploy the directory unchanged to GitHub Pages, Netlify, Vercel, or any static host.

Google Fonts (Outfit/Inter) and Font Awesome load from CDNs when online. Offline, system fonts take over; text labels keep controls usable without icon fonts. Contact download and QR generation are completely offline. External websites, calls, WhatsApp, and email require the relevant connection/device apps.

## Update details

Find `const CONTACT =` near the first script in `index.html`. Update name, separate first/last names, title, company/legal name, phone, display phone, WhatsApp, email, URLs, and structured address there. The displayed identity, contact links, downloads, and QR all use this object. Keep phone numbers in international format. Contact channels currently belong to the **company**, not a verified personal line for Azim.

Set `cardUrl` to this card's public HTTPS URL after deployment. Otherwise Share uses the current page URL; when opened as a local file it falls back to the company website rather than sharing a private filesystem path. Native sharing requires browser support; clipboard and a manual-copy prompt provide fallbacks.

Editorial paragraphs, company statistics, project cards, regional office labels, and service descriptions are plain HTML. Update those separately when the company changes. Client names live in `clientNames`. Brand colors are CSS variables in `:root`.

## Replace portrait / logo

Replace `assets/profile.png` and `logo.png` with your approved PNGs (square logo recommended). To preserve standalone/offline behavior, update the `photo` and `logo` data URLs in CONTACT, not just `photoPath` / `logoPath` (those document the source assets). The favicon contains the same embedded logo. This optional one-time Python command synchronizes all three automatically; it is an editing aid, **not a build requirement**:

```sh
python3 - <<'PY'
import base64, re
from pathlib import Path
p = Path('index.html')
s = p.read_text()
for key, source in [('photo', 'assets/profile.png'), ('logo', 'logo.png')]:
    data = 'data:image/png;base64,' + base64.b64encode(Path(source).read_bytes()).decode()
    s = re.sub(r"\b" + key + r":'data:image/png;base64,[^']*'", key + ":'" + data + "'", s)
    if key == 'logo':
        s = re.sub(r'(<link rel="icon" href=")[^"]*(")', lambda m: m[1] + data + m[2], s)
p.write_text(s)
PY
```

The export includes a folded vCard 3.0 `PHOTO` and `LOGO`; whether they are displayed depends on the importing contacts app. QR encodes a smaller, image-free vCard with the same identity, phone, email, address, and URLs. Scanning opens a contact preview/import prompt on compatible devices; users must confirm saving. It cannot silently save a contact.

For public link previews, change `og:image` to the **absolute deployed URL** of `assets/profile.png`; most crawlers require absolute URLs. Update title and description metadata when changing the identity.

## Sources and content choices

Reference material reviewed on 10 October 2026:

- https://focusoninteriors.com/ — official brand and tagline.
- https://focusoninteriors.com/team/ — confirms Azim Khan's CEO role and leadership description.
- https://roushan62.github.io/FocusOn/about/ — company services, positioning, projects, and clients.
- https://roushan62.github.io/FocusOn/contact/ — published company contact channels/offices.
- https://github.com/roushan62/FocusOn — reference site's `lib/data.ts` supplies legal entity, numerical counter targets (15M+ sq. ft., 300+ projects, 8+ company years, 250+ people), structured office address, and services; `public/images` supplies the real portrait and logo.
- https://in.linkedin.com/in/azim-khan-1424561a9 — user-provided profile link, retained as supplied. Automated reading was blocked; no personal claims were inferred from it.

Company statistics are labeled as company results, not personal achievements. No unsupported certification, founding date, personal phone, or personal email was added. The methodology is the company's published quality pillars, not a certification.

Project photography and original client logo downloads were inaccessible in this environment. Project cards intentionally use editorial geometric graphics rather than fabricated project photos. The marquee uses clearly typographic client-name treatments, not purported official logo artwork; matching editable SVG copies are in `assets/clients`. Replace with approved client logos if available (and update the inline image generation if standalone support is needed). Please confirm permission for brand/portrait/client use before public deployment.

## QR dependency & checks

`assets/vendor/qrcode.js` is qrcode-generator **2.0.4**, copyright Kazuhiko Arase, MIT licensed; upstream license notice is retained in both the vendored and embedded copies. No CDN or service is involved in QR creation.

Checked with a JavaScript DOM harness: offline script execution, rendered QR SVG, populated contact links, embedded photo/logo, vCard fields, escaping, CRLF termination, and 75-byte line folding. Browser automation could not be run because downloading the browser was blocked by the environment's network policy. Before publication, test the page on desktop/mobile, scan the QR with a real phone, and import the VCF in your intended contacts app.
