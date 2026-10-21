# CLAUDE.md: www.digitaldataservices.com

Static HTML site hosted on GitHub Pages. Repo: `dds-development/www.digitaldataservices.com`. It replaces the old WordPress site, which was retired after repeated compromises. No database, no build step, no server-side code, no CMS. Tom maintains it; changes are rare.

**This repo is public.** Nothing in this file, or anywhere in the repo, may contain internal paths, credentials, personal names or emails, or anything from the WordPress backup except the images and PDFs chosen for the site. Backup locations and migration notes live in the private DDS Claude project (`migration/notes.md`), not here.

## Layout

```
.
├── CLAUDE.md            # instructions (not published)
├── README.md            # short human readme (not published)
├── .gitignore
└── docs/                # the published site; GitHub Pages serves this folder
    ├── CNAME            # www.digitaldataservices.com
    ├── .nojekyll        # serve files as-is
    ├── index.html
    ├── 404.html
    ├── <page>/index.html   # one folder per page for clean URLs (/services/)
    ├── css/site.css     # the only stylesheet
    ├── images/          # all images, flat, lowercase-kebab names
    └── downloads/       # PDFs and other downloadable files
```

Only `docs/` is published, so CLAUDE.md and README.md never appear on the website.

## HTML rules

- HTML5, `lang="en"`, `<meta charset="utf-8">`, viewport meta, a unique `<title>` and `<meta name="description">` per page, exactly one `<h1>`.
- Semantic elements: `header`, `nav`, `main`, `footer`, `section`. No layout tables, no inline styles.
- Root-relative links (`/services/`, `/images/logo.svg`). No absolute links to our own domain.
- Shared header and footer are copied into every page between `<!-- header:start -->` / `<!-- header:end -->` and `<!-- footer:start -->` / `<!-- footer:end -->`. When either changes, update every page in the same commit and confirm all copies are identical (check below).
- One stylesheet, `css/site.css`, with brand colors and fonts as CSS custom properties at the top.
- No external scripts, CDNs, web fonts from third parties, analytics, trackers, or embeds. Fonts, if any, are self-hosted woff2 in `css/fonts/`. JavaScript only if Tom approves it for a specific need.
- Images: lowercase-kebab file names, `alt` text, `width` and `height` attributes, `loading="lazy"` below the fold. Resize and compress before committing (aim for under 300 KB each).
- Forms: `action` points to the hosted form endpoint once a provider is chosen. Until then, show a `mailto:` link to the company role address. Never a personal address.

## Never commit

- `database.sql` or any other file from the WordPress backup, apart from selected images and PDFs.
- Passwords, API keys, tokens, connection strings.
- Personal names tied to contact details, personal email addresses.

`.gitignore` includes: `*.sql`, `*.sql.gz`, `*.wpress`, `*.zip`, `backup/`, `_work/`, `.env`.

Anything committed to a public repo stays in its history even after deletion. If something sensitive is committed, stop and tell Tom; it needs a history rewrite and credential rotation, not just a delete.

## Making a change

1. Create a branch from `main`.
2. Edit the HTML/CSS under `docs/`.
3. Check locally:
   ```bash
   python -m http.server 8000 -d docs     # preview at http://localhost:8000
   # all header/footer copies identical (each command should print 1)
   for b in header footer; do
     for f in $(find docs -name '*.html' ! -name 404.html); do
       sed -n "/<!-- $b:start -->/,/<!-- $b:end -->/p" "$f" | sha1sum
     done | sort -u | wc -l
   done
   ```
4. Show Tom the diff. Push and open a pull request only after he approves.
5. Tom merges. GitHub Pages publishes within a minute or two.

## One-time content migration

- Source: the published WordPress **pages** in the backup (Elementor page content). Posts, docs, form entries, users, and plugin data are not migrated.
- For each page, extract the text and the images it uses, write a clean HTML page in the layout above, and list anything that could not be converted (embedded widgets, forms, scripts) for Tom.
- Rename images to descriptive lowercase-kebab names and drop WordPress size copies (`-300x200` and similar). Keep the original-size file only.
- Old WordPress URLs will break. That is accepted. `404.html` links to the home page and the main sections.

## GitHub and DNS setup (one time, done by Tom)

1. Org settings: require two-factor authentication; verify `digitaldataservices.com` under Pages (prevents domain takeover). [Docs](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/verifying-your-custom-domain-for-github-pages)
2. Repo settings, Pages: source "Deploy from a branch", branch `main`, folder `/docs`; custom domain `www.digitaldataservices.com`; Enforce HTTPS once the certificate issues.
3. Repo settings, Branches: protect `main`, require a pull request.
4. DNS: `www` CNAME to `dds-development.github.io`. Apex `digitaldataservices.com` A records `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`; AAAA `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153`.
5. Keep write access to the repo limited to the people who maintain the site. If Claude pushes from a cloud session, use a fine-grained token scoped to this repo only (contents + pull requests), with an expiry.

## Site notes

- The client logo wall carries over as-is (`images/clients-*.png` naming).
- Product and service PDFs go in `downloads/` with descriptive names.
- Calcite-style SVG icons used on service pages go in `images/icons/`.

## Open decisions

- Hosted form provider for the contact form.
