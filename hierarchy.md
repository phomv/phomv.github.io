---
title: How the date hierarchy works
---

# How the date hierarchy works

phomv derives a per-file destination path from a single timestamp. The result
is always:

```
<dest>/YYYY/YYYY_MM/YYYY_MM_DD/<original-filename>
```

The interesting question is *where the timestamp comes from*.

## Timestamp resolution order

For each file, phomv tries these sources in order and uses the first one that
returns a usable date:

1. **EXIF `DateTimeOriginal`.** This is the camera's record of when the shutter
   actually fired. It's the most accurate signal and survives copies, exports,
   and renames.
2. **File mtime (modification time).** Used as a fallback for files that have
   no EXIF block or whose EXIF date is unreadable (e.g. screenshots, scans,
   exports from certain editors).
3. **`Unknown/`.** If neither source produces a date, the file is placed at
   `<dest>/Unknown/<original-filename>` instead of failing the run. This makes
   bulk imports resilient — a few bad files don't abort the job.

Each `Result` emitted by the worker pool records which `TimeSource` was used
(`EXIF`, `mtime`, or `Unknown`), so you can audit a run after the fact.

## Supported formats

EXIF parsing covers the formats most cameras and phones produce:

`.jpg`, `.jpeg`, `.png`, `.heic`, `.cr2`, `.nef`, `.arw`, `.dng`, `.tif`, `.tiff`.

Other extensions are skipped during the directory walk.

## Collisions and idempotency

Two situations can produce the same destination path:

- **Same file, same place.** phomv computes a SHA-256 of both the source and
  the existing destination file. If they match, the source is skipped.
- **Different file, same name.** A suffix is added before the extension:
  `IMG_001.jpg` → `IMG_001_1.jpg`, then `IMG_001_2.jpg`, etc. Names are
  resolved deterministically; re-runs converge.

This means re-running phomv against the same source is safe and cheap: only
new or changed files are written.

## The `Unknown/` bucket

A file lands in `<dest>/Unknown/` when:

- It has no EXIF date *and*
- Its mtime can't be read (very rare; usually a permissions issue).

The same collision and idempotency rules apply inside `Unknown/`. Once you've
identified the right date for a file, you can move it manually into the
appropriate `YYYY/YYYY_MM/YYYY_MM_DD/` directory — phomv won't fight you on
re-runs.

[← Back to home](./)
