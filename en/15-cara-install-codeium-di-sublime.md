# Install Codeium in Sublime Text 4

Codeium for Sublime Text 4 — port of [windsurf.vim](https://github.com/Exafunction/windsurf.vim) (`job` + `curl` http `GetCompletions`) to Python `phantom` + `language_server`.

> Repo: https://github.com/jhonoryza/sublime-codeium-plugin — auto-downloads `language_server` to `~/.local/share/.codeium/bin/<sha>/` (Mac/Linux/Windows).

## Prerequisites

* Sublime Text 4
* Git
* Codeium/Windsurf account

## Install

### Package Control

`Cmd+Shift+P` → `Package Control: Install Package` → `Codeium` (after publish)

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

Check `View → Show Console` → `Codeium: is_running?`.

## Keymap

`Tab` accept, `Esc` dismiss, `Ctrl+Right` accept word — via `Default.sublime-keymap`.

## Commands

```
Codeium: Start / Stop / Restart / Toggle / Status
Codeium: Show Log
```

## Troubleshooting

**Binary `.gz` corrupt?** Same as Vim — `gzip -dc` manually to `~/.local/share/.codeium/bin/<sha>/`.

**No ghost?** `View → Show Console` check `PORT` and `api_key`.

See also [Codeium in Vim](./10-cara-install-codeium-di-vim.md).
