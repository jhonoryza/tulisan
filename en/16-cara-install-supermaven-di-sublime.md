# Install Supermaven in Sublime Text 4

Supermaven for Sublime Text 4 — port of [supermaven-nvim](https://github.com/supermaven-inc/supermaven-nvim). Ghost via `phantom` + `sm-agent` stdio.

> Repo: https://github.com/jhonoryza/sublime-supermaven-plugin — auto-downloads `sm-agent` to `~/.supermaven/binary/v20/<os>-<arch>/`.

## Prerequisites

* Sublime Text 4
* Supermaven account ([supermaven.com](https://supermaven.com))

## Install

```bash
git clone https://github.com/jhonoryza/sublime-supermaven-plugin.git ~/Library/Application\ Support/Sublime\ Text/Packages/Supermaven
# Linux: ~/.config/sublime-text/Packages/Supermaven
```

Via Package Control: `Install Package` → `Supermaven`.

## Auth

```
Command Palette → Supermaven: Use Free Version
# or
mkdir -p ~/.config/supermaven
echo '{"apiKey":"sm-..."}' > ~/.config/supermaven/config.json
```

## Keymap

`Tab` accept, `Esc` dismiss, `Ctrl+Right` accept word.

## Commands

```
Supermaven: Start / Stop / Restart / Toggle / UseFree / UsePro / ShowLog
```

## Troubleshooting

`View → Show Console` → `supermaven: is_running?`

See also [Supermaven in Vim](./14-cara-install-supermaven-di-vim.md) and [Supermaven Vim](https://github.com/jhonoryza/supermaven-vim).
