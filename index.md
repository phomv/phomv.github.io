---
title: phomv
---

<img class="lockup" src="{{ '/assets/phomv-lockup.png' | relative_url }}" alt="phomv — organize photos, fast">
<p class="tagline">$ phomv ~/DCIM ~/Photos &nbsp;→&nbsp; sorted by EXIF, fast</p>

**phomv** (photo move) is a high-performance CLI that organizes photo directories
into a `YYYY/YYYY_MM/YYYY_MM_DD` hierarchy based on EXIF metadata. Fast, safe,
and decoupled enough that the same core engine can later back a GUI.

- [Install](install.html) — Homebrew, winget, pre-built binaries, or `go install`.
- [Usage / quick start](usage.html) — `phomv copy` / `phomv move` with flags.
- [How the date hierarchy works](hierarchy.html) — EXIF extraction, mtime fallback, `Unknown/` bucket.
- [Source on GitHub](https://github.com/phomv/phomv) — issues, releases, contributing.

## Why phomv

- **Fast.** Concurrent worker pool (configurable, default 4).
- **Safe.** Atomic-ish copy via temp + rename; cross-device move fallback.
- **Idempotent.** Identical files are skipped via SHA-256 content compare.
- **Collision-safe.** Differing files with the same name get `_1`, `_2`, … suffixes.
- **Resilient.** Files with unreadable timestamps go to an `Unknown/` bucket instead
  of crashing the run.
- **Preview-friendly.** `--dry-run` logs every planned action without touching disk.

## Supported formats

`.jpg`, `.jpeg`, `.png`, `.heic`, `.cr2`, `.nef`, `.arw`, `.dng`, `.tif`, `.tiff`.
