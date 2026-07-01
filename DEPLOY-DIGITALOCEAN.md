# Deploy the landing page on DigitalOcean App Platform (Static Site)

Isolated from the product platform. GitHub hosts the code; DO builds + serves it; auto-redeploys on
every push. Free static-site tier, auto-HTTPS, global CDN — no server to maintain.

## 1. Create the app (one time, in the DO dashboard)
1. Go to **https://cloud.digitalocean.com/apps** → **Create App**.
2. **Source:** GitHub → authorize DigitalOcean to access the repo **`Abdulaziz-almoshen/belahhodod-landing`** → branch **`main`** → enable **Autodeploy**.
3. DO detects a **Static Site** (no build command; output = repo root, index = `index.html`).
   - If it doesn't auto-detect, set **Resource type = Static Site**, **Output directory = `/`**.
   - (Or on the review screen, **Edit App Spec** and paste `.do/app.yaml`.)
4. **Region:** Frankfurt (`fra`). **Plan:** Static Sites (free).
5. **Create Resources.** First build/deploy takes ~1–2 min → you get a URL like
   `https://belahhodod-landing-xxxxx.ondigitalocean.app`. Verify it loads.

## 2. Custom domain + DNS
1. App → **Settings → Domains → Add Domain** → enter your domain.
2. DO shows you the target + issues a TLS cert automatically. Then at your DNS provider:
   - **Subdomain** (e.g. `www.belahhodod.com` or `new.belahhodod.com`):
     add a **CNAME** → `belahhodod-landing-xxxxx.ondigitalocean.app` (the value DO shows).
   - **Apex/root** (e.g. `belahhodod.com`): either
     - let **DO manage DNS** (add the domain under DO Networking, point your registrar's
       **nameservers** to DO) and DO adds an **ALIAS** automatically — cleanest; or
     - if staying on the current DNS provider, add the records DO lists (ALIAS/ANAME if supported,
       else the provided A records).
3. Wait for DNS propagation + the cert to go green → HTTPS live on your domain.

> ⚠️ If the domain currently serves the live WordPress site, repointing its DNS replaces it.
> Recommended: map a **subdomain first** (e.g. `new.belahhodod.com`) to verify, then cut over the apex.

## 3. After go-live
- Every `git push` to `main` auto-redeploys — no manual step.
- GitHub Pages can be disabled (repo → Settings → Pages) once DO serves the domain, or kept as backup.
