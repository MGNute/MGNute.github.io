# Mike Nute's Website — How to Publish & Update It

This site is built with [Hugo](https://gohugo.io/) using the **PaperMod** theme, and it
publishes automatically to **GitHub Pages**. Once the one-time setup below is done, updating
your live site is just: *edit a file → commit → push*. About a minute later the change is
live at **https://mgnute.github.io/**. You never build or upload anything by hand.

---

## Part 0 — One-time local setup

Your Hugo (0.164, extended) is already the right version for PaperMod, so you only need to
swap the theme folder. In a terminal in your site folder (`~/…/nute_hugo`):

**1. Replace the theme folder with PaperMod** (clears out the old theme too):

```bash
rm -rf themes
git clone --depth 1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

**2. Remove the leftover custom layout** (a scrap from the original setup that overrides the
homepage — it breaks the home page under any theme):

```bash
rm -rf layouts
```

Then test it: `hugo server` → open <http://localhost:1313>. You should see the clean white
PaperMod site.

---

## Part 1 — One-time GitHub setup (do this once)

### 1. Create the repository on GitHub
1. Go to <https://github.com> and sign in (username **MGNute**).
2. Click the **+** in the top-right → **New repository**.
3. Repository name: **`MGNute.github.io`** — this exact name is what makes it a personal
   site served at `https://mgnute.github.io/`. It must match your username.
4. Set it to **Public**. Do **not** add a README, .gitignore, or license — leave it empty.
5. Click **Create repository**.

### 2. Push your site folder to that repository
Easiest if you're not a terminal person: **GitHub Desktop** (<https://desktop.github.com>):
1. **File → Add local repository →** choose your `nute_hugo` folder.
2. If it says the folder isn't a Git repository, click **"create a repository"**, then
   **Create Repository**.
3. Click **Publish repository**. **Uncheck "Keep this code private"**, keep the name
   `MGNute.github.io`, and click **Publish**.

<details>
<summary>Prefer the command line? (optional)</summary>

```bash
git init
git branch -M main
git add .
git commit -m "Initial commit of my Hugo site"
git remote add origin https://github.com/MGNute/MGNute.github.io.git
git push -u origin main
```
</details>

### 3. Turn on GitHub Pages
1. On GitHub, open your `MGNute.github.io` repo → **Settings** → **Pages**.
2. Under **Build and deployment → Source**, choose **GitHub Actions**.
3. Go to the **Actions** tab. If the first run went red because Pages wasn't set to Actions
   yet, open it and press **Re-run all jobs**.
4. Green check (~1–2 min) → your site is live at **https://mgnute.github.io/**.

Setup is now done forever.

---

## Part 2 — Everyday updates

### Add a new blog post
1. In `content/blog/`, make a new file, e.g. `my-first-post.md`.
2. Paste this at the top and edit it:

   ```markdown
   +++
   title = "My First Real Post"
   date = "2026-08-12"
   author = "Mike Nute"
   description = "A one-line summary that shows in the post list."
   draft = false
   +++

   Write your post here in Markdown.
   ```

   Set `draft = true` while writing to keep it off the live site; `false` (or delete the
   line) when ready.

### Edit existing pages
- Homepage intro text: it's the `Content` under `[params.homeInfoParams]` in `config.toml`.
- About page: `content/aboutme.md`
- Projects: `content/projects/`

### Preview locally
Run `hugo server`, open <http://localhost:1313>. Live-reloads as you edit. `Ctrl+C` to stop.

### Publish your changes
- **GitHub Desktop:** type a summary, **Commit to main**, then **Push origin**.
- **Command line:**
  ```bash
  git add .
  git commit -m "Add new blog post"
  git push
  ```

Within ~1 minute the live site updates automatically. Watch it on the repo's **Actions** tab.

---

## How the automatic publishing works (reference)

- `.github/workflows/hugo.yml` tells GitHub to build and deploy on every push. Nothing runs
  on your computer.
- The build is **pinned to Hugo 0.164.0 (extended)** — the same version you run locally.
- **The theme is fetched automatically during each build** (PaperMod). That's why there's no
  `themes/` folder in your repository and why `.gitignore` lists `/themes/`. Your own
  customizations live outside the theme folder — in `config.toml`, in `assets/` (e.g.
  `assets/css/extended/white.css`, which forces the pure-white background), and in `static/`.
- If you clone this repo onto a fresh computer, re-fetch the theme once:
  ```bash
  git clone --depth 1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
  ```

## Customizing PaperMod (quick pointers)
- **Nav links, social icons, homepage intro, site title**: all in `config.toml`.
- **Pure-white background**: comes from `assets/css/extended/white.css`. Delete that file to
  fall back to PaperMod's default (a very light gray page with white cards).
- **Light vs dark**: `defaultTheme = "light"` in `config.toml`. There's also a light/dark
  toggle (the moon icon) in the top bar for visitors.
- **Search**: powered by `content/search.md` plus the `[outputs]` JSON index — leave those in
  place for the Search page to work.
- One old project page (`content/projects/acw_missile_tid.md`) had an embedded tweet that no
  longer loads; it's now a plain link, so nothing breaks.
