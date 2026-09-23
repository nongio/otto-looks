# Requests for Otto

Things a look needs that Otto can't do yet.

## Pick up icon themes installed while the session runs

`otto-look` installs a theme into `~/.local/share/icons` and sets
`icon_theme` straight away, but the compositor doesn't see the new theme
until it restarts. `freedesktop-icons` (0.2.6) indexes the installed themes
once per process, and `otto_kit::icons::clear_cache()` doesn't reset that
index. Until the next restart, lookups against the new theme skip it
silently and fall back to hicolor. Names hicolor lacks resolve to nothing,
so the dock draws Trash as an empty placeholder and apps keep their stock
icons.

Seen with Pomodoro and Oxylite, 2026-09-22, on otto-git
1.4.0.r32.g195bcdbd. Reproduce by warming the index with one lookup, then
installing a theme and looking an icon up in it: the result is `None`.

Wanted: rebuild the theme index when `icon_theme` changes (or when a
directory appears under an icon path), or look a theme the index doesn't
know up by hand, the way `guarded_lookup` already does for broken themes.
