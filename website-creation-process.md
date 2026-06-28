# How This Website Was Created

A complete record of how this academic portfolio site was built, from initial prompt to live deployment.

---

## 1. Original Prompt (Claude.ai Chat)

The site was generated from the following prompt given to Claude:

> You are an expert Frontend Developer and UI/UX Designer specializing in building clean, modern, responsive websites for academics and researchers. Your task is to build a complete website based on a reference site the user likes, a set of custom preferences, and a specific data-driven architecture.
>
> **1. Architectural Requirement: JSON-Driven Content**
> Because the user is an academic who needs to frequently update their content (publications, working papers, news), you MUST decouple the content from the HTML structure.
> - Implement a structured JSON data file system (e.g., `data/publications.json`, `data/news.json`, `data/profile.json`).
> - The UI must fetch and dynamically render content from these JSON files.
> - Ensure the publication schema distinguishes between "Working Papers" and "Published Papers" so they render in separate, appropriate sections.
> - Include a template or clear documentation on how the user can manually update these JSON files to change the site's content.
>
> **2. Design & Aesthetics**
> - Inspiration: Analyze the layout, typography, spacing, and color palette of the reference URLs below. Emulate their professional, clean aesthetic while adapting it to an academic context.
> - Academic Needs: Ensure high readability, elegant typography for abstracts, and prominent links to PDFs, Google Scholar, or institutional profiles.
> - Responsiveness: The site must be fully responsive (mobile, tablet, and desktop).
>
> **3. User Inputs**
> - Reference URLs to Emulate: https://kavishshah.vercel.app/, https://ronalderobertson.com/, https://www.matthewdeverna.com/
> - Specific Design/Feature Notes: Do not have extensive research papers to include, instead supplement with activities section. Do not have academic data.
>
> **4. Expected Output**
> 1. The Project Structure: A tree view of the files.
> 2. The JSON Schema: Sample JSON files filled with placeholder academic data (e.g., 2 working papers, 2 published papers, 3 news items).
> 3. The Source Code: Complete, production-ready HTML/CSS/JS that parses the JSON and renders the UI perfectly.
> 4. Update Guide: A brief, 3-step text explanation showing the academic exactly how to move a paper from "Working Paper" to "Published" inside the JSON file.

Claude generated the complete site (HTML, CSS, JS, and all JSON files) and delivered it as a downloadable `files.zip`.

---

## 2. Environment Setup (WSL on Windows)

All work was done inside **WSL (Windows Subsystem for Linux)** on a Windows machine.

### Install Claude Code CLI

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

### Create the project directory

```bash
mkdir repos
cd repos
mkdir website
```

### Move the zip from Windows Downloads into WSL

```bash
cp /mnt/c/Users/gregu/Downloads/files.zip repos
mv repos/files.zip website/
cd website
```

---

## 3. Install GitHub CLI (`gh`) on WSL

Before pushing to GitHub, the `gh` CLI was needed. Install it with:

```bash
(type -p wget >/dev/null || (sudo apt update && sudo apt install wget -y)) \
  && sudo mkdir -p -m 755 /etc/apt/keyrings \
  && out=$(mktemp) && wget -nv -O$out https://cli.github.com/packages/githubcli-archive-keyring.gpg \
  && cat $out | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
  && sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
  && sudo mkdir -p -m 755 /etc/apt/sources.list.d \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
     | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
  && sudo apt update \
  && sudo apt install gh -y
```

Then authenticate:

```bash
gh auth login
# Choose: GitHub.com → HTTPS → Login with a web browser
```

---

## 4. First Claude Code Session — Extracting and Deploying

Launched Claude Code inside the project directory:

```bash
claude
```

**Prompt given to Claude Code:**
> "We are building a website, @files.zip is our initial setup, what else do we need to do to get this up on github?"

Claude Code:
1. Inspected `files.zip` and understood the project structure
2. Extracted the zip and organized files into the correct directory layout: `css/`, `js/`, `data/`, `assets/`, `docs/`
3. Created a `.gitignore` (excludes `files.zip`, `.DS_Store`, etc.)
4. Initialized a git repo on the `main` branch
5. Staged all files
6. Noted that `assets/photo.jpg` and `docs/cv.pdf` were placeholder (empty) files that would need replacing

### Git remote and first push

A repo named `gwr-website` was created on GitHub, then:

```bash
git remote add origin https://github.com/gitgregr/gwr-website.git
git config --global user.email "57818959+GitGregR@users.noreply.github.com"
git config --global user.name "GitGregR"
git commit -m "initial commit"
git push -u origin main
```

**Initial commit** (`7b2a5e3`, Jun 27 13:43) included:
- `index.html` — site structure (184 lines)
- `css/style.css` — all styles with CSS custom properties (765 lines)
- `js/main.js` — fetches JSON and renders all sections (438 lines)
- `data/profile.json` — name, bio, photo path, social links
- `data/publications.json` — working papers + published papers schema
- `data/news.json` — news/updates
- `data/activities.json` — teaching, service, talks, software
- `assets/photo.jpg` — empty placeholder
- `docs/cv.pdf` — empty placeholder
- `README.md` — full documentation

---

## 5. Post-Launch Customisation (Claude Code Sessions)

### ORCID link removal (Jun 27)

The generated profile included an ORCID link. Since there was no ORCID to add yet:

**Prompt:** "That's installed, lets get rid of the orc id"

Claude explained JSON has no native comment syntax and offered three options:
1. Prefix the key to `"_orcid"` — JS ignores unknown keys
2. Set it to `null` — JS skips falsy values
3. Delete it entirely

**User chose option 2** (`"orcid": null`).

Claude verified that `main.js` line 180 already used `.filter(s => links[s.key])` — a null value is falsy, so the button would be automatically excluded. When the ORCID button still appeared, the filter logic was fixed to explicitly handle null.

**Commit:** `af667cf` — "Set orcid link to null"

---

### Profile, bio, and link updates (Jun 27, 14:29)

Personal information was filled in: real name, bio paragraphs, institution, and working social/web links.

**Commit:** `710139f` — "updated profile, bio, and website links"

---

### Email: click-to-copy instead of mailto (Jun 27, 14:38)

**Prompt:** "Get rid of the mailto link and put my email at the end of the bio, and make it so clicking on the email will copy it to the viewer clipboard"

The sidebar mailto button was removed. The email address was moved into the bio area with JavaScript clipboard copy functionality on click.

**Commit:** `7a8bfe1` — "Replace mailto button with click-to-copy email in bio"

---

### Side-by-side CV and email buttons (Jun 27, 14:47)

**Prompt:** "Place the cv and email side-by-side, while maintaining the copy functionality of the email address"

The CV download button and the click-to-copy email were laid out in a flex row in the About section CTA.

**Commit:** `54a4431` — "Move email alongside CV button in about CTA"

---

### README live site link (Jun 27, 14:51)

**Prompt:** "Please add the website name to the README.md"

Added the live GitHub Pages URL to the top of `README.md`.

**Commit:** `0e6b4e3` — "Add live site link to README"

---

## 6. Repo Rename to `gitgregr.github.io` (Jun 28)

The GitHub repository was renamed from `gwr-website` to `gitgregr.github.io` via the GitHub web UI. This activated the special GitHub Pages behaviour for user sites (served from `main` branch root, at `https://gitgregr.github.io/`).

**Prompt to Claude Code:** "I renamed the remote repo to gitgregr.github.io, do we need to change anything?"

Claude confirmed the local git remote URL would need updating and updated the README with the new canonical URL. No other code changes were needed — GitHub Pages automatically detected the rename.

```bash
git remote set-url origin https://github.com/gitgregr/gitgregr.github.io.git
```

**Commit:** `39e2d24` — "Update live site URL for renamed repo (gitgregr.github.io)"

---

## 7. Final Project Structure

```
website/
├── index.html              ← Structure only, no hardcoded content
├── css/
│   └── style.css           ← All styles (CSS custom properties at top)
├── js/
│   └── main.js             ← Fetches JSON, renders all sections
├── data/
│   ├── profile.json        ← Name, bio, photo path, social links
│   ├── publications.json   ← Working papers + published papers
│   ├── news.json           ← News/updates
│   └── activities.json     ← Teaching, service, talks, software
├── assets/
│   └── photo.jpg           ← Profile photo
├── docs/
│   └── cv.pdf              ← CV
├── .gitignore
├── README.md               ← Content update guide
└── CREATION_GUIDE.md       ← This file
```

**Live site:** https://gitgregr.github.io/

---

## 8. Tools Used

| Tool | Purpose |
|------|---------|
| Claude.ai (chat) | Generated the initial site from the prompt |
| Claude Code CLI (`claude`) | All subsequent edits, git operations |
| WSL (Ubuntu on Windows) | Development environment |
| GitHub + GitHub Pages | Hosting (free, no build step needed) |
| `gh` CLI | GitHub authentication and repo management from terminal |
