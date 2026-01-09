# reclr

**reclr** (short for recolor) is a simple POSIX shell script for managing color schemes.  
It works with pywal templates and plain-text palettes, allowing you to reuse your existing templates to preview palettes and recolor your terminal, without requiring Python or extra dependencies.
![](../demos/assets/reclr.webp?raw=true)

---

## This branch adds support for dyanmic color generation like pywal, but isn't good at the moment and needs a lot of fixing. Feel free to contribute if you'd like though the code is cursed at the moment

---

## Features

- **Fast** – applies palettes instantly  
- **Format** – pywal templates and plain-text palettes  
- **Hackable** – ~160 LOC of POSIX shell, easy to read and tweak

---

## Why not pywal?

pywal is great for managing color schemes, but it's slow and bloated with features that not everyone needs, like wallpaper setting.  
reclr follows the same concept but simplifies it by making it faster, easier to use, and fully implemented in POSIX shell.  
It keeps pywal’s template format because it works well, but uses a plain-text color scheme format instead of JSON, which is more complicated and unnecessary for most users.  
Unlike pywal, reclr does not generate colors from wallpapers and only works with predefined palettes.

---

## Performance

Tested in bash five times and averaged. reclr loads the same colorscheme faster than pywal despite pywal caching colorschemes.
```sh
time wal --theme petrichor
real    0m0.134s

time ./reclr -i petrichor  
real    0m0.041s
```

---

## Setup Guide

```sh
git clone https://codeberg.org/oceanicc/reclr
cd reclr
./reclr -i palette
```

**Requirements**: POSIX-compliant shell

---

## Usage

```sh
reclr [command]
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
reclr -i petrichor

# Load palette with custom template directory
reclr -i nord -t ~/dotfiles/templates

# List all available palettes
reclr -l

# Preview current colors
reclr -p

# Commands can also be chained
# Load a palette and preview it
reclr -i gruvbox -p
```

---

## Palettes

Palettes live in `~/.config/reclr/palettes` as simple `key=value` text files with a `.txt` extension.
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

Templates use pywal-compatible placeholders and can be dropped in directly from your existing pywal setup (refer to the [migrating from pywal](#migrating-from-pywal) section).
Templates live in `~/.config/reclr/templates`.

Syntax: `{variable} or {variable.modifier}`

### Variables
- `{background}`, `{foreground}`
- `{color0}`–`{color15}`

### Modifiers
- `.strip` – hex without # (e.g: 282828)
- `.rgb` – comma-separated RGB (e.g: 40,40,40)

Example `colors.conf`:
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

## Migrating from pywal

reclr is compatible with pywal's template syntax, you can drop in your existing pywal templates with minimal changes.

### Template migration
Copy your pywal templates into `~/.config/reclr/templates`

### Unsupported modifiers

Some modifiers used in pywal are not implemented in reclr as they are rarely used in templates.
These can usually be replaced with `.strip` or `.rgb` as needed.
- `.red`, `.green`, `.blue` - float components
- `.rgba`, `.xrgba` - alpha channel formats

A patch adding support for these modifiers may be available in the future.

### Converting pywal color schemes

reclr does not use pywal’s JSON color scheme files directly, but you can easily convert them to plain-text palettes using this command:
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

### Optional colorscheme definitions
The `cursor` and `accent` colors are optional but can be added manually and used.
- `accent` defaults to `color2` if not defined
- `cursor` defaults to `color15` if not defined

The palettes provided in this repository do not include `accent` and `cursor`.

Templates can reference any custom color key (e.g: `accent2`, `accent3`) as long as the palette defines them.
Undefined keys expand to empty strings, which may break templates, so ensure your palettes include all keys your templates reference.

---

## Credits

Inspired by [pywal](https://github.com/dylanaraps/pywal) with code style from [kiss](https://codeberg.org/kiss-community/kiss).

## License

[MIT](./LICENSE)
