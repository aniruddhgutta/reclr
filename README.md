# reclr

**reclr** (short for *recolor*) is a simple, POSIX-compliant shell script for **generating colorschemes**.

---

## Features
- **Simple** – load palettes with a single command
- **Template expansion** – generate config files from templates with color placeholders
- **Terminal sequences** – apply colors to your terminal instantly
- **Minimal** – ~160 lines of pure POSIX shell (), no dependencies
- **Fast** – parameter expansion over external tools where possible

---

## Setup Guide

```sh
git clone https://codeberg.org/oceanicc/reclr
cd reclr
./reclr -i palette_name
```

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

## Examples

```sh
# Load a palette
./reclr -i petrichor

# Load palette with custom template directory
./reclr -i nord -t ~/dotfiles/templates

# List all available palettes
./reclr -l

# Preview current colors
./reclr -p
```

Actions can also be chained together
```sh
# Load a palette and preview it
./reclr -i petrichor -p
```


## Palettes

Palettes are simple `key=value` text files with `.txt` extension.
Values are passed in `hex` format.
All keys (`background`, `foreground`, `color0`-`color15`) are required.

Example template (`~/.config/reclr/palettes/petrichor.txt`)
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

## Templates

Templates use `{variable}` or `{variable.modifier}` syntax:


### Available variables

`{background}` / `{foreground}`
`{color0}` through `{color15}`

### Modifiers

`.strip` – hex without `#` (e.g., `282828`)
`.rgb` – comma-separated RGB (e.g., `40,40,40`)

Example template (`~/.config/reclr/templates/colors.conf.txt`)
```
background = {background}
foreground = {foreground}
cursor = {color7.strip}ff
color0 = {color0.rgb}
```

## Applying Colors

Terminal sequences are automatically generated and applied when you load a palette.

To apply colors in a new terminal session:
```sh
cat ~/.cache/reclr/sequences
```

Add to your shell rc file (.bashrc, .zshrc, etc.):
```sh
[ -f ~/.cache/reclr/sequences ] && cat ~/.cache/reclr/sequences
```

## Credits

This project’s design was inspired by [pywal](https://github.com/dylanaraps/pywal).

## License

[MIT](https://mit-license.org/)
