# cirl-signature

Email signature generator for Commonwealth IRL. Hosts the navy pin logo at a permanent URL and provides a copy-paste-ready HTML email signature.

## Why this exists

Gmail's signature editor strips image width attributes, breaks data URIs, and can't reliably host images from external URLs. Third-party services (HiHello, WiseStamp) add unwanted branding, "Save Contact" buttons, or monthly fees. This is a single-user, self-hosted signature tool that owns the whole stack.

## How it works

1. Static site deployed to Cloudflare Pages (free tier)
2. Hosts the logo PNG and LinkedIn icon PNG at permanent URLs
3. The `/signature/` page renders a live preview and has a "Copy Signature" button
4. User clicks Copy → pastes into Gmail signature editor → saves → Gmail re-hosts the images on its own CDN
5. Signature works forever even if this site goes down (Gmail cached the images)

## File structure

```
cirl-signature/
├── README.md               # this file
├── index.html              # root — redirects to /signature/
├── signature/
│   └── index.html          # generator page (preview + copy button)
└── assets/
    ├── logo-navy-pin.svg   # source SVG
    ├── logo-navy-pin.png   # 960×300 PNG (displays at 120×38)
    ├── linkedin-icon.svg   # source SVG
    └── linkedin-icon.png   # 48×48 PNG (displays at 24×24)
```

No build step, no dependencies, no framework. Everything is static.

## Deploy to Cloudflare Pages

```bash
# Option 1: via wrangler CLI
npx wrangler pages project create cirl-signature --production-branch=main
npx wrangler pages deploy . --project-name=cirl-signature

# Option 2: via dashboard
# 1. Push this repo to GitHub
# 2. Go to dash.cloudflare.com → Pages → Create a project → Connect to Git
# 3. Select the repo. Build command: (empty). Build output: / (root)
# 4. Deploy
```

The site will be available at `https://cirl-signature.pages.dev`.

## Custom domain (later)

Once the main site is live at `commonwealthirl.org`:
1. Cloudflare Pages dashboard → project settings → Custom domains
2. Add `signature.commonwealthirl.org` (or similar)
3. Update `BASE` in `signature/index.html` if needed (currently uses `location.origin` so custom domains work automatically)

## Install signature in Gmail

1. Open `https://cirl-signature.pages.dev/signature/` (or custom domain)
2. Click **Copy Signature**
3. Open Gmail → ⚙️ Settings → See all settings → General
4. Scroll to **Signature** → **+ Create new** → name it `card`
5. Click into the signature box, paste with `⌘V`
6. Set both **Signature defaults** dropdowns to `card`
7. Click **Save Changes**

## Update the signature later

Edit the `SIGNATURE_HTML` constant in `signature/index.html`, push, Cloudflare Pages auto-deploys. Re-copy and re-paste into Gmail to update existing signatures (Gmail won't auto-refresh).
