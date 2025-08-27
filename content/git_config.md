Title: git config
Date: 2025-08-27
Category: Git
Tags: basic-info

`git config`

Write to one of the three config files.

These files have the following order of precedence (file path for Unix-like):

1. Local: `.git/config` within the repo, accessed by `--local`
2. Global: `~/.gitconfig`, accessed by `--global`
3. System: `/etc/gitconfig`, accessed by `--system`
#### write
Write (or overwrite) to config file: `git config --[scope] foo.bar "baz"`

This adds
```ini
[foo]
	bar = baz
```
but only certain section/key combos are read by Git, e.g. `user.name`.
#### read
Read from config file: `git config --[scope] foo.bar`
#### remove
Remove a key: `git config --[scope] --unset foo.bar`
#### list
See what a config file stores: `git config --[scope] --list`
#### trace
See which file defines a section/key combo: `git config --show-origin foo.bar`