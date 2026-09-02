# vutbr-redirect

Redirect from `quantum.fit.vutbr.cz` to `quantum.fit.vut.cz`.

`quantum.fit.vutbr.cz` is a DNS CNAME to `quantumfit.github.io`; this repo is the
GitHub Pages site that claims that hostname and bounces visitors to the new domain.

## Files

| File | Purpose |
| --- | --- |
| `CNAME` | Claims `quantum.fit.vutbr.cz` for this Pages site (also triggers the Let's Encrypt cert). |
| `index.html` | Redirect for `/`. |
| `404.html` | Served by GitHub Pages for every other path; preserves path, query and fragment. |
| `.nojekyll` | Skips the Jekyll build — these are plain static files. |

## Setup

1. Push to `main`.
2. Repo → Settings → Pages → Source: *Deploy from a branch*, `main` / `/ (root)`.
3. The custom domain should pick up `quantum.fit.vutbr.cz` from `CNAME`. Wait for the
   certificate to be issued, then tick **Enforce HTTPS**.

Note that until the cert is issued, `https://quantum.fit.vutbr.cz/` fails the TLS
handshake (no matching certificate); only plain `http://` works.

## Why this is not an HTTP 301

GitHub Pages serves static files only: no `.htaccess`, no response-header config, and
no Netlify/Cloudflare-style `_redirects` support. Its one native 301 is the
canonical-domain redirect, which would require putting `quantum.fit.vut.cz` into this
repo's `CNAME` — but that domain is already claimed by the main site's repo, and
GitHub does not allow the same custom domain on two Pages sites. So a meta refresh
plus `rel=canonical` (with JS for path preservation) is the lowest level reachable
here. Search engines do treat `rel=canonical` as a strong consolidation signal.

If a real `301` is wanted, the redirect has to move off GitHub Pages:

- Point `quantum.fit.vutbr.cz` at Cloudflare (free tier) and add a single Redirect
  Rule: `https://quantum.fit.vut.cz/${path}`, 301, preserve query string.
- Or host on Cloudflare Pages / Netlify with a one-line `_redirects` file:
  `/* https://quantum.fit.vut.cz/:splat 301!`
- Or ask the operator of the `fit.vutbr.cz` zone to serve the redirect from their web
  infrastructure instead of delegating the name to GitHub.
