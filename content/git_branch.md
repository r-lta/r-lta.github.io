Title: git branch
Date: 2025-08-31
Category: Git
Tags: basic-info

`git branch [branch_name]`

Create a new branch.
## Branch creation
With `git branch develop`, the reference (`refs/heads/develop`) and reflog (`logs/refs/heads/develop`) for the `develop` branch are created.

The reference points to the same commit as `HEAD`, while the reflog reads
```text
0000000000000000000000000000000000000000 651c4218bcac631691d42e3fb03112e0ca0fccbf Example User <user@example.com> 1756297488 +0900	branch: Created from main
```
## Branch listing
The command `git branch` lists the existing local branches:
```text
  develop
* main
```
The branch that `HEAD` currently points to is starred.