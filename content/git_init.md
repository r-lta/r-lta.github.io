Title: git init
Date: 2025-08-26
Category: Git
Tags: basic-info

`git init`

Creates the directory `.git` with the following tree:
```text
.git
|-- config
|-- description
|-- HEAD
|-- hooks
|   |-- applypatch-msg.sample
|   |-- commit-msg.sample
|   |-- fsmonitor-watchman.sample
|   |-- post-update.sample
|   |-- pre-applypatch.sample
|   |-- pre-commit.sample
|   |-- pre-merge-commit.sample
|   |-- pre-push.sample
|   |-- pre-rebase.sample
|   |-- pre-receive.sample
|   |-- prepare-commit-msg.sample
|   |-- push-to-checkout.sample
|   |-- sendemail-validate.sample
|   `-- update.sample
|-- info
|   `-- exclude
|-- objects
|   |-- info
|   `-- pack
`-- refs
    |-- heads
    `-- tags
```
## HEAD
Contains the current branch. Default: `ref: refs/heads/main`
## description
Description used by GitWeb. Default: `Unnamed repository; edit this file 'description' to name the repository.`

Q: What's GitWeb?

A: Web interface for viewing a repo, now mostly unused.
## config
Local configuration file, which overrides the global one. Default:
```ini
[core]
	repositoryformatversion = 0
	filemode = false
	bare = false
	logallrefupdates = true
	symlinks = false
	ignorecase = true
```
Q: What does each line mean?

A:

- `repositoryformatversion`: Version number of the repo format, always 0 unless Git decides to change the storage format.
- `filemode`: On Unix-like systems, `filemode = true` means that the change in the `+x` (execute) permission is tracked.
- `bare`: True if the repo has no working directory, e.g. a remote repo.
- `logallrefupdates`: Whether the history of branch and head changes (reflogs) is kept.
- `symlinks`: Whether Git supports symbolic links. (Skipped: what symbolic links are)
- `ignorecase`: Whether the file names are treated as case-insensitive.

Q: Why `[core]`?

A: `config` is an INI-style file, which uses square brackets for sections.
Other sections appear for remotes and branches.
## refs
References to commits.

Subdirectories in `refs`:

- `heads`
- `tags`
#### heads
Branch tips.

Q: What are those?

A: Pointer to the most recent commit on the branch.
#### tags
Tag pointers.

Q: What are those?

A: A human-readable label for a commit or an object.
## objects
Contains all Git objects.

Subdirectories in `objects`:

- `info`
- `pack`
#### info
Q: What does this contain?

A: Object database info, e.g. that certain objects are stored in another repo. Normally empty.
#### pack
Q: What does this contain?

A: Packfiles, i.e. compressed collections of objects that are created by Git.
## hooks
Shell scripts for actions, disabled by default. They execute when the extension `.sample` is removed.

Q: When do they execute?

A: The timing of the execution depends on the name of the script.
## info
`exclude` file lives here, which contain repo-specific ignore rules.

Q: Do any other files live here?

A: Yes, for certain advanced features.

Q: What do "ignore rules" do?

A: Prevents files from showing up as "untracked" in `git status`.

Q: How does `exclude` differ from `.gitignore`?

A: `.gitignore` is version-controlled and shared with who clones the repo. `exclude` is specific to the local clone - use for files like `.vscode/`.