# manwithawhistle

Marketing site for Owain Williams &mdash; bespoke dog training services.

## Structure

```
index.html              # The entire site (one file, no build step)
assets/
  owain-hero-clean.jpeg # Hero photo
vercel.json             # Vercel deploy config (headers, clean URLs)
HOW_TO_ADD_DOGS.md      # Guide for adding new training videos
DEPLOY.md               # First-time deployment guide (Vercel + Ionos domain)
```

## Editing

The site is plain HTML and CSS in `index.html`. No frameworks, no build step.

- **Add a new training video:** see `HOW_TO_ADD_DOGS.md`.
- **Change text:** edit the relevant section in `index.html`.
- **Replace the hero photo:** drop a new JPEG into `assets/` and update the path in two places in `index.html` (search for `owain-hero-clean.jpeg`).

## Deploying changes

Every push to `main` deploys automatically to Vercel. Push branches to get preview URLs.

```bash
git add .
git commit -m "describe what changed"
git push
```

Vercel builds + deploys in ~10 seconds.
