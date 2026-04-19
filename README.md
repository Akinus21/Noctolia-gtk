# Noctolia GTK

Syncs [Noctalia](https://github.com/Akinus21/Noctalia) theme colors to GTK 3 and GTK 4 applications via CSS overrides and gsettings.

Watches `~/.config/noctalia/` for changes to `colors.json` and `settings.json`, then generates matching GTK CSS and updates the GNOME color scheme automatically.

## Features

- Watches Noctalia config in real-time via inotify
- Generates GTK 3 CSS (`~/.config/gtk-3.0/gtk.css`)
- Generates GTK 4 CSS (`~/.config/gtk-4.0/gtk.css`)
- Updates GNOME gsettings `color-scheme` and `gtk-theme`
- Works with any GTK app (Nyxt, GTK+ apps, etc.)

## Prerequisites

```bash
# Debian/Ubuntu
sudo apt-get install libgtk-3-dev libxkbcommon-dev libwayland-dev pkg-config
```

## Build

```bash
cargo build --release
```

The binary will be at `target/release/noctalia-gtk`.

## Setup

### 1. Create Noctalia config

Noctolia-gtk expects these files to exist:

`~/.config/noctalia/colors.json` — Noctalia color palette in camelCase:

```json
{
  "mPrimary": "#color",
  "mOnPrimary": "#color",
  "mSecondary": "#color",
  ...
}
```

`~/.config/noctalia/settings.json` — includes dark/light mode toggle:

```json
{
  "colorSchemes": {
    "darkMode": true
  }
}
```

### 2. Autostart

**systemd (recommended):**

```bash
cp noctalia-gtk.service ~/.config/systemd/user/
systemctl --user enable noctalia-gtk.service
systemctl --user start noctalia-gtk.service
```

**Manual:**

Add to your window manager's autostart (niri, Hyprland, etc.):

```bash
target/release/noctalia-gtk
```

## How it works

1. On startup, reads `~/.config/noctalia/colors.json` and `settings.json`
2. Generates GTK 3 and GTK 4 CSS with Noctalia color variables
3. Sets GNOME gsettings `color-scheme` and `gtk-theme`
4. Watches the noctalia config directory for changes
5. Re-applies theme whenever `colors.json` or `settings.json` changes

## Verification

Check that CSS was generated:

```bash
cat ~/.config/gtk-3.0/gtk.css
cat ~/.config/gtk-4.0/gtk.css
```

Check gsettings:

```bash
gsettings get org.gnome.desktop.interface color-scheme
gsettings get org.gnome.desktop.interface gtk-theme
```

## Troubleshooting

If GTK apps don't update, try:
- Restart the app (some apps only read CSS on launch)
- Kill and restart noctalia-gtk
- Verify the config files exist and are valid JSON

## License

MIT