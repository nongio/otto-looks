# otto-looks

Looks for Otto: a wallpaper, an icon theme, a cursor theme, an accent colour
and the dock and desktop settings that go with them.

Every look is published on its own, as a package,
`ghcr.io/nongio/otto-looks/<look>`, and installed by name with `otto-look`,
which ships with Otto:

```sh
otto-look circles        # the latest version of Circles
otto-look circles@1      # a given version
```

It installs the look's icon and cursor themes into `~/.local/share/icons`,
puts its wallpaper under `~/.local/share/backgrounds/otto-looks/`, and sets
everything on the running session through Otto's settings service, so it
applies at once and survives a restart.

## Looks

| Look | | Install |
|---|---|---|
| **Béton Brut** | A concrete monolith against a cold sky, and one signal yellow. | `otto-look beton-brut` |
| **Circles** | Kandinsky's Circles in a Circle, 1923. | `otto-look circles` |
| **Crate Digger** | 52nd Street in the rain, 1948, on aubergine. | `otto-look crate-digger` |
| **Deep Field** | Webb's First Deep Field, near-black and one violet. | `otto-look deep-field` |
| **Ember** | Out-of-focus lights melted into one warm glow on black. | `otto-look ember` |
| **Otto98** | Windows 98 SE icons on a cool, dithered teal. Square, flat, minimal. | `otto-look otto98` |
| **Pomodoro** | One wet tomato on a flat green field. | `otto-look pomodoro` |
| **Section 9** | A misted high-rise city in fog grey and ice. | `otto-look section-9` |
| **Supremus** | Malevich's Suprematist planes on white, 1915. | `otto-look supremus` |
| **Swan** | Hilma af Klint's split disc on red, 1915. | `otto-look swan` |

## What a look is

A look is a folder in `looks/`: a `look.toml` that names the wallpaper, the
icon and cursor themes and the settings, plus any file the look ships
itself. Themes are downloaded from their makers, pinned by sha256.

```
looks/<look>/
  look.toml
  wallpaper.jpg
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
url = "wallpaper.jpg"
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

While you make a look, `otto-look` also takes its folder, its `look.toml`,
a packed `.tar.gz`, or a URL to one:

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
4. Add a row for it to the table of looks at the top of this README.
5. Open a pull request. CI runs the same dry run and checks that a new
   look's author is you.

When you change a published look, bump its `version`. Merging to `main`
publishes every look whose version is new, as a GitHub package:
`ghcr.io/nongio/otto-looks/<look>`, tagged with its version and `latest`.

## Testing

[AGENTS.md](AGENTS.md) has the full order, including how to try a look on
the live session.

## Licence

MIT, see [LICENSE](LICENSE).
