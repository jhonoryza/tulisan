# Cara Install Codeium di Sublime Text 4

Codeium untuk Sublime Text 4 — port dari [windsurf.vim](https://github.com/Exafunction/windsurf.vim) (`job` + `curl` http `GetCompletions`) ke Python `phantom` + `language_server`.

> Repo: https://github.com/jhonoryza/sublime-codeium-plugin — auto-download `language_server` ke `~/.local/share/.codeium/bin/<sha>/` (Mac/Linux/Windows).

## Prasyarat

* Sublime Text 4
* Git
* Akun Codeium/Windsurf

## Install

### Package Control

`Cmd+Shift+P` → `Package Control: Install Package` → `Codeium` (setelah publish)

### Manual

```bash
git clone https://github.com/jhonoryza/sublime-codeium-plugin.git ~/Library/Application\ Support/Sublime\ Text/Packages/Codeium
# Linux: ~/.config/sublime-text/Packages/Codeium
# Windows: %APPDATA%\Sublime Text\Packages\Codeium
```

Restart Sublime.

## Auth

```bash
mkdir -p ~/.config/Codeium
echo '{"apiKey":"sk-ws-..."}' > ~/.config/Codeium/config.json  # Linux
mkdir -p ~/.codeium && echo '{"apiKey":"sk-..."}' > ~/.codeium/config.json  # Mac
```

Cek `View → Show Console` → `Codeium: is_running?`.

## Keymap

`Tab` terima, `Esc` hapus, `Ctrl+Right` terima kata — via `Default.sublime-keymap`.

## Commands

```
Codeium: Start / Stop / Restart / Toggle / Status
Codeium: Show Log
```

## Troubleshooting

**Binary `.gz` corrupt?** Sama kayak Vim — `gzip -dc` manual ke `~/.local/share/.codeium/bin/<sha>/`.

**Tidak ada ghost?** `View → Show Console` cek `PORT` dan `api_key`.

Lihat juga [Codeium di Vim](./10-cara-install-codeium-di-vim.md).
