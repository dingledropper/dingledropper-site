# dingledropper.com — landing site

Static site for dingledropper LABS. Self-contained HTML, no build step, no dependencies, no JavaScript frameworks.

## Structure

```
site/
├── index.html                    # Landing page → dingledropper.com/
├── susdetector/
│   └── privacy.html              # SusDetector privacy policy → dingledropper.com/susdetector/privacy.html
├── og-image.html                 # Source for the main site OG share card
├── og-image.png                  # Rendered 1200x630 OG card for index.html
├── og-susdetector.html           # Source for the SusDetector OG share card
├── og-susdetector.png            # Rendered 1200x630 OG card for the privacy page
├── feature-graphic.html          # Source for the Play Store feature graphic
├── feature-graphic.png           # Rendered 1024x500 feature graphic for Play Console
└── README.md                     # This file
```

Every HTML file is fully self-contained — CSS is inlined, fonts are system fonts, zero external assets. Upload and it works.

## URLs after deployment

| Page | URL |
|---|---|
| Landing | `https://dingledropper.com/` |
| SusDetector privacy policy | `https://dingledropper.com/susdetector/privacy.html` |
| Main OG share card (for Twitter/LinkedIn etc.) | `https://dingledropper.com/og-image.png` |
| SusDetector OG share card | `https://dingledropper.com/og-susdetector.png` |

Both HTML pages already reference the correct OG image URLs via `<meta property="og:image">` tags, so link previews on Twitter, LinkedIn, Discord, Slack, iMessage, Signal, Facebook, etc. will automatically pull the right card.

**The Play Console privacy policy URL is:** `https://dingledropper.com/susdetector/privacy.html` — paste this into Play Console → App content → Privacy policy.

## Deployment — pick your host

### Option A: Squarespace (since you already have an account)

Squarespace CAN host a custom HTML site, but it's not Squarespace's happy path — their platform is built around their visual editor and template system. Here's the realistic options on Squarespace:

1. **Code Injection + a blank page template** — works for simple one-page sites but doesn't scale to multi-page (privacy policy at a subpath is awkward). Skip this option.
2. **Custom Code Block inside a regular Squarespace page** — the HTML gets embedded inside their template chrome (their nav, their footer), and you can't remove those without a Business plan upgrade. Not ideal for a clean landing site.
3. **Developer Platform / Custom Templates** — full control, but requires switching your site to Developer Mode, editing `.region` files, and deploying via git. This is the "right" way on Squarespace but it's a non-trivial learning curve for a one-page landing site.
4. **Point the domain at a real static host** — keep Squarespace for whatever else you might be using it for, but point `dingledropper.com` DNS at Cloudflare Pages / Netlify / Vercel for the landing site. **This is what I recommend.**

**If you're already paying for a Squarespace subscription** and Squarespace is where your domain registration lives:

- Log into Squarespace → Settings → Domains → click `dingledropper.com`
- Choose **"Use a different domain provider"** or find the **DNS settings** panel
- Add the DNS records your chosen static host provides (A records + CNAME)
- The domain keeps its Squarespace registration but points at the other host for the actual site

This is the cleanest way to keep your Squarespace account for other purposes and still ship a professional landing site. You can always move back to a pure Squarespace site later if you build something fancier there.

**If you really want to keep everything inside Squarespace:**

1. Create a new blank page in your Squarespace site
2. Use a **Code Block** (Business plan or higher)
3. Paste the contents of `index.html` into the Code Block
4. Repeat for `privacy.html` on a separate page
5. Set the URL slug for each page to match (`/` for landing, `/susdetector/privacy` for the policy)
6. Upload the OG images and feature graphic to Squarespace's file asset system and update the `<meta property="og:image">` URLs in the HTML to match the uploaded paths

Caveats:
- You cannot remove Squarespace's template header/footer without the Developer plan
- File upload paths in Squarespace are auto-generated and ugly
- The `<title>` and `<meta>` tags inside a Code Block may conflict with Squarespace's own head tags, making OG previews unreliable

**My honest recommendation:** keep Squarespace for whatever else you're using it for, and point the `dingledropper.com` domain at Cloudflare Pages for the site. 5 minutes of DNS work, free forever, no template fighting. See Option B.

### Option B: Cloudflare Pages (my recommendation — free, fast, pairs well with Squarespace-registered domain)

1. Go to [dash.cloudflare.com](https://dash.cloudflare.com) → Pages → Create a project → Direct Upload
2. Project name: `dingledropper-site` (or anything — this is just the internal name)
3. Drag the entire `site/` folder (not its parent) into the upload area. All 8 files should upload.
4. Click **Deploy site**. Cloudflare gives you a `.pages.dev` URL instantly for testing.
5. Once it works on the `.pages.dev` URL:
   - Go to **Custom domains** → Set up a custom domain → enter `dingledropper.com`
   - Cloudflare shows you the DNS records you need to add
6. Back in **Squarespace → Settings → Domains → dingledropper.com → DNS Settings** (or wherever your domain is registered):
   - Add the CNAME record Cloudflare gave you (usually something like `dingledropper.com` → `<your-project>.pages.dev`)
   - Save
7. Wait 5-30 minutes for DNS propagation. Cloudflare issues an SSL cert automatically.
8. Done. Site is live at `https://dingledropper.com`.

Cloudflare Pages is free for unlimited bandwidth and has the fastest cold-start times of any static host.

### Option C: Netlify (similar to Cloudflare, drag-and-drop UI)

1. Sign up at [netlify.com](https://netlify.com)
2. Go to **Sites** → drag the `site/` folder onto the drop zone
3. Site deploys to a random `.netlify.app` subdomain
4. **Domain management** → Add custom domain → `dingledropper.com`
5. Follow Netlify's DNS instructions and add the records in Squarespace

### Option D: GitHub Pages (free, requires a GitHub repo)

1. Create a public GitHub repo (name doesn't matter)
2. Upload the contents of `site/` to the repo root
3. Repo → Settings → Pages → Source: `Deploy from a branch` → Branch: `main` → Folder: `/ (root)`
4. Wait for the first deploy, then add your custom domain in Settings → Pages
5. Add the CNAME in Squarespace DNS

### Option E: Vercel

Same flow as Netlify — sign up, drag folder, add custom domain, update DNS. Free tier is generous.

## How the DNS change works with Squarespace

You do NOT need to transfer your domain away from Squarespace. The domain registration (who owns the name) and the DNS routing (where the name points) are separate. You keep the first, change the second.

1. Log into Squarespace
2. Settings → Domains → `dingledropper.com`
3. DNS Settings (or "Advanced DNS")
4. For whichever host you chose (Cloudflare Pages / Netlify / Vercel / GitHub Pages), the host gives you 1-2 records to add. Usually:
   - One CNAME record for `www` → the host's CDN URL
   - One A record or ALIAS for the apex (`@`) → the host's IP addresses
5. Save
6. Wait up to 1 hour (usually minutes) for propagation
7. Visit `https://dingledropper.com` and you should see your site

## Updating the site

After the first deploy, any edit follows the same flow:

- **Cloudflare Pages / Netlify / Vercel** — re-drag the `site/` folder onto the project dashboard, it redeploys in ~30 seconds
- **GitHub Pages** — commit and push
- **Squarespace Code Block** — paste updated HTML into the block editor

## How to regenerate the images

All three PNGs (`og-image.png`, `og-susdetector.png`, `feature-graphic.png`) are rendered from the matching `.html` files via headless Chrome. When you want to update the design:

1. Edit the source `.html` file
2. Run the render command from a terminal in the `site/` directory:

```bash
# OG image (1200x630)
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless=new --disable-gpu --hide-scrollbars --force-device-scale-factor=1 \
  --screenshot=/tmp/og-image-raw.png --window-size=1200,850 \
  --default-background-color=00000000 \
  "file://$(pwd)/og-image.html"

python3 -c "from PIL import Image; Image.open('/tmp/og-image-raw.png').crop((0,0,1200,630)).save('og-image.png')"
```

Same pattern for `og-susdetector.html` (1200×630) and `feature-graphic.html` (1024×500 — use `window-size=1024,700`).

The reason for the overshoot-and-crop: Chrome's `--window-size` includes window chrome (toolbar etc.), so actual viewport ends up smaller than requested. Overshooting by ~150-200px and cropping to the target dimensions guarantees the full content fits.

## Style notes

The aesthetic matches the SusDetector "Backlit Neon" theme:
- Background: `#A0C8E0` (deep sky blue)
- Surface cards: `#EADAE8` / `#DCC4DA` (pink-lavender)
- Text: `#0A1020` (near-black navy)
- Accent: `#1A5A9A` (bold blue)
- Shadows: hard offset drop shadows (`4px 4px 0 0 #0A1020`) + optional backlit blue glow
- Borders: thick 2.5px near-black outlines on all cards and buttons
- Buttons animate a "press-in" on `:active`

Fonts are the system stack — no Google Fonts, no CDN dependencies. Site loads in well under a second on any connection.

## File sizes

| File | Size |
|---|---|
| index.html | ~14 KB |
| susdetector/privacy.html | ~16 KB |
| og-image.png | ~80 KB |
| og-susdetector.png | ~105 KB |
| feature-graphic.png | ~95 KB |
| **Total** | ~310 KB |

Entire site is under 320 KB with zero dependencies.
