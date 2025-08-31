Title: git commit
Date: 2025-08-28
Category: Git
Tags: basic-info

`git commit`

Commits the information about the current staging area and updates the branch pointer.

If we create a file `foo` with content `bar` in an empty repo and commit it, the `.git` folder looks as follows:
```text
.git
|-- COMMIT_EDITMSG <- Created by commit (where you write the commit message)
|-- config
|-- description
|-- HEAD
|-- hooks
|   |-- ...
|-- index
|-- info
|   `-- exclude
|-- logs
|   |-- HEAD <- Created by commit
|   `-- refs
|       `-- heads 
|           `-- main
|-- objects
|   |-- 57 <- Created by add
|   |   `-- 16ca5987cbf97d6bb54920bea6adde242d87e6
|   |-- 65 <- Created by commit
|   |   `-- 1c4218bcac631691d42e3fb03112e0ca0fccbf
|   |-- 6a <- Created by commit
|   |   `-- 09c59ce8eb1b5b4f89450103e67ff9b3a3b1ae
|   |-- info
|   `-- pack
`-- refs
    |-- heads
    |   `-- main <- Created by commit
    `-- tags

```
## reference
The `refs/heads/` directory contains the hash (reference) of the latest commit object of a given branch. Here, the content of the `refs/heads/main` file is
```text
651c4218bcac631691d42e3fb03112e0ca0fccbf
```
## log
The `log/` directory contains the reflogs (reference log), created when the `logallrefupdates` config parameter is set to `true` (default). The reflogs have the format
```text
<old-hash> <new-hash> <committer> <timestamp> <message>
```
The `logs/HEAD` file contains the history of the commit object hashes that the `HEAD` pointed to. In this case, we have a single entry
```text
0000000000000000000000000000000000000000 651c4218bcac631691d42e3fb03112e0ca0fccbf Example User <user@example.com> 1756288734 +0900	commit (initial): test commit
```
The `logs/refs/heads/main` file contains the analogous log for the `main` branch, which here is the same as `logs/HEAD`.
## commit object
Let's inspect the content of the commit object, in the same way as we inspected the blob object:
```bash
git cat-file -p 651c4218bcac631691d42e3fb03112e0ca0fccbf
```
We find
```text
tree 6a09c59ce8eb1b5b4f89450103e67ff9b3a3b1ae
author Example User <user@example.com> 1756288734 +0900
committer Example User <user@example.com> 1756288734 +0900

test commit
```
Note that the hash(es) for the parent commit(s) are missing because this was our first commit. Here, the hash points to the root tree of the repo.
## tree object
Each tree object provides a snapshot of a directory.

We found the hash for the tree object from inspecting the commit object. Running
```bash
git cat-file -p 6a09c59ce8eb1b5b4f89450103e67ff9b3a3b1ae
```
displays
```
100644 blob 5716ca5987cbf97d6bb54920bea6adde242d87e6 foo
```
If there were a subdirectory in the root directory, then we would have seen a pointer to another tree objects corresponding to the subdirectory.