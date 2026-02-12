# pages

Personal blog / developer notes, hosted on [GitHub Pages](https://hirisave.github.io/pages/). Built with [Hugo](https://gohugo.io/) and the [Maverick](https://github.com/hirisave/maverick) theme.

---

## What you need to run it locally

- **Hugo** (extended version recommended), e.g. 0.135+
- **Git** (the theme is a submodule)

### Install Hugo (macOS)

```bash
brew install hugo
```

Check:

```bash
hugo version
```

---

## Run locally

1. **Clone the repo (with the theme submodule):**

   ```bash
   git clone --recurse-submodules https://github.com/hirisave/pages.git
   cd pages
   ```

   If you already cloned without submodules:

   ```bash
   git submodule update --init --recursive
   ```

2. **Start the dev server:**

   ```bash
   hugo server -D
   ```

   `-D` includes draft posts. Omit it to hide drafts.

3. **Open in the browser:**

   - Site: **http://localhost:1313/pages/**
   - Posts: http://localhost:1313/pages/posts/
   - About: http://localhost:1313/pages/about/

   Hugo will use a base URL for localhost, so links stay on your machine. The server watches for changes and reloads automatically.

4. **Stop the server:** `Ctrl+C` in the terminal.

---

## Build for production

To generate the static site (e.g. for GitHub Pages):

```bash
hugo
```

Output goes to the `public/` directory. The repo’s GitHub Action (see `.github/workflows/deploy.yml`) builds from this and deploys to GitHub Pages.

---

## Project layout (quick reference)

| Path            | Purpose                          |
|-----------------|-----------------------------------|
| `content/`      | Markdown content (home, about, posts) |
| `content/posts/`| Blog posts                        |
| `config.toml`   | Site config (title, baseURL, menu) |
| `themes/maverick/` | Theme (submodule); we override layouts/partials and `_extra.scss` here |
| `public/`       | Generated site (gitignored or deployed) |

New posts: add a `.md` file under `content/posts/` with front matter (`title`, `date`, `tags`).
