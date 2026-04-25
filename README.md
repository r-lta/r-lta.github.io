# r-lta.github.io

Source for [r-lta.github.io](https://r-lta.github.io). Built with [Quarto](https://quarto.org); deployed to GitHub Pages by the workflow at `.github/workflows/publish.yml` on every push to `main`.

## Repo layout

```
_quarto.yml          site-wide config (title, theme, what to render)
index.qmd            home page (lists all posts)
about.qmd            about page
styles.css           custom CSS (currently empty)
posts/
  _metadata.yml      defaults applied to every post
  _drafts/           in-progress posts; not published (see below)
  <slug>/index.md    one folder per published post
.github/workflows/publish.yml   GitHub Actions: render and deploy on push to main
_site/               generated HTML (gitignored, built locally or by CI)
```

## Writing a new post

1. Create `posts/<slug>/index.qmd` (or `.md` for plain Markdown — `.qmd` adds executable code chunks and richer maths).
2. Top of the file:
   ```yaml
   ---
   title: "Post title"
   date: "YYYY-MM-DD"
   categories: ["Category", "tag1", "tag2"]
   ---
   ```
3. Write the body in Markdown below the closing `---`.
4. Preview locally: `quarto preview` (opens a live-reloading browser).
5. Commit and push (see "Publishing" below). GitHub Actions rebuilds the site within ~1-2 min.

## Drafts

A draft is a post you don't want published yet, but do want to sync across your devices.

- Put it in `posts/_drafts/<slug>/index.qmd` instead of `posts/<slug>/`.
- Optionally add `draft: true` to the front matter as a second line of defence.
- Two layers prevent it from being published: (a) Quarto skips any directory whose name begins with `_`, and (b) the `draft: true` flag, if set, also hides the post from listings.
- To publish: move the folder out of `_drafts/` and remove `draft: true`. Commit and push.

## Publishing — single device

```
git add -A
git commit -m "new post: <slug>"
git push
```

That's it. The Action runs; the site updates.

## Publishing — multiple devices

The first time on a new device:

```
git clone git@github.com:r-lta/r-lta.github.io.git
cd r-lta.github.io
```

Every session:

```
git pull           # at the start: grab anything the other device pushed
# ...edit posts, run `quarto preview` if you want to see them...
git add -A
git commit -m "..."
git push           # at the end: send your changes back
```

Rule of thumb: **pull first, push last**. Always commit before switching devices, even drafts. If you leave a device with uncommitted edits and edit the same file on another device, git will need you to manually merge them.

## Local preview

```
quarto preview
```

Starts a live-reloading server (default port). Edits to `.qmd` / `.md` files re-render automatically.

To do a one-shot static render without the server:

```
quarto render
```

Output goes to `_site/`. Both `_site/` and `.quarto/` are gitignored.

## How deploys work

Every push to `main` triggers `.github/workflows/publish.yml`:

1. Checks out the repo on a GitHub-hosted Linux runner.
2. Installs Quarto.
3. Runs `quarto render` to build `_site/`.
4. Uploads `_site/` as a GitHub Pages artifact and deploys it.

You can watch a run at <https://github.com/r-lta/r-lta.github.io/actions>. Green check → live site updated.

## Rollback

If a push breaks the live site:

```
git revert HEAD
git push
```

This creates a new commit that undoes the previous one. Pushing it triggers the Action again, restoring the prior state of the site within ~1-2 min.
