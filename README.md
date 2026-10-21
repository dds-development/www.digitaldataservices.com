# www.digitaldataservices.com

Source for the public website of [Digital Data Services, Inc.](https://www.digitaldataservices.com) (DDS), a GIS consulting firm and Esri and VertiGIS partner. The site describes DDS services (GIS data creation and management, GIS implementation, managed ArcGIS Enterprise, custom GIS development), the geoWidgets product family, and how to contact us.

The site is plain, hand-written HTML and CSS served by GitHub Pages. There is no CMS, database, build step, or server-side code, and no third-party scripts, trackers, or web fonts.

## Repository layout

```
.
├── README.md
├── .gitignore
└── docs/                    # published site (GitHub Pages serves this folder)
    ├── CNAME                # custom domain
    ├── .nojekyll            # serve files as-is
    ├── index.html           # home page
    ├── 404.html
    ├── <page>/index.html    # one folder per page, for clean URLs
    ├── css/site.css         # the only stylesheet; brand colors at the top
    ├── css/fonts/           # self-hosted Montserrat (SIL Open Font License)
    ├── images/              # images, lowercase-kebab file names
    └── downloads/           # brochures and other PDFs
```

Only `docs/` is published. Files at the repository root never appear on the website.

## Preview locally

```bash
python -m http.server 8000 -d docs
```

Then open http://localhost:8000.

## Making changes

1. Create a branch from `main`.
2. Edit the HTML or CSS under `docs/`.
3. The header and footer are copied into every page between `<!-- header:start -->` / `<!-- header:end -->` and `<!-- footer:start -->` / `<!-- footer:end -->`. If you change either one, update every page in the same commit. This check should print `1` twice:

   ```bash
   for b in header footer; do
     for f in $(find docs -name '*.html' ! -name 404.html); do
       sed -n "/<!-- $b:start -->/,/<!-- $b:end -->/p" "$f" | sha1sum
     done | sort -u | wc -l
   done
   ```

4. Open a pull request into `main`. Changes go live a minute or two after the pull request is merged.

## Content guidelines

- Every page has a unique `<title>`, a `<meta name="description">`, and exactly one `<h1>`.
- Use root-relative links (`/about-us/`, `/images/dds-logo.png`).
- Images need `alt` text and `width`/`height` attributes. Keep each image under about 300 KB.
- No inline styles, external scripts, CDNs, analytics, or embeds.
- This repository is public. Never commit passwords, API keys, tokens, connection strings, personal contact details, or internal project material.

## Contact

Questions about DDS services: [info@digitaldataservices.com](mailto:info@digitaldataservices.com).

Copyright © 1999-2026 Digital Data Services, Inc. All rights reserved. Esri, ArcGIS, and VertiGIS Studio are trademarks of their respective owners.
