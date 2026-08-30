# How to Install Supermaven in Vim 9

Supermaven is a fast AI autocomplete (Codeium/Windsurf alternative) now available as a Vim 9 port of `supermaven-nvim` (Neovim Lua → Vimscript). Perfect if you use Vim 9.0+ and don't want to switch to Neovim.

> Port: https://github.com/jhonoryza/supermaven-vim — ghost via `prop` (Vim) / `extmark` (Neovim), `job` stdio `SM-MESSAGE`.

## Prerequisites

```bash
vim --version | head -n1      # 9.0+
git --version
```
Supermaven account — sign up at [supermaven.com](https://supermaven.com) → **Settings → API Key** or use Free Tier via `:SupermavenUseFree`.

---

## 0. Prepare `~/.vimrc` First

```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

Minimal `~/.vimrc` (to avoid Windsurf `Ctrl` conflict → Supermaven uses `Alt`):

```vim
call plug#begin()
Plug 'jhonoryza/supermaven-vim'
call plug#end()

syntax on
set number
set relativenumber
set tabstop=4
set shiftwidth=4
set expandtab
set cursorline
set noswapfile

" Supermaven keymaps (Alt to avoid Windsurf Ctrl conflict)
let g:supermaven_disable_bindings = 1
imap <M-g> <Plug>(supermaven-accept)   " Accept all
imap <M-]> <Plug>(supermaven-clear)    " Clear
imap <M-j> <Cmd>call supermaven#Accept()<CR>  " Accept 1 word
imap <M-Space> <Cmd>call supermaven#Complete()<CR>  " Manual trigger
```

> If you want to keep Codeium active, use `Ctrl` for Codeium and `Alt` for Supermaven — don't overlap.

---

## 1. Install the Plugin

```vim
:PlugInstall
```
Restart Vim. The `sm-agent` binary will auto-download to `~/.supermaven/binary/v20/<platform>-<arch>/sm-agent` via `https://supermaven.com/api/download-path-v2`.

Manual:
```bash
git clone https://github.com/jhonoryza/supermaven-vim ~/.vim/pack/supermaven/start/supermaven-vim
```

---

## 2. Authentication

**Option A: Free Tier (no API key):**
```vim
:SupermavenUseFree
" follow the activation link shown in the popup
```

**Option B: API Key:**
```bash
mkdir -p ~/.config/supermaven
echo '{"apiKey":"sm-..."}' > ~/.config/supermaven/config.json
# or
:SupermavenAuth sm-xxxx
```

Check:
```vim
:SupermavenStatus  " running / not running
:SupermavenShowLog  " tail -f $(find /tmp -name "*supermaven.log" | head -n1)
```

---

## 3. How to Use

Ketik seperti biasa — ghost putih muncul otomatis (or `Alt+Space` jika `g:supermaven_manual`).

| Aksi | Keymap |
| :--- | :--- |
| Accept all | `Alt+g` |
| Clear | `Alt+]` |
| Accept 1 word | `Alt+j` |
| Manual trigger | `Alt+Space` |

Statusline:
```vim
set statusline+=%{supermaven#GetStatusString()}
```

---

## 4. Troubleshooting

**Ghost not appearing?**
```vim
:SupermavenStatus
:echo &filetype  " should not be empty, check g:supermaven_ignore_filetypes
:SupermavenShowLog
```

**Binary download failed?**
```bash
ls -lh ~/.supermaven/binary/v20/*/sm-agent
curl -s "https://supermaven.com/api/download-path-v2?platform=linux&arch=x86_64&editor=neovim" | jq
```

**Want to use nvim-cmp (Neovim)?**
```lua
cmp.setup { sources = {{ name = "supermaven" }} }
```

---

## Download

Automatic on `PlugInstall`. Reference links:

* https://github.com/jhonoryza/supermaven-vim
* https://github.com/supermaven-inc/supermaven-nvim (original Neovim)

---

## Conclusion

1. **Prepare `~/.vimrc`** (plug + `Alt` keymaps)
2. **Install** (`:PlugInstall`)
3. **Auth** (`:SupermavenUseFree` or file `~/.config/supermaven/config.json`)

Done — Supermaven ghost works in Vim 9 without Neovim. For Codeium/Windsurf see [How to Install Codeium in Vim](./10-cara-install-codeium-di-vim.md).
