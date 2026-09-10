---
name: hugo-harry-site
description: Manage and write posts for Harry's personal Hugo site (hugo-harry), including submodule-aware content workflows and Stack theme conventions.
---

# Harry's Hugo Site

Use this skill when working with the Hugo site at /home/harry/projects/hugo-harry — creating or editing posts, pages, images, configuration, or build/deployment steps.

## Authorization and scope

Read-only checks, local previews, and file edits inside the working tree are in scope.

**Do not commit, push, or deploy without explicit user authorization.** This includes:

- `git commit` and `git push` in the main repo or any submodule
- `git submodule update --remote` when the submodule worktree has local changes that could be overwritten
- Triggering, cancelling, or re-running the GitHub Actions workflow that deploys to Pages
- Any command sequence that publishes the site (e.g., building `public/` and uploading it to Pages or another host)
- Editing `.github/workflows/hugo.yaml` if the intent is to change deploy behavior

If a task would change published state or remote refs, ask the user before running it. Drafts and local `hugo serve -D` do not require special authorization.

## Repository basics

- **Generator:** Hugo extended 0.141.0
- **Theme:** Stack (`themes/stack`), with hugo-book kept as a submodule but not active
- **Branch deployed to GitHub Pages:** `ghpages`
- **Content lives partly in submodules:**
  - `content/post/HarrysNotes` → `https://github.com/harryzhou2000/HarrysNotes` (branch `main`)
  - `content/blog/HarrysShare` → `https://github.com/harryzhou2000/HarrysShare` (branch `main`)
  - `content/page` and site config live in the main repo

When a task needs the latest remote content, update the submodules *before* building:

```bash
git submodule update --init --recursive
git submodule update --remote content/post/HarrysNotes content/blog/HarrysShare
```

## Content sections

| Section | Path (repo) | Submodule | Purpose | Permalink |
| --- | --- | --- | --- | --- |
| Post | `content/post/HarrysNotes/...` | HarrysNotes | Longer/technical notes | `/p/:slug/` |
| Blog | `content/blog/HarrysShare/...` | HarrysShare | Casual/shorter shares | `/blog/:slug/` (default) |
| Page | `content/page/...` | — | Theme functional pages | `/:slug/` |

`mainSections = ["post", "blog"]`, so both sections appear on the homepage and archive.

## Creating a post

Create posts inside the correct submodule and section. Use the conventions in [references/conventions.md](references/conventions.md) for frontmatter, bundle layout, and asset handling.

Hugo supports two post forms:

- **Leaf bundle (standard):** `<section>/<post-slug>/index.md`, with any post-local artifacts beside `index.md`. Prefer this form for new posts, including posts that initially use only externally hosted media; it keeps later local assets, downloads, or diagrams naturally scoped to the page.
- **Single file:** `<section>/<post-slug>.md`. Use this only for a genuinely small note with no likely page-local artifacts.

Typical workflow:

1. Decide the section (`post` for technical notes, `blog` for casual shares).
2. Create a leaf bundle under the corresponding submodule path unless the single-file exception clearly fits.
3. Add YAML frontmatter with `title`, `date`, and `type: post`.
4. Put related images in the bundle directory, or use an `image` URL/path in frontmatter.
5. Run `hugo serve -D` locally to preview.

## Writing conventions

- Frontmatter is **YAML** by default. Required fields: `title`, `date`, `type: post` for posts.
- Date timezone: use `+08:00` (e.g., `2026-08-27T16:00:00+08:00`). The Hugo config sets `timeZone = "ROC"`.
- Set `draft: true` while working; remove or set `false` to publish.
- Cover image field is `image`; for leaf bundles put the image in the bundle folder and reference it by filename.
- Math uses MathJax with Goldmark passthrough. Valid delimiters:
  - Block: `$$...$$` or `\[...\]`
  - Inline: `\( ... \)`
- Use the existing `bilibili` shortcode for Bilibili embeds: `{{< bilibili BVID >}}`.

## Media storage policy

The user may provide a separate GitHub repository for images and videos (for example, `~/projects/resources-0` or `https://github.com/harryzhou2000/resources-0`).

If the user **explicitly authorizes** media uploads for a given task, you may:

1. Add the image/video to the resources repo under a stable path (e.g., `resources-0/<year>/<post-name>/`).
2. Commit and push it to GitHub.
3. Reference it in the post with a raw GitHub content URL, such as:
   `https://raw.githubusercontent.com/harryzhou2000/resources-0/main/2026/reaction-experiments/front_marker_time.png`

For the current work, use `~/projects/resources-0` as the resources repo and place assets under `resources-0/<year>/<post-name>/`. Each future media push still requires explicit user authorization.


## Building, previewing, and deploying

### Local

```bash
# Local preview with drafts
hugo serve -D

# Production build (does not deploy by itself)
hugo --gc --minify
```

### GitHub Pages deployment

The CI workflow is `.github/workflows/hugo.yaml`. It deploys to GitHub Pages automatically on pushes to `ghpages`. The workflow:

1. Checks out the `ghpages` branch with recursive submodules.
2. Runs `git submodule update --remote` for both content submodules.
3. Builds with `hugo --gc --minify --baseURL "$BASE_URL/"` using Hugo 0.141.0 extended.
4. Uploads `./public` and deploys to GitHub Pages via `actions/deploy-pages`.

**Do not trigger, skip, cancel, or modify this deployment without explicit user authorization.** Draft content can be previewed locally; do not rely on the CI workflow to preview unpublished drafts.

## Detailed reference

For a full repo map, sample frontmatter, and shortcode list, read [references/conventions.md](references/conventions.md).
