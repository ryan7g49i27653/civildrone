# Civil Drone — Static Archive

A frozen, self-contained static archive of **civildrone.net**, a WordPress site
retired in August 2026. Preserved here for historical reference.

The archive is **fully self-contained**: once published it makes no requests to
`civildrone.net`, so the original WordPress install can be decommissioned
without affecting it.

## Layout

```
docs/                     <- the deployable static site (19 pages)
  index.html              Home
  bio/  events/  mystore/  photos/  contact/
  venueevent-schedule/    "Latest News" post
  category/main/          Category archive
  photos/<slug>/          11 attachment pages
  wp-content/             Theme CSS + 86 images (original paths preserved)
  assets/                 Self-hosted jQuery + Droid Serif webfont
  archive-media/          Contact sheet of 25 unpublished media-library
                          images; NOT linked from site navigation
```

## Deploying

The site is served from `docs/` on the default branch:

**Settings → Pages → Source: "Deploy from a branch" → `main` / `/docs`**

No build step, no workflow, no dependencies. To serve from the repository root
instead, move the contents of `docs/` up one level — every internal link is
relative, so nothing needs rewriting.

## Local preview

```bash
python3 -m http.server 8000 --directory docs
```

## What was changed

See [ARCHIVE-REPORT.md](ARCHIVE-REPORT.md) for the full record of what was
removed, rewritten, and what could not be preserved.

Visual design and page content are unchanged. The only removals are WordPress
plumbing that cannot function without PHP, and third-party embeds whose
services no longer exist.

## Not included

The UpdraftPlus backup this archive was reconstructed alongside — the MySQL
dump, plugin source, and theme source — is excluded via `.gitignore`. The
database dump contains a user account, a password hash, and security audit
logs, and should not be published. **Retain it privately**: it is the only
remaining copy of the site's content once the origin is gone.
