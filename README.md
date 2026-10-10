# Azim Khan · Digital Visiting Card

The complete vCard for **Azim Khan — Chief Executive Officer, FocusOn Interiors**: one static page, no framework,
no build step, no runtime dependencies.

The design system, layout, motion and behaviour are a **1:1 port of the sibling Sarim Khan card**
(`focusoninteriorsai.github.io/Sarim.Khan-VCard`) — same palette, type pairing, section order, cards and effects —
re-populated with Azim's identity, company metrics and contact channels.

## Files

| Path | Purpose |
| --- | --- |
| `index.html` | The whole card: markup, CSS and JS in one file (~69 KB). |
| `logo.png` | FocusOn mark — favicon, intro animation, hero brand, footer, and embedded in the saved `.vcf`. |
| `assets/azim-khan.jpg` | Portrait used in the hero and embedded (downscaled) in the `.vcf`. |
| `assets/profile.png` | Original portrait source (RGBA, 636 × 1200). Not fetched by the page — kept for re-exporting the JPEG. |
| `assets/clients/` | 26 client logos used by the "Marquee Accounts" strips. |
| `assets/vendor/qrcode.js` | Vendored `qrcode-generator` 2.0.4 (MIT) — renders the contact QR fully offline. |

## Features

- Intro: FocusOn logo animation (~2 s, CSS only) over an ambient drifting-gradient background
- Hero: portrait with 3D pointer tilt, floating stat badges, gradient wordmark, pulse kicker, meta chips
- Four stat cards that count up when scrolled into view (company results, not personal claims)
- About + FocusOn model · C.Q.E.T. standard · six services · sector pills · six project cards · two-row client marquee (pauses on hover, second row reverses)
- Cursor spotlight on every card, scroll-reveal on every block, scroll progress bar
- Contact tiles, head-office card with Google Maps link, and an offline QR that saves the contact
- **Save Contact** → standard vCard 3.0 `.vcf` with `PHOTO`/`LOGO`, RFC-2426 octet folding, CRLF line endings
- **Share** → native share sheet, copies the link where unsupported (falls back to the deployed URL on `file://`)
- Sticky quick-action dock, toast feedback, `prefers-reduced-motion` support, skip link, focus rings
- Responsive at 960 / 640 / 380 px; works from `file://` except for the fonts and icons (see below)

## Run

```bash
python3 -m http.server 8000 --bind 0.0.0.0   # open http://localhost:8000
```

or just open `index.html`. Deploy the directory unchanged to GitHub Pages / Netlify / Vercel.
Google Fonts (Outfit, Inter) and Font Awesome 6.5.2 load from CDNs; offline, system fonts take over and the text
labels keep every control usable. `.vcf` export and QR generation never touch the network.

## Update details

All machine-readable identity lives in the `CONTACT` object in the last `<script>` of `index.html` — name, split
first/last, title, org, legal name, phone, WhatsApp, email, URLs, photo, logo and address. It feeds the `.vcf`, the
QR and the share payload; the visible prose in each section is plain HTML and is edited separately. Keep phone numbers
in international format.

`CARD_URL` (same script) is the deployed address, used only when the page is opened from `file://`. Point
`og:image` at the absolute deployed URL if you move hosts — most crawlers ignore relative ones.

**Client marquee.** Entries live in the `clients` array: `{ name, file }` renders `assets/clients/<file>`, and
`{ name, file: null }` renders a typographic tile in the Outfit face (used where no approved logo file exists —
L&T Finance, Centricity, Emversity, Navu). Both rows are generated from one list and duplicated so the
`translateX(-50%)` loop is seamless.

**Portrait.** Replace `assets/profile.png` and re-export the served JPEG:

```bash
python3 -c "
from PIL import Image
im = Image.open('assets/profile.png')
bg = Image.new('RGB', im.size, (255, 255, 255)); bg.paste(im, mask=im.split()[-1])
bg.save('assets/azim-khan.jpg', quality=88, optimize=True, progressive=True, subsampling=1)"
```

Needs `pip install pillow`. The white matte is deliberate: the hero crops the portrait into a 4:5 frame with a dark
name plate over the lower edge, so a transparent background would show through.

## Sources, and what is deliberately *not* claimed

Reference material reviewed on 10 October 2026:

- <https://focusoninteriors.com/> and `/team/` — brand, tagline, CEO role and leadership description.
- <https://github.com/roushan62/FocusOn> — company services, sectors, office address, and the metrics 15 M+ sq. ft.,
  300+ projects, 8+ years, 250+ people.
- <https://github.com/focusoninteriorsai/Sarim.Khan-VCard> — this card's design system, the C.Q.E.T. framing, the
  service list, the client logo set, and the Max Life / Compass Group / Krsnaa Diagnostics engagements.
- <https://in.linkedin.com/in/azim-khan-1424561a9> — profile link as supplied; automated reading was blocked, so
  nothing was inferred from it.

Boundaries kept from the previous revision of this card:

- `+91 99 1025 8820` and `info@focusoninterior.in` are **company** channels and are labelled as such in the UI and in
  the `.vcf`; there is no verified personal number for Azim.
- The four stat cards and the badge figures are **company** results, phrased that way, not personal achievements.
- No education, certifications, founding year, awards, testimonials or personal biography were invented. The portrait
  badge reads "Official" (issued by the company's own repo) rather than "Verified".
- Project cards carry area and scope only — where a ₹ value was not published, none was manufactured.
- The L&T Finance, Luminous and Emversity figures come from this repo's earlier revision; the three extra project
  cards are company engagements credited to the FocusOn team, not to Azim personally.

Client logos are reused from the sibling FocusOn repo, i.e. from the same owner. **Confirm permission for client,
brand and portrait use before public deployment.**

## Checks run

Verified in a jsdom harness (`IntersectionObserver`, `fetch` and a stubbed canvas/image pipeline supplied by the test,
since jsdom decodes nothing): 48 structural and behaviour assertions pass, plus 7 more covering the canvas path —
- section counts (4 stats, 4 C.Q.E.T., 6 services, 9 sectors, 6 projects, 6 contact tiles, 40 reveal blocks),
  marquee tiles (2 × 30), every referenced logo file exists on disk, QR renders as an SVG with modules,
  all three Save / two Share handlers bind, no `Sarim`-era data leaks through, and reduced-motion resolves to
  visible content with counters at their targets with zero console errors;
- the generated `.vcf` parses to the expected `N`/`FN`/`ORG`/`TITLE`/`TEL`/`EMAIL`/`ADR` (comma-escaped)/`URL`/
  `X-SOCIALPROFILE`/`CATEGORIES`/`NOTE` fields, ends `END:VCARD` with CRLF terminators, folds every line to ≤ 75
  octets, and its `PHOTO` payload round-trips byte-for-byte after unfolding.

Two bugs inherited from the reference implementation were fixed rather than copied: its line-folder rebuilds the
string on every fold (quadratic: on this 454 KB portrait it churned enough to exhaust a 1.9 GB heap in testing), and an image
that neither loads nor errors could hang the Save button forever — `imageData()` now races a timeout and ships a lighter `.vcf` instead.
The portrait is also redrawn through a canvas to 512 px JPEG before embedding, so contacts apps get a light payload.

Browser automation could not run in this environment (browser downloads are blocked). Before publication, open the page
on desktop and mobile, scan the QR with a real phone, and import the `.vcf` in your intended contacts app.
