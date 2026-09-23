# otto-looks

Looks for Otto: a wallpaper, an icon theme, a cursor theme, an accent colour
and the desktop settings that go with them.

```sh
otto-look circles
```

A look is a folder in `looks/`: a `look.toml` that names the wallpaper, the
icon and cursor themes and the settings, plus any file the look ships
itself. Anything else is downloaded from its makers, pinned by sha256.
`otto-look` unpacks the themes into `~/.local/share/icons`, puts the wallpaper
under `~/.local/share/backgrounds/otto-looks/`, then sets everything on the
running session through Otto's settings service, so it applies at once and
survives a restart.

```
looks/pomodoro/
  look.toml
looks/otto98/
  look.toml
  wallpaper.png
```

```toml
[look]
name = "Pomodoro"
version = 1
author = "nongio"                # GitHub username of whoever made the look
description = "One wet tomato on a flat green field."

[wallpaper]
title = "Red round fruit on green surface"
author = "Auguste A"
source = "https://unsplash.com/photos/WQ2WOHw0C7I"
licence = "Unsplash Licence"
licence_url = "https://unsplash.com/license"
url = "https://images.unsplash.com/photo-1605315024122-7fd363c1f7ab?fm=jpg&q=90&w=3840&h=2160&fit=crop"
sha256 = "ac98ad62699e0e107c45b6c96f44fbcc0b4071047a0e52590c7a3d53ac38936b"
```

A `url` is either a file in the look's folder (`url = "wallpaper.png"`, no
sha256 needed) or a download, pinned by `sha256`. A wallpaper lives in the
folder by default, and is linked only when its licence doesn't allow sharing
it.

Every resource credits its maker: `author`, `source` (a link to the
original, not a mirror) and `licence`, plus `licence_url` for anything that
isn't a well-known licence and `title` for a wallpaper's name. `otto-look`
won't install a look that leaves any of them out, and prints the credits
after installing, with any `attribution` text a licence asks for.

Icon and cursor themes are never mirrored. A look names the theme and links
the makers' own archive, and `otto-look`:

1. does nothing if the theme is **already installed** anywhere in the icon
   paths;
2. otherwise downloads the **`url`**, checks its **`sha256`**, and unpacks the
   theme into `~/.local/share/icons` (`.tar.*` or `.zip`; the theme's
   directory can sit anywhere in the archive). When the archive is the
   theme's source rather than a built theme, **`build`** names the theme's
   own installer, run from the top of the source with `DEST` set to the icons
   folder; `otto-look` shows it and asks first, and runs it as you, never as
   root.

```toml
[icons]
theme = "WhiteSur"
author = "Vince Liu"
source = "https://github.com/vinceliuice/WhiteSur-icon-theme"
licence = "GPL-3.0"
url = "https://github.com/vinceliuice/WhiteSur-icon-theme/archive/refs/tags/2026-09-10.tar.gz"
sha256 = "406c9cd59705583f1754b0eaca96cc48bafda042b88ef143f16d8ae1820ecd95"
build = "./install.sh -d \"$DEST\""      # a source archive: the theme's own installer

[cursors]
theme = "Bibata-Modern-Classic"
author = "Abdulkaiz Khatri"
source = "https://github.com/ful1e5/Bibata_Cursor"
licence = "GPL-3.0"
url = "https://github.com/ful1e5/Bibata_Cursor/releases/download/v2.0.7/Bibata-Modern-Classic.tar.xz"
sha256 = "7d3495864e5bbef02f5e77de760b2905903b63c71495a78ef6306d19a3b556d8"
```

`otto-look circles` installs the latest version of Circles, and
`otto-look circles@1` a given one. It also takes a look's folder, its
`look.toml`, a packed `.tar.gz`, or a URL to one, for trying a look while
you make it:

```sh
otto-look looks/pomodoro
```

## Adding a look

1. Set your desktop up the way you like it, then run
   `scripts/record <id> "<Name>"`. It writes `looks/<id>/look.toml` from
   your current settings, with your GitHub username as `author`. Themes and
   wallpapers another look already uses come with their credits; anything
   new is left for you to fill in.
2. Credit every resource, and pin every download by its sha256
   (`curl -fsSL <url> | sha256sum`).
3. Check it: `scripts/dry-run looks/<id>` shows what it would install and
   set, without touching your session, and fails on a missing credit or a
   wrong sha256.
4. Open a pull request. CI runs the same dry run and checks that a new
   look's author is you.

When you change a published look, bump its `version`. Merging to `main`
publishes every look whose version is new, as a GitHub package:
`ghcr.io/nongio/otto-looks/<look>`, tagged with its version and `latest`.

## Testing

[AGENTS.md](AGENTS.md) has the full order, including how to try a look on
the live session.

## Licence

MIT, see [LICENSE](LICENSE).
