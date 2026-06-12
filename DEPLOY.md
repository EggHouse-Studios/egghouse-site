# Deploying egghouse.studio — morning runbook

Your site is built and SEO-optimized in the **`site/`** folder:

```
site/
├── index.html      ← the website (clean, self-contained)
├── og-image.png    ← social-share preview image
├── robots.txt      ← search-engine crawl rules
└── sitemap.xml     ← page index for Google/Bing
```

Plan: **GitHub Pages** (free hosting) + **DNS stays at Squarespace**.

---

## Step 1 — Create a free GitHub account
1. Go to https://github.com/signup
2. Use whatever email you like (your new `hello@egghouse.studio` is fine once it works, or any email).
3. Pick a username — e.g. `egghouse-studios` (this becomes part of your free URL).

## Step 2 — Create the repository
1. Click the **+** (top-right) → **New repository**.
2. Name it `egghouse-site`. Set it to **Public**. Click **Create repository**.

## Step 3 — Upload the site files
1. On the new repo page, click **uploading an existing file** (or **Add file → Upload files**).
2. Drag in the **contents of the `site/` folder** — i.e. `index.html`, `og-image.png`, `robots.txt`, `sitemap.xml`.
   ⚠️ Upload the *files themselves*, not the `site` folder, so `index.html` sits at the repo root.
3. Click **Commit changes**.

## Step 4 — Turn on GitHub Pages
1. In the repo, go to **Settings → Pages**.
2. Under **Build and deployment → Source**, choose **Deploy from a branch**.
3. Branch: **main**, folder: **/ (root)**. Click **Save**.
4. Wait ~1 minute. A live URL appears: `https://<username>.github.io/egghouse-site/`. Open it to confirm it works.

## Step 5 — Connect your domain (in GitHub)
1. Still in **Settings → Pages → Custom domain**, type: `egghouse.studio`  → **Save**.
   (GitHub adds a `CNAME` file to your repo automatically.)
2. Leave **Enforce HTTPS** unchecked for now — you'll check it after DNS propagates (Step 7).

## Step 6 — Add DNS records at Squarespace
Log in at https://account.squarespace.com → your domain → **DNS / DNS Settings**.

**a) Remove the existing parking records** first:
- Delete any existing `A` records on host `@` that point to Squarespace IPs (`198.185.159.x` / `198.49.23.x`).
- Delete the existing `www` `CNAME` if it points to Squarespace.

**b) Add these four A records** (host `@`, the apex):

| Type | Host | Value |
|------|------|--------------------|
| A | @ | `185.199.108.153` |
| A | @ | `185.199.109.153` |
| A | @ | `185.199.110.153` |
| A | @ | `185.199.111.153` |

**c) Add the www redirect:**

| Type | Host | Value |
|------|------|----------------------------|
| CNAME | www | `<username>.github.io` |

*(Replace `<username>` with your real GitHub username. Note: just `username.github.io`, with no `/egghouse-site` path.)*

**Optional (IPv6, nice to have)** — four AAAA records on host `@`:
`2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153`

> 📧 **Email note:** Any `MX` records for your `hello@egghouse.studio` mailbox are *separate* and do **not** conflict with the records above. Leave your email's MX (and any TXT/SPF/DKIM) records in place.

## Step 7 — Finish (later that day)
1. DNS takes ~15 min–2 hrs (occasionally up to 24 hrs) to propagate.
2. Visit `https://egghouse.studio` — it should load your site.
3. Back in **GitHub → Settings → Pages**, tick **Enforce HTTPS** (it becomes available once the cert is issued).

## Step 8 — Tell Google about it (SEO)
1. Go to https://search.google.com/search-console → **Add property** → enter `egghouse.studio`.
2. Verify (easiest with the domain — Google gives you one TXT record to add at Squarespace).
3. Once verified: **Sitemaps** → submit `https://egghouse.studio/sitemap.xml`.
4. This gets you indexed faster and shows search performance over time.

---

## Updating the site later
Edit `index.html` directly in GitHub (pencil icon) or re-upload — changes go live in ~1 min.

## When you're back, I can help with:
- Installing the `gh` CLI so future updates are one command.
- Setting up `hello@egghouse.studio` email DNS (MX records) if you haven't finished that.
- Verifying the live site, checking the social preview, and confirming Search Console.
