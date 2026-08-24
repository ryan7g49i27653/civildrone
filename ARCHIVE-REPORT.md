# Civil Drone — Static Conversion Report

**Date:** 24 August 2026
**Source:** `https://civildrone.net` (WordPress 7.1 / PHP 8.2 / MariaDB, theme `nimbit-dark`)
**Output:** `docs/` — 19 pages, 85 files, 5.9 MB

---

## 1. Important: what this directory actually contained

The working directory did **not** contain an HTML mirror. It held a raw
UpdraftPlus backup — `wp-content` plus a MySQL dump:

| Path | Contents |
|---|---|
| `plugins/`, `themes/`, `mu-plugins/` | 1,932 PHP source files |
| `uploads/` | 149 images |
| `database/…-db` | 3.4 MB SQL dump |

The site's content lived in the SQL dump, not in any HTML file. The 35 `.html`
files present were blank directory-guard stubs and Twenty Twenty-Five block
templates — not pages.

### The one existing static export was unusable

`uploads/simply-static/temp-files/simply-static-1-1787603021/` held 6 rendered
pages from the Simply Static plugin. It was a **`temp-files/` directory — an
interrupted run** (Simply Static only promotes temp → destination on success),
broken three independent ways:

1. **`wp-content/` was entirely absent** — no theme CSS, no images. Pages
   rendered as unstyled text.
2. **Two of seven pages were never captured** — `/events` and `/contact`.
3. **Navigation was broken on every subpage.** Links were written `./contact/`
   with no `<base>` tag, so from `/bio/` they resolved to `/bio/contact/`.
   113 broken local targets.

That export was **not** used. It remains in `uploads/` (gitignored) untouched.

### How this archive was produced instead

The live site was still serving HTTP 200, so it was crawled directly — the only
approach that captures fully-rendered pages with real theme output. All 19
public URLs were retrieved, including the two the plugin had missed.

---

## 2. Inventory captured

| | |
|---|---|
| Pages | 19 |
| Content pages | Home, Bio, Events, MyStore, This and That, Contact, Latest News, `category/main` |
| Attachment pages | 11 |
| Images | 86 (61 in-use + 25 rescued originals) |
| Stylesheets | 1 (`nimbit-dark/style.css`) |
| Self-hosted assets | jQuery 1.4.2, Droid Serif woff2 |

### Unpublished media rescued (added after initial conversion)

25 images sat in the WordPress media library but were referenced by no
published page, so the crawl never saw them — they existed only in the
UpdraftPlus backup. Before that backup was deleted they were copied into the
archive at their original `wp-content/uploads/` paths, and a contact sheet was
generated at `docs/archive-media/`.

That page is **not linked from site navigation** and the original 19 pages were
verified byte-identical afterwards (`md5` of all pages unchanged), so the site's
design and content are untouched. The 66 remaining backup-only files were
WordPress thumbnail variants — regenerable, not unique — and were not copied.

---

## 3. Changes applied

### WordPress `<head>` metadata removed

| Element | Occurrences |
|---|---|
| RSD / `EditURI` link | 19 |
| Pingback link | 19 |
| REST API link (`api.w.org`) | 19 |
| REST `alternate` link (`wp-json`) | 18 |
| RSS feed `<link>` | 19 |
| oEmbed discovery (JSON) | 17 |
| oEmbed discovery (XML) | 17 |
| Shortlink | 17 |
| Emoji settings JSON blob | 19 |
| Emoji detection module script | 19 |
| Emoji inline stylesheet | 19 |
| Speculation-rules prefetch | 19 |
| `comment-reply.min.js` | 17 |
| LiteSpeed cache comment | 19 |

`wlwmanifest` and `generator` were already absent (stripped by a security plugin).

### Dynamic elements neutralised

| Element | Action | Count |
|---|---|---|
| Comment form (`#respond`) | Removed — rendered only as a dead "you must be logged in" prompt | 16 |
| Comment reply links | Removed | 2 |
| `securelogin` / `wp-login` links | Converted to inert text | — |
| RSS feed links (footer) | Converted to inert text, layout preserved | 38 |

**No search form existed** on this site, and **no contact form** — `/contact` is
a plain `mailto:` address. Nothing to neutralise in either case.
**No `admin-ajax` calls** were present anywhere.

### Third-party calls removed

| Service | Why | Count |
|---|---|---|
| `c.gigcount.com` beacon | Hidden 1×1 tracking pixel | 19 |
| `reverbnation.com/widgets/trk` beacon | Hidden 1×1 tracking pixel | 19 |
| ReverbNation Flash player | Flash; service defunct | 19 |
| ReverbNation banner (`edgecastcdn.net`) | Dead CDN asset | 19 |
| `nimbitmusic.com` scripts | Nimbit shut down | 2 |
| Facebook Like iframe | Third-party tracker | 19 |

Each visible embed was replaced with an inert placeholder marking what was
there, so the historical record is preserved without any network call.

### Assets self-hosted

Two resources were loading over **plain `http://`**, which HTTPS GitHub Pages
would have **blocked as mixed content**:

- `ajax.googleapis.com/.../jquery-1.4.2.min.js` → `docs/assets/js/`
  jQuery drives the theme's dropdown navigation; blocked, the nav would break.
- `fonts.googleapis.com/css?family=Droid+Serif` → `docs/assets/fonts/`

Both are now local. This was a latent bug in the original site, not something
introduced here.

### Links relativised

684 absolute `civildrone.net` URLs were found. 447 were rewritten to
depth-correct relative paths; the remainder were removed along with the
elements containing them.

---

## 4. Verification

Checked against a local server serving `docs/`:

```
absolute civildrone.net references : 0
broken internal link targets       : 0
insecure http:// resource loads    : 0
full crawl                         : 70 resources / 20 pages, 0 failures
browser console                    : no errors
```

Home and depth-2 attachment pages were rendered and confirmed visually correct:
theme CSS, header artwork, navigation, and images all intact.

### Decommission safety

**Confirmed: the archive makes zero requests to `civildrone.net`.** The only
resource that loads from a remote host is the YouTube embed. Taking the
WordPress install offline will not affect this archive.

---

## 5. Unresolved — read before decommissioning

### Cannot be preserved

- **Events calendar (`/events`)** — the live listing was a remote Nimbit
  `<script>`. Nimbit is defunct, so it returned nothing even on the live site.
  The static event list below it (2010–2011 performances) is fully preserved.
- **Music player** — the ReverbNation Flash widget cannot work in any modern
  browser regardless of archiving.
- **RSS feeds** (`/feed`, `/comments/feed`) — require PHP. Links are inert text.
- **Comment submission** — required login even when live; permanently gone.
  The 2 existing comments are preserved as static text.
- **Search** — no search form existed on this site.

### Judgment calls you may want to reverse

- **Facebook Like iframe was removed.** It was not explicitly in either the
  keep or remove list. It was treated as a tracker rather than a "plain
  outbound link". If you want it back, it was in the `text-4` sidebar widget.
- **The private `News` page is not included.** It was `post_status=private` and
  never publicly reachable, so the crawl did not see it. Its content exists in
  the SQL dump if you want it added.
- **25 originals were rescued, not 23.** Two files — `Civil-Drone-Final.jpg`
  and `cropped-Civil-Drone-Final.jpg` — were first classified as size variants
  by filename heuristic, but are distinct images (the uncropped 1.2 MB source
  of the site's header artwork, and an earlier crop of it). They were included
  rather than lost. Delete those two if you disagree.

### Still live and unarchived

- **YouTube embed** (`b2F8uxr8YrY`) — retained by choice. It is the one
  remaining remote resource load. If that video is ever removed from YouTube,
  the embed goes blank; download it locally if you want it permanently.
- **Outbound links** to Twitter, Facebook, SoundCloud, ReverbNation,
  `gabrielbgmusic.com`, and `nimbit.com` are preserved as plain links. Several
  of those services are defunct — the links will rot, but they only resolve if
  a reader clicks them.

### Keep the backup

The SQL dump is excluded from this repo (it contains a user account, password
hash, and audit logs). **It is also the only remaining copy of the site's
content once the origin is gone.** Retain it privately — do not delete it
alongside the WordPress install.
