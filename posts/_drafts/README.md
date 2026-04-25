# Drafts

Posts in progress live here. They are committed to git (so they sync across your devices via push/pull) but Quarto excludes them from the blog listing, so they never appear on the live site.

## Why drafts don't get published

Two layers protect you:

1. **The folder name starts with `_`.** Quarto skips any directory whose name begins with an underscore. So `posts/_drafts/<slug>/index.md` is invisible to the build.
2. **Use `draft: true` in the front matter as well.** Belt and suspenders — if you ever move a draft outside `_drafts/` by accident, this flag still hides it from the listing.

## How to start a draft

```
posts/_drafts/my_new_post/index.qmd
```

with front matter like:

```yaml
---
title: "My new post"
date: "2026-04-25"
draft: true
---
```

You can use either `.md` (plain markdown) or `.qmd` (Quarto, with executable code chunks and richer math).

## How to publish a draft

1. Move the folder out of `_drafts/`:
   ```
   mv posts/_drafts/my_new_post posts/my_new_post
   ```
2. Remove `draft: true` from the front matter.
3. Commit and push to `main`.

## Editing across devices

Just `git pull` on each device before you start writing, and `git push` when you stop. Drafts go along for the ride.
