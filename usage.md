---
title: Usage
---

# Usage

phomv has two commands: `copy` (non-destructive) and `move` (destructive). Both
take a source directory and a destination root and lay out the destination as
`YYYY/YYYY_MM/YYYY_MM_DD/<original-filename>`.

## Quick start

```sh
# Preview what a copy would do — no disk writes
phomv copy --src ~/Pictures/import --dest ~/Pictures/library --dry-run

# Actually copy, with 8 workers
phomv copy -s ~/Pictures/import -d ~/Pictures/library -w 8

# Move from an SD card; empty source dirs are cleaned up
phomv move -s /mnt/sdcard -d ~/Pictures/library

# Show version
phomv version
```

## Flags

| Flag            | Default | Description                              |
| --------------- | ------- | ---------------------------------------- |
| `-s, --src`     | —       | Source directory (required)              |
| `-d, --dest`    | —       | Destination directory (required)         |
| `-n, --dry-run` | `false` | Simulate execution without touching disk |
| `-w, --workers` | `4`     | Number of concurrent workers             |
| `-v, --verbose` | `false` | Enable debug logging                     |

`--src` and `--dest` must be separate trees: phomv refuses to run if they are the
same directory or one is inside the other (symlinks are resolved), since it
would otherwise re-process files it just wrote. Import from a sibling folder,
as in the examples above.

## What gets written where

For a photo with EXIF `DateTimeOriginal` = `2024:08:13 17:42:01`:

```
~/Pictures/library/
└── 2024/
    └── 2024_08/
        └── 2024_08_13/
            └── IMG_0421.jpg
```

If the same file is run again, phomv hashes both copies (SHA-256) and skips
the import. If a different file lands on the same name, phomv writes
`IMG_0421_1.jpg`, `IMG_0421_2.jpg`, etc.

See [How the date hierarchy works](hierarchy.html) for the details on EXIF
extraction, the mtime fallback, and the `Unknown/` bucket.

## copy vs move

- `copy` reads the source and writes to the destination. The source is never
  modified.
- `move` does the same, but on success removes the source file. After all
  copies complete, empty source directories are cleaned up.
- Both perform an atomic-ish copy via temp file + rename, with a cross-device
  fallback for `move` when source and destination live on different filesystems.

## Recommended workflow

1. **Dry-run first.** `--dry-run` logs every planned action, including the
   `_1`, `_2` suffixes and duplicate skips a real run would produce; review the output.
2. **Copy, don't move, the first time.** Once the destination layout looks
   right, re-run with `move` (or just delete the source).
3. **Re-run freely.** Idempotency (SHA-256 compare) makes re-runs safe — only
   new or changed files are written.

## Exit status and warnings

The run ends with a summary line (`discovered`, `processed`, `skipped`,
`unknown`, `failed`, `walk_errors`). phomv exits nonzero if any file failed or
any folder could not be read. Unreadable folders (permissions, I/O errors) are
logged as `unreadable, not scanned` warnings with their path, so nothing is
skipped silently. In `move`, a duplicate whose source can't be deleted counts
as failed.

[← Back to home](./)
