Title: git add
Date: 2025-09-05
Modified: 2026-02-15
Category: Git
Tags: basic-info

`git add`

Two things happen.

1. A blob is created at `objects`.
2. `index` is updated.

## blob
When a new file is added, Git creates a blob from the following byte string:
```
"blob " + <size of contents in bytes> + "\0" + <contents>
```
The SHA-1 (or SHA-256 depending on the setting) hash of this byte string is used to create the directory and the file name:

- The first 2 hex chars becomes the directory.
- The remaining 38 hex chars becomes the file name.

For example:
```
|-- objects
|   |-- 57
|   |   `-- 16ca5987cbf97d6bb54920bea6adde242d87e6
```
The content of the file is the byte string compressed by zlib.

Q: How do you inspect the blob?

A:
- Find the hash of the added file: `git hash-object foo`
- Print the content of the blob: `git cat-file -p 5716ca...`
- Print the type of the blob: `git cat-file -t 5716ca...`
- Print the size of the blob: `git cat-file -s 5716ca...`

Q: What about the file name? What if there are two files with the same content?

A: The file name is stored in `index`. If two files share the content, there will be two entries in the index with the same hash.
## index
`index` contains the information of the staged files, in the following format:
```
[metadata][hash][path][padding]
```
To make this concrete, let's add a file with name `foo` and inspect `index` with `xxd -g 1 -c 16 .git/index`:
```
00000000: 44 49 52 43 00 00 00 02 00 00 00 01 68 ac f6 b0  DIRC........h...
00000010: 03 c1 fa f8 68 ac f6 b0 03 c1 fa f8 01 00 00 10  ....h...........
00000020: 02 b5 e6 b3 00 00 81 a4 00 00 01 f6 00 00 00 14  ................
00000030: 00 00 00 04 57 16 ca 59 87 cb f9 7d 6b b5 49 20  ....W..Y...}k.I 
00000040: be a6 ad de 24 2d 87 e6 00 03 66 6f 6f 00 00 00  ....$-....foo...
00000050: 00 00 00 00 ec 7e 89 70 0d 16 6c d8 61 35 6b 94  .....~.p..l.a5k.
00000060: 34 e6 c3 eb e6 e7 48 cb                          4.....H.
```
(A human-readable information can be printed with `git ls-files --stage` and `git-ls-files --debug`.)

The first 12 bits is the header of the file:

- `DIRC`: "Magic number" telling Git that this is an index file.
- `00 00 00 02`: Version of the index format (versions 3 and 4 are also supported).
- `00 00 00 01`: Number of entries in the index.

Q: Why `DIRC`?

A: "DIRC" is short for "directory cache".

This is followed by 62 bytes of staging information about `foo`:

- `68 ac f6 b0 03 c1 fa f8`: Creation time (seconds + nanoseconds).
- `68 ac f6 b0 03 c1 fa f8`: Modification time (same as above in this case).
- `01 00 00 10`: Number of the device that the file resides in (`dev`).
- `02 b5 e6 b3`: Identification number for the file within the device (`ino`).
- `00 00 81 a4`: File type and permissions. `0100644` in octal: `0100` means regular file, `644` means `rw-r--r--`.
- `00 00 01 f6`: UID of the owner.
- `00 00 00 14`: GID of the owner.
	- For Windows, UID and GID are replaced with fake values.
- `00 00 00 04`: Size of the file in bytes.
- `57 16 ca 59 87 cb f9 7d 6b b5 49 20 be a6 ad de 24 2d 87 e6`: The hash of the blob, as seen before.
- `00 03`: Packed bits with the following information:
	- Bits 0-11: Length of the path that follows.
	- Bits 12-13: Merge stage. (Not explained because merging is not covered yet.)
	- Bits 14-15: Reserved for future formats.

Q: Why save all this metadata?

A: For Git to quickly check if a file has been modified, in which case the file has to be re-hashed. Otherwise, Git would have to re-hash all files every time.

Then we have the variable-length file name (relative to the repo root) part terminated by NUL: `66 6f 6f 00`.

Finally, NUL padding ensures that the next entry starts at an 8-byte boundary: `00 00 00 00 00 00`.

The last 20 bytes are the SHA-1 hash of the index (not including the hash itself), acting as the checksum.