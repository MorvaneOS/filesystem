# filesystem

The base filesystem package for MorvaneOS: the directory layout, `/etc/passwd`,
`/etc/fstab` and friends, and the files that say which OS this is.

This is a fork of [Artix's `filesystem`](https://gitea.artixlinux.org/packages/filesystem)
under the same package name. The `[morvane]` repo is listed above Artix's in
`pacman.conf`, so pacman installs this one instead.

## What's different from Artix

| File | Change |
|---|---|
| `/usr/lib/os-release` (and `/etc/os-release`, a symlink to it) | MorvaneOS Linux: `ID=morvane`, `ID_LIKE="artix arch"`, pastel pink `ANSI_COLOR`, `LOGO=morvane-logo` |
| `/etc/morvane-release` | New, alongside Artix's `/etc/artix-release` |
| `/usr/share/pixmaps/morvane-logo*` | The Twilight Peaks logo (from [MorvaneOS/assets](https://github.com/MorvaneOS/assets)) |
| `/etc/xdg/fastfetch/config.jsonc` + `/usr/share/morvane/fastfetch/logo.txt` | Default fastfetch look with the MorvaneOS logo. A user's own `~/.config/fastfetch/config.jsonc` still wins |

Everything else is Artix's, unchanged. `/etc/artix-release` and the
`artixlinux-logo*` pixmaps stay because Artix packages look for them.

## Versioning

`pkgver` follows Artix's; `pkgrel` is Artix's plus `.1` (e.g. `2025.10.12-1.1`).
That makes pacman treat ours as newer than Artix's same release, so existing
systems switch over on their next `pacman -Syu`.

## Merging an Artix update

Artix updates this package a few times a year. The `artix` remote is fetch-only.

```
git fetch artix
git log --oneline artix/master      # "[system]" = stable, "[system-gremlins]" = testing
git merge <the stable commit>       # conflicts, if any, are in PKGBUILD
```

Then in `PKGBUILD`: take Artix's new `pkgver`, set `pkgrel` to their `pkgrel`
plus `.1`, keep our lines (Maintainer, `pkgdesc`, `url`, the morvane files),
and refresh the checksums with `updpkgsums` (or `makepkg -g`). Build and
publish as usual:

```
cd ~/projects/morvane && ./pack filesystem
bash ~/projects/morvane-repo/publish.sh out/packages/filesystem-<version>-any.pkg.tar.zst
```

Merge only commits that reached `[system]`; `[system-gremlins]` is Artix's testing repo.

## Updating the logos

The logo files are copies from [MorvaneOS/assets](https://github.com/MorvaneOS/assets):
`app-icon-dark.svg` → `morvane-logo.svg`, `app-icon-dark-256.png` →
`morvane-logo.png`, `lockup-horizontal-{light,dark}.svg` →
`morvane-logo-text{,-dark}.svg`, `ascii/morvane.txt` → `fastfetch-logo.txt`.
Copy them over again, bump `pkgrel`, and refresh the checksums.

## License

0BSD, as upstream (`LICENSE`).
