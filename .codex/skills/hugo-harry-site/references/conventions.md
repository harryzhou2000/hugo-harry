# hugo-harry conventions

## Repo map

```text
hugo-harry/
├── hugo.toml                    # site config
├── config/_default/             # theme/menu/markup/permalinks/related
│   ├── params.toml
│   ├── menu.toml
│   ├── markup.toml
│   ├── permalinks.toml
│   └── related.toml
├── content/
│   ├── _index.md                # home page
│   ├── page/                    # functional pages (in-repo)
│   │   ├── archives/
│   │   ├── links/
│   │   └── search/
│   ├── post/HarrysNotes/        # technical notes (submodule)
│   └── blog/HarrysShare/        # casual shares (submodule)
├── layouts/partials/article/components/math.html  # MathJax loader
├── assets/scss/                 # SCSS overrides
├── themes/
│   ├── stack                    # active theme (submodule)
│   └── hugo-book                # inactive (submodule)
└── .github/workflows/hugo.yaml  # deploy to GitHub Pages
```

## Submodule reference

| Path | URL | Branch | Update command |
| --- | --- | --- | --- |
| `themes/stack` | `https://github.com/CaiJimmy/hugo-theme-stack` | tag v3.29.0 (detached) | `git submodule update --remote themes/stack` only if intentional |
| `themes/hugo-book` | `https://github.com/alex-shpak/hugo-book` | detached | rarely update |
| `content/post/HarrysNotes` | `https://github.com/harryzhou2000/HarrysNotes` | `main` | `git submodule update --remote content/post/HarrysNotes` |
| `content/blog/HarrysShare` | `https://github.com/harryzhou2000/HarrysShare` | `main` | `git submodule update --remote content/blog/HarrysShare` |

The CI build always runs:

```bash
git submodule update --remote content/post/HarrysNotes
git submodule update --remote content/blog/HarrysShare
```

## Frontmatter samples

### Regular post (single markdown file)

```markdown
---
title: ArrayTransformer bench
date: 2025-03-13T22:31:56+08:00
type: post
categories: ["DNDSR"]
tags: ["HPC", "MPI"]
---
```

### Leaf bundle

```markdown
---
title: DES Tests
date: 2025-12-25T15:57:00+08:00
type: post
image: icon.png
tags: ["DNDSR", "DES", "test-case"]
---
```

Place resources (`icon.png`, etc.) in the same directory as `index.md`.

### Casual blog post

```markdown
---
title: Chiikawa 1
date: 2025-01-24T21:41:03+08:00
type: post
categories: ["Share"]
tags: ["Chiikawa"]
image: https://harryzhou2000.github.io/resources-0/chiikawa-cover.png
---
```

### Page

```markdown
---
menu:
  main:
    name: Harry's Notes
    weight: 4
    params:
      icon: books
title: Harry's Notes
---
```

## Permalinks and URLs

Configured in `config/_default/permalinks.toml`:

```toml
post = "/p/:slug/"
page = "/:slug/"
```

If no `slug` is set, Hugo derives it from the filename or bundle directory name.

## Markdown / math notes

`config/_default/markup.toml` enables:

- `goldmark.renderer.unsafe = true` — raw HTML allowed.
- `goldmark.extensions.passthrough` for LaTeX.
  - Block delimiters: `\[ ... \]` and `$$ ... $$`
  - Inline delimiters: `\( ... \)`
- Table of contents: `startLevel = 2`, `endLevel = 4`, `ordered = true`.
- Syntax highlighting with line numbers.

Example:

```markdown
$$
C_L = 0.5 \pm 0.0001
$$

Inline \( E = mc^2 \) and $\Delta x$.
```

## Comments

Comments are enabled via giscus in `params.toml`:

```toml
[comments.giscus]
repo = "harryzhou2000/hugo-harry"
category = "Announcements"
mapping = "pathname"
```

Do not change these values unless explicitly asked.

## Images and assets

- **Leaf bundle:** put image next to `index.md` and reference by filename: `![alt](icon.png)`.
- **External/absolute URL:** use full URL in `image` or markdown.
- **Site-wide assets:** `assets/img/` is processed by Hugo's asset pipeline; `static/` is copied as-is.
- **Favicon:** served from `/favicon.jpeg`.

## Media storage

When the user provides a resources repo (e.g., `~/projects/resources-0` or `https://github.com/harryzhou2000/resources-0`) and explicitly authorizes media uploads for the current task:

1. Place the image/video in the resources repo under a stable, year/post-based path.
2. Add, commit, and push to the resources repo.
3. In the Hugo post, reference the raw GitHub content URL:
   `https://raw.githubusercontent.com/harryzhou2000/resources-0/main/<path>`

Avoid adding large binary files to `content/post/HarrysNotes` or `content/blog/HarrysShare` unless the user asks for a leaf bundle with local assets.


## Shortcodes

Known site-specific shortcode (defined in theme or layouts):

- `bilibili`: `{{< bilibili BV17nmKY6E5L >}}`

Check `layouts/shortcodes/` or the theme for additional shortcodes before inventing new ones.

## Build/deploy

**Authorization:** do not trigger, modify, or cancel the GitHub Pages deployment workflow without explicit user approval. Local builds and previews do not require authorization.

GitHub Actions workflow `.github/workflows/hugo.yaml`:

- Trigger: push to `ghpages` or manual dispatch.
- Hugo version: `0.141.0` extended.
- Commands:
  ```bash
  hugo --gc --minify --baseURL "$BASE_URL/"
  ```

Local development:

```bash
hugo serve -D        # include drafts
hugo server --bind 0.0.0.0 -D
```

## License line

Default article license in `params.toml`:

```toml
[article.license]
enabled = true
default = "Licensed under CC BY-NC-SA 4.0"
```

Do not remove unless explicitly asked.
