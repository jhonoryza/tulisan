# Cara Install Supermaven di Sublime Text 4

Supermaven untuk Sublime Text 4 — port dari [supermaven-nvim](https://github.com/supermaven-inc/supermaven-nvim). Ghost via `phantom` + `sm-agent` stdio.

> Repo: https://github.com/jhonoryza/sublime-supermaven-plugin — auto-download `sm-agent` ke `~/.supermaven/binary/v20/<os>-<arch>/`.

## Prasyarat

* Sublime Text 4
* Akun Supermaven ([supermaven.com](https://supermaven.com))

## Install

```bash
git clone https://github.com/jhonoryza/sublime-supermaven-plugin.git ~/Library/Application\ Support/Sublime\ Text/Packages/Supermaven
# Linux: ~/.config/sublime-text/Packages/Supermaven
```

Via Package Control: `Install Package` → `Supermaven`.

## Auth

```
Command Palette → Supermaven: Use Free Version
# atau
mkdir -p ~/.config/supermaven
echo '{"apiKey":"sm-..."}' > ~/.config/supermaven/config.json
```

## Keymap

`Tab` terima, `Esc` hapus, `Ctrl+Right` terima kata.

## Commands

```
Supermaven: Start / Stop / Restart / Toggle / UseFree / UsePro / ShowLog
```

## Troubleshooting

`View → Show Console` → `supermaven: is_running?`

Lihat juga [Supermaven di Vim](./14-cara-install-supermaven-di-vim.md) dan [Supermaven Vim](https://github.com/jhonoryza/supermaven-vim).
