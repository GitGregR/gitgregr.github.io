# Academic Portfolio

A clean, JSON-driven academic portfolio website. All content lives in `data/` — the HTML/CSS/JS never needs to change.

**Live site:** https://gitgregr.github.io/gwr-website/

---

## Project Structure

```
academic-portfolio/
├── index.html              ← Entry point (structure only, no content)
├── css/
│   └── style.css           ← All styles (design tokens at top)
├── js/
│   └── main.js             ← Fetches JSON and renders all sections
├── data/
│   ├── profile.json        ← Name, bio, photo path, social links
│   ├── publications.json   ← Working papers + published papers
│   ├── news.json           ← News / updates (sorted by date automatically)
│   └── activities.json     ← Teaching, service, talks, software
├── assets/
│   └── photo.jpg           ← YOUR profile photo (replace this file)
├── docs/
│   └── cv.pdf              ← YOUR CV (replace this file)
└── README.md               ← This file
```

---

## Quickstart (Local Development)

Because the site fetches JSON files via `fetch()`, you must serve it from a local server — not open `index.html` directly from the file system.

**Option 1 — Node (recommended):**
```bash
npx serve .
# → http://localhost:3000
```

**Option 2 — Python:**
```bash
python3 -m http.server 8080
# → http://localhost:8080
```

---

## Updating Your Content

### Profile, Bio, Links → `data/profile.json`
Change any field directly. The `bio` field accepts an **array of strings**, where each string becomes its own paragraph.

```jsonc
{
  "name": "Your Full Name",
  "initials": "YN",               // shown if photo fails to load
  "title": "Your Title",
  "affiliation": "Your Institution",
  "bio": [
    "First paragraph of your bio.",
    "Second paragraph."
  ],
  "photo": "assets/photo.jpg",   // replace the file, keep this path
  "links": {
    "cv": "docs/cv.pdf",
    "email": "you@university.edu",
    "scholar": "https://scholar.google.com/...",
    "github": "https://github.com/...",
    "bluesky": "https://bsky.app/...",
    "linkedin": "https://linkedin.com/...",
    "orcid": "https://orcid.org/..."
  }
}
```

---

## ★ How to Promote a Paper from Working Paper → Published

This is a three-step process entirely inside `data/publications.json`.

### Step 1 — Find the paper in `working_papers`

```json
"working_papers": [
  {
    "id": "wp1",
    "title": "My Paper Title",
    "authors": ["Your Name", "Co-Author"],
    "year": 2025,
    "abstract": "...",
    "tags": ["Tag1", "Tag2"],
    "pdf_link": "#",
    "preprint_link": "https://osf.io/...",
    "status": "Under Review at Nature Human Behaviour"
  }
]
```

### Step 2 — Cut that object from `working_papers` and paste it into `published`

No re-typing needed — just move the entire `{ … }` block.

### Step 3 — Add the publication details and clean up working-paper fields

```json
"published": [
  {
    "id": "pub3",                  // ← give it a new id
    "title": "My Paper Title",
    "authors": ["Your Name", "Co-Author"],
    "year": 2025,
    "journal": "Nature Human Behaviour",   // ← ADD
    "volume": "9",                         // ← ADD
    "issue": "3",                          // ← ADD (optional)
    "pages": "112–130",                    // ← ADD
    "doi": "10.1038/s41562-025-00000-0",   // ← ADD
    "doi_link": "https://doi.org/...",     // ← ADD
    "abstract": "...",
    "tags": ["Tag1", "Tag2"],
    "pdf_link": "#",                       // ← update to real PDF URL when available
    "award": null                          // ← set to "Best Paper Award" etc. if applicable
    // REMOVE: "preprint_link", "status"
  }
]
```

Save the file and refresh the page — the card automatically moves to the Published section with the correct blue accent, journal line, and DOI link.

---

## Adding News Items → `data/news.json`

Prepend a new object to the array. Items are **sorted by date automatically**, so order in the file doesn't matter.

```json
{
  "date": "2025-12-01",
  "text": "Your news text here.",
  "link": "https://optional-link.com",
  "link_text": "Read more"
}
```
Set `"link": null` and `"link_text": null` if there's no associated URL.

---

## Adding Activities → `data/activities.json`

Each top-level object is a category (Teaching, Service, etc.). Add a new item inside the `items` array:

```json
{
  "title": "Talk Title",
  "organization": "Host Institution",
  "period": "Month Year",
  "description": "Short description of the activity.",
  "link": "https://optional-link.com"  // or null
}
```

To add a whole new category, add a new top-level object:

```json
{
  "category": "New Category",
  "icon": "🔬",
  "items": [ … ]
}
```

---

## Deployment

The site is static HTML/CSS/JS with no build step. Deploy to any static host:

- **GitHub Pages** — push to `main`, enable Pages in repo settings
- **Vercel** — `vercel deploy` from project root
- **Netlify** — drag and drop the folder onto netlify.com/drop
- **Any web server** — upload all files maintaining the folder structure

---

## Customisation Tips

- **Colors & fonts** — all design tokens are CSS custom properties at the top of `css/style.css` under `:root { … }`. Changing `--blue` changes every accent across the site.
- **Sidebar width** — adjust `--sidebar-w` in the same block.
- **Section order** — reorder the `<section>` blocks in `index.html` and the nav links in the sidebar.
- **Add a new section** — add a `<section id="new">` in HTML, a matching nav `<a>`, a new JSON file, a fetch in `loadAll()`, and a render function in `main.js`.
