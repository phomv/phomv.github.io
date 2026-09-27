# phomv.github.io

Source for the phomv documentation site at <https://phomv.github.io>.

This is a minimal Jekyll site using the built-in `jekyll-theme-minimal`
theme — GitHub Pages builds it automatically on every push to `main`. No
custom workflow needed.

## Local preview

```sh
bundle install
bundle exec jekyll serve
# http://127.0.0.1:4000
```

## Structure

- `index.md` — landing page
- `install.md` — Homebrew, winget, binaries, `go install`
- `usage.md` — quick start, flag reference, what gets skipped, progress/exit status
- `hierarchy.md` — how the date hierarchy works: EXIF/QuickTime dates, sidecars, collisions
- `_config.yml` — site config + theme
- `assets/` — logos and favicons

Project source: <https://github.com/phomv/phomv>
