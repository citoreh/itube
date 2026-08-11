# Reel Archive

A single static page (`index.html`) that searches every `.csv` file sitting in the
repo root and plays the matching YouTube URLs inline. No build step, no server.

## How CSV discovery works

The page tries three things, in order, and stops at the first that finds files:

1. **GitHub API** — if the page is served from `*.github.io`, it calls the
   GitHub Contents API for that repo/branch, lists every `*.csv` file in the
   root, and fetches them directly. New CSVs you push are picked up
   automatically, nothing to edit.
2. **`csv-manifest.json`** — a plain JSON array of filenames in the root
   (`["iran_film.csv", "another.csv"]`). Works on *any* static host, not just
   GitHub Pages, and avoids the GitHub API's rate limit. Keep this in sync
   when you add a CSV if you rely on it as your primary method.
3. **Manual upload** — the "load csv file(s)" button at the bottom of the
   page reads local files client-side. Useful for testing before you commit,
   or if a repo isn't public.

If you're hosting on GitHub Pages, method 1 will normally just work and you
never need to touch the manifest. It's there as a safety net.

## CSV format

Two columns, header optional:

```csv
title,url
فیلم سینمایی قدیمی ...,https://www.youtube.com/watch?v=XXXXXXXXXXX
```

- The title/url columns are detected by header name (`title`/`عنوان`,
  `url`/`آدرس`); if no matching header is found the first column is treated
  as the title and the second as the URL.
- Any YouTube URL shape works: `watch?v=`, `youtu.be/`, `embed/`, `shorts/`.
- Standard CSV quoting (`"..."`) is supported if a title ever contains a
  comma.

## Hosting on GitHub Pages

1. Push this folder (`index.html`, `csv-manifest.json`, your `.csv` files) to
   a repo.
2. In the repo settings, enable **Pages** → deploy from the `main` branch,
   root folder.
3. Visit `https://<username>.github.io/<repo>/`.

If your repo's default branch isn't `main`, or you want to point the page at
a different owner/repo (e.g. testing a fork), you can override detection
with query params:

```
index.html?owner=yourname&repo=yourrepo&branch=main
```

## Adding more films

Drop another `.csv` file (same two-column shape) in the root and push. If
you're relying on `csv-manifest.json` as your discovery method, add the new
filename to that list too.
