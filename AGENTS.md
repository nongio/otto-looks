# otto-looks

Looks for Otto. Each look is a folder, `looks/<id>/`, holding `look.toml`
and any file the look ships itself, such as its wallpaper. `otto-look`
installs a look and applies it to the running session through
`org.otto.Settings`. `README.md` describes the format.

## A look's folder

```
looks/<id>/
  look.toml        the definition and all its credits
  wallpaper.jpg    only when the licence lets us host it
```

A resource's `url` is either a file in the folder (relative, no `sha256`) or
a download from its maker (absolute, pinned by `sha256`). Nothing outside the
folder: no `../`, no absolute paths. `[look]` has `name`, `version` (an
integer, bumped on every change to a published look), `author` (the GitHub
username of whoever made the look) and `description`.

## Testing a look

Test in this order, and don't skip to the live session.

1. **Dry run.** `scripts/dry-run looks/<id>`. It installs into a throwaway
   home against a stub `busctl` and prints the files it would write and every
   setting it would set. Nothing live changes. Check:
   - every file you expect is listed, themes as `icons/<Theme>/index.theme`;
   - every setting is one from the table below, with the right type letter
     (`s` string, `b` bool, `x` integer, `d` decimal);
   - `would run:` lines show a theme's own installer that a real install
     would ask about;
   - a wrong sha256, a missing credit, or a `url` outside the folder stops it
     with an error; that is the check working, not a bug in the script.

   CI runs the same dry run on every pull request, for the looks it touches.

2. **Live, only when asked.** Installing changes the desktop Riccardo is
   using, so only do it when they say so:

   ```sh
   ./otto-look looks/<id>
   ```

   Then read back what landed:

   ```sh
   busctl --user call org.otto.Settings /org/otto/Settings org.otto.Settings Get s accent_color
   ```

   and take a screenshot with `grim` (into `~/Pictures/Screenshots/`) and
   look at it. Never send input to the live session: no typing, clicking or
   synthetic keys.

3. **Published.** A push to `main` that changes a look publishes every look
   whose `version` is new, as the package `ghcr.io/nongio/otto-looks/<id>`
   tagged with its version and `latest`. `otto-look <id>` installs the latest
   and `otto-look <id>@<version>` a given one; `scripts/dry-run <id>` checks
   what was published. A new package starts private: make it public once in
   its package settings on GitHub.

## Themes are never mirrored

An icon or cursor theme is linked, not hosted: the `url` of its makers' own
archive, pinned by `sha256`, plus `build` when the archive is source that
the theme's own installer builds. `otto-look` never runs a package manager
or sudo. A theme goes in a look's folder only if it is Otto's own.

## Credits

Every look has clear credits and links, without exception. They live in
`look.toml`, next to what they credit.

- Each `[wallpaper]`, `[icons]` and `[cursors]` section has `author`,
  `source` and `licence`, plus `licence_url` when the licence isn't a
  well-known one and `title` for a wallpaper. `source` links to the
  original: the artist's page, the photo's page, the theme's repository.
  Not a mirror, a store re-upload or a search result.
- Attribution text a licence asks for goes in `attribution`, word for word;
  `otto-look` prints it with the credits.
- A wallpaper lives in the look's folder by default. Public domain, CC0,
  CC BY, our own and the Unsplash Licence all allow it; the Unsplash
  Licence only forbids compiling photos into a competing service. Link it
  with `url` + `sha256` only when its licence doesn't allow sharing it.
- The dry run fails on a missing credit. If the author or licence of
  something can't be established, it doesn't go in the look.

## Settings a look can set

| Key | Values |
|-----|--------|
| `theme_scheme` | `"Light"`, `"Dark"` |
| `accent_color` | `blue purple pink red orange yellow green mint teal cyan indigo brown gray`, or `"#RRGGBB"` |
| `background_color` | `"#RRGGBB"` |
| `cursor_size` | 16–96, steps of 8 |
| `rounded_corners`, `frosting`, `show_maximize_button` | `true` / `false` |
| `window_controls_side` | `"left"`, `"right"` |
| `dock.position` | `"bottom"`, `"left"`, `"right"` |
| `dock.size` | 0.5–2.0 |
| `dock.colorize_icons` | `true` / `false` |
| `dock.colorize_color` | `"#RRGGBB"`; the tint is luminance × colour, so tint bright, never dark |
| `dock.colorize_intensity` | 0.0–1.0 |

A look that doesn't set `dock.position` gets the dock at the bottom, and one
that doesn't set `dock.colorize_icons` gets the tint turned off, so one
look's dock never carries into the next. Other settings a look leaves out
stay as they were.

`icon_theme`, `cursor_theme` and `background_image` come from `[icons]`,
`[cursors]` and `[wallpaper]`; don't put them in `[settings]`. Looks don't set
the font. The running compositor's `Describe` is the authority:
`busctl --user call org.otto.Settings /org/otto/Settings org.otto.Settings Describe`.

## Scripts

| Script | What |
|--------|------|
| `otto-look <id>[@<version>]` | install and apply a published look |
| `otto-look list` | the published looks, from the `index` package `scripts/release` keeps |
| `otto-look <folder \| look.toml \| look.tar.gz \| url>` | install and apply a look you're making |
| `scripts/dry-run <look>...` | what looks would install and set, without touching anything |
| `scripts/release [--dry] [<id>...]` | publish every look whose version is new; CI runs it on `main` |
| `scripts/record <id> [name]` | turn the running desktop into `looks/<id>/look.toml`, reusing the credits of themes and wallpapers other looks already use |

## Don't

- Commit, push or tag without being asked.
- Edit Otto itself from here. If a look needs something Otto can't do, write
  it down as a request in `REQUESTS.md`.
