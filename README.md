# reclr

**reclr** (short for *recolor*) is a simple, POSIX-compliant shell script for **generating colorschemes**.
It generates configs from pywal-compatible templates, previews palettes, and recolors your terminal — all with zero dependencies.

---

## Features

- Fast – loads themes instantly; pure shell, no Python
- Compatible – works with pywal template syntax out of the box
- Simple – load palettes and expand templates in one step
- Portable – pure POSIX shell (~160 LOC), no external dependencies

---

## Why I Made This

I like using **pywal** to manage color schemes, but I never cared much for its wallpaper features.
It always felt slower than it needed to be, and I didn’t like relying on Python or extra dependencies just to load some colors.

I prefer working in plain POSIX shell, so I made reclr — something faster, simpler, and easier to tinker with.
It keeps pywal’s template format (because it’s great) but replaces the JSON colorschemes with a simpler plain-text format, since JSON seemed unnecessary.

---

## Performance

Testing done in bash, loading the same colorscheme (cached):
```sh
time wal --theme petrichor
real    0m0.219s

time ./reclr -i petrichor  
real    0m0.088s
```

---

## Setup Guide

```sh
git clone https://codeberg.org/oceanicc/reclr
cd reclr
./reclr -i palette
```

Requirements: POSIX-compliant shell

---

## Usage

```sh
./reclr [command]
```

### Commands

- `-i` – load palette by name
- `-t` – custom template directory (optional)
- `-o` – custom output directory (optional)
- `-l` – list available palettes
- `-p` – preview current color palette
- `-h` – display help message

### Examples

```sh
# Load a palette
./reclr -i petrichor

# Load palette with custom template directory
./reclr -i nord -t ~/dotfiles/templates

# List all available palettes
./reclr -l

# Preview current colors
./reclr -p

# Commands can also be chained
# Load a palette and preview it
./reclr -i gruvbox -p
```

---

## Palettes

Palettes live in `~/.config/reclr/palettes/` as simple `key=value` text files with a `.txt` extension.
Values are passed in `hex` format. All standard keys (`background`, `foreground`, `color0`-`color15`) must be defined.

Example: `petrichor.txt`
```
background=#0E0E0E
foreground=#c7c9cc
cursor=#c7c9cc
color0=#0E0E0E
color1=#9c6b7a
color2=#899087
color3=#c4afa2
color4=#8d96bd
color5=#b694b6
color6=#86a3a6
color7=#c7c9cc
color8=#44444c
color9=#ae808f
color10=#949b92
color11=#e0cca8
color12=#9da4c8
color13=#d2aece
color14=#9cb9c0
color15=#c7c9cc
```

---

## Templates

Templates use *pywal-compatible placeholders* and can be dropped in directly from your existing pywal setup (refer to the [migration from pywal](#migration-from-pywal) section).
Templates live in `~/.config/reclr/templates/`.

Syntax: `{variable} or {variable.modifier}`

### Variables
- `{background}`, `{foreground}`
- `{color0}`–`{color15}`

### Modifiers
- `.strip` – hex without # (e.g., 282828)
- `.rgb` – comma-separated RGB (e.g., 40,40,40)

Example `colors.conf.txt`:
```sh
background = {background}
foreground = {foreground}
cursor = {color7.strip}ff
color0 = {color0.rgb}
```

For additional reference: [pywal wiki](https://github.com/dylanaraps/pywal/wiki/User-Template-Files)

---

## Applying Colors

Terminal sequences are automatically generated and applied when you load a palette.
Add this to your shell rc file (`.profile`, `.bashrc`, `.zshrc`, etc.) to automatically load generated colors at startup:
```sh
[ -f ~/.cache/reclr/sequences ] && (cat ~/.cache/reclr/sequences)
```

---

## Migration from pywal

reclr is compatible with pywal template syntax, you can drop in your existing pywal templates with minimal changes.

### Template migration
- Copy your pywal templates into `~/.config/reclr/templates/`
- Add `.txt` to their filenames (e.g., `colors.Xresources` → `colors.Xresources.txt`)
- reclr supports `{colorX}`, `{background}`, `{foreground}` and `{variable.modifier}` syntax

### Unsupported modifiers

Some modifiers used in pywal are not implemented in reclr as they are rarely used in templates.
These can usually be replaced with `.strip` or `.rgb` as needed.
- `.red`, `.green`, `.blue` - float components
- `.rgba`, `.xrgba` - alpha channel formats

A patch adding support for these modifiers may be available in the future.

### Converting pywal color schemes

reclr does not use pywal’s `.json` color scheme files directly, but you can easily convert them to reclr-compatible `.txt` palettes using this command:
```sh
# Convert all .json colorschemes in the current directory to .txt palettes
for f in *.json; do
    name=$(basename "$f" .json)
    grep -oE '"(background|foreground|color[0-9]+)":\s*"#[0-9a-fA-F]+"' "$f" |       
    sed 's/"//g;s/:\s*/=/' > "$name.txt"
done
```
Each `.json` file will produce a plain-text `.txt` palette containing all standard color keys supported by reclr.

### Unsupported colorscheme definitions

reclr ignores non-color fields in pywal’s `.json` files such as `checksum`, `wallpaper`, and `alpha`.  
The `cursor` color is optional but can be added manually for template compatibility.

---

## Credits

Design inspired by [pywal](https://github.com/dylanaraps/pywal)
Code style and philosophy inspired by [kiss](https://codeberg.org/kiss-community/kiss)

## License

[MIT](https://mit-license.org/)
