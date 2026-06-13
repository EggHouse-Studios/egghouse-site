# Deploying egghouse.studio

The site is hosted on **GitHub Pages** from this repo (`EggHouse-Studios/egghouse-site`, public) and served at the custom domain **egghouse.studio**. DNS is managed at **Squarespace**; email stays on **Google Workspace** (untouched).

## Repo layout

The site files live at the **repo root** (GitHub Pages serves them at the apex domain):

```
index.html      ← the website (clean, self-contained)
og-image.png    ← social-share preview image
robots.txt      ← search-engine crawl rules
sitemap.xml     ← page index for Google/Bing
CNAME           ← tells GitHub Pages the custom domain is egghouse.studio
```

## How deploys work now

This is a git repo with `origin → github.com/EggHouse-Studios/egghouse-site`. **To publish a change, just push to `main`:**

```bash
# from this folder
# edit index.html (or assets), then:
git add -A
git commit -m "Update site copy"
git push
```

GitHub Pages rebuilds automatically within ~1 minute. No manual upload, no separate hosting step.

Check build status / config:

```bash
gh api repos/EggHouse-Studios/egghouse-site/pages/builds/latest --jq '.status'
gh api repos/EggHouse-Studios/egghouse-site/pages --jq '{cname,https_enforced,status}'
```

## DNS (one-time, at Squarespace)

The apex (`egghouse.studio`) points at GitHub Pages via four `A` records; `www` is a CNAME to the org's Pages domain. **MX/email records are separate and must stay in place.**

| Type | Host | Value |
|------|------|--------------------------|
| A | @ | `185.199.108.153` |
| A | @ | `185.199.109.153` |
| A | @ | `185.199.110.153` |
| A | @ | `185.199.111.153` |
| CNAME | www | `egghouse-studios.github.io` |

Optional IPv6 — four `AAAA` records on host `@`:
`2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153`

After DNS propagates (~15 min–2 hrs), GitHub auto-issues a Let's Encrypt cert. Then enforce HTTPS:

```bash
printf '{"https_enforced":true}' | gh api -X PUT repos/EggHouse-Studios/egghouse-site/pages --input -
```

> **Gotcha (learned the hard way):** if the cert never issues — `.https_certificate.state` stays `null` for hours and the served cert stays `CN=*.github.io` — GitHub's provisioning queue is wedged. This happens when the custom domain was first set while DNS was still propagating, or when repeated rebuilds keep resetting it. **Fix: cleanly toggle the custom domain off and back on, with no builds in between**, then it issues in seconds:
> ```bash
> printf '{"cname":null}'              | gh api -X PUT repos/EggHouse-Studios/egghouse-site/pages --input -   # remove
> # wait for the build to finish (gh api .../pages --jq .status == "built")
> printf '{"cname":"egghouse.studio"}' | gh api -X PUT repos/EggHouse-Studios/egghouse-site/pages --input -   # re-add
> ```
> Each toggle makes the Pages bot commit `Delete CNAME`/`Create CNAME` to the repo, so `git fetch && git rebase origin/main` afterward to resync local.

## SEO (one-time)

1. [Google Search Console](https://search.google.com/search-console) → **Add property** → **Domain** → `egghouse.studio`. Because the domain is on **Google Workspace**, Search Console **auto-verifies** ownership (the `google-site-verification` TXT record is already present) — no DNS step needed.
2. **Sitemaps** → submit `sitemap.xml`. *(Done 2026-06-12.)*
3. Optional: **URL inspection** → enter `https://egghouse.studio/` → **Request indexing** to nudge a faster first crawl.

## Verifying it's live

```bash
dig +short egghouse.studio A          # should show the 185.199.108-111.153 IPs
curl -sI https://egghouse.studio | head -1   # HTTP/2 200
```
