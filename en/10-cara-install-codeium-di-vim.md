# How to Install Codeium in Vim

Codeium is a free AI autocomplete (GitHub Copilot alternative) for Vim and Neovim. The plugin automatically downloads the Language Server for your OS — no manual download needed.

## Prerequisites (Check First)

Open a terminal and make sure versions are sufficient:

```bash
vim --version | head -n1      # must be 9.0.0185 or newer
# or for Neovim
nvim --version | head -n1      # must be 0.6 or newer
git --version                  # must be installed
```

* Codeium/Windsurf account — sign up free at [windsurf.com](https://windsurf.com) (formerly codeium.com). After logging in, go to **Settings → API Key** to copy your `sk-...` (Mac) or `sk-ws-...` (Linux/Windsurf) key.

---

## 0. Prepare `~/.vimrc` First

> Required before installing the plugin — so keymaps don't conflict with `Tab`.

**1. Install vim-plug first (once, if not already installed):**
```bash
# Vim
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
# Neovim
curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

**2. Create/edit `~/.vimrc` (Vim) or `~/.config/nvim/init.vim` (Neovim):**

Minimal `~/.vimrc` currently in use (synced with `12-my-vimrc-config.md`):

```vim
call plug#begin()
Plug 'Exafunction/windsurf.vim', { 'branch': 'main' }
" Linux fix (cpo + sk-ws): Plug 'jhonoryza/windsurf-vim-linux'
call plug#end()

syntax on           " Syntax colors for readability
set number          " Left line numbers
set relativenumber  " Relative numbers (for fast 5j/3k navigation)
set tabstop=4
set shiftwidth=4
set expandtab       " Tab becomes spaces (Python/Go standard)
set cursorline      " Highlight cursor line

" Windsurf keymaps (using Ctrl, not Tab to avoid conflicts)
imap <script><silent><nowait><expr> <C-g> codeium#Accept()              " Accept all suggestions
imap <script><silent><nowait><expr> <C-h> codeium#AcceptNextWord()      " Accept 1 word
imap <script><silent><nowait><expr> <C-j> codeium#AcceptNextLine()      " Accept 1 line
imap <C-;>   <Cmd>call codeium#CycleCompletions(1)<CR>   " Next suggestion
imap <C-,>   <Cmd>call codeium#CycleCompletions(-1)<CR>  " Previous suggestion
imap <C-x>   <Cmd>call codeium#Clear()<CR>                " Clear
" Manual trigger (optional, if you prefer not auto)
" let g:codeium_manual = v:true
imap <C-Space> <Cmd>call codeium#Complete()<CR>
imap <M-Bslash> <Cmd>call codeium#Complete()<CR>
```

> On Mac the config above works out of the box. For Linux see Special Notes below.

---

## 1. Install the Plugin

In Vim run:

```vim
:PlugInstall
```

Wait until `windsurf.vim` is installed, then restart Vim (`:qa` and reopen).

### Manual (without vim-plug)

```bash
# Vim
git clone https://github.com/Exafunction/windsurf.vim ~/.vim/pack/Exafunction/start/windsurf.vim
# or Linux fix
git clone https://github.com/jhonoryza/windsurf-vim-linux ~/.vim/pack/Exafunction/start/windsurf.vim

# Neovim
git clone https://github.com/Exafunction/windsurf.vim ~/.local/share/nvim/site/pack/Exafunction/start/windsurf.vim
```

---

## 2. Set Up the API Key (Without Browser)

> The old method `:Codeium Auth` that opens a browser + `curl api.codeium.com` is deprecated, so write the file directly.

**Get the key first:** Log in at [windsurf.com](https://windsurf.com) → **Settings → API Key** → Copy `sk-...` or `sk-ws-...`

**Mac** (`~/.codeium/config.json`):
```bash
mkdir -p ~/.codeium
echo '{"apiKey":"sk-..."}' > ~/.codeium/config.json
# or in Vim:
:Codeium Auth sk-xxxx
```

**Linux** (Windsurf `sk-ws-`, `~/.config/Codeium/config.json`):
```bash
mkdir -p ~/.config/Codeium
echo '{"apiKey":"sk-ws-01-..."}' > ~/.config/Codeium/config.json
:Codeium Auth sk-ws-01-xxxx
```

**Verification (required for new users):**
```bash
cat ~/.config/Codeium/config.json  # Linux
cat ~/.codeium/config.json          # Mac
```
In Vim:
```vim
:echo codeium#command#ApiKey()[:12]  " should show sk-... / sk-ws-...
:echo codeium#Enabled()              " should be v:true (if 0, check :set filetype?)
```

> Language Server auto-downloads on first Vim launch. If it remains `.gz`, see Troubleshooting.

---

## 3. How to Use

Type as usual — **ghost text** (transparent gray text) will appear as suggestions. No extra action needed; suggestions appear automatically (or press `Ctrl+Space` if `g:codeium_manual` is set).

| Action | Key in `~/.vimrc` |
| :--- | :--- |
| Accept all suggestions | `Ctrl+g` |
| Accept 1 word | `Ctrl+h` |
| Accept 1 line | `Ctrl+j` |
| Next suggestion / sebelumnya | `Ctrl+;` / `Ctrl+,` |
| Clearkan saran | `Ctrl+x` |
| Trigger manually | `Ctrl+Space` / `Alt+\` |

Check status:
```vim
:Codeium Enable / Disable
:echo codeium#GetStatusString()  " 3/8 = saran ke-3 dari 8, * = loading, 0 = tidak ada saran
```

---

## 4. Troubleshooting (New Users)

**Suggestions not appearing?**
```vim
:echo codeium#Enabled()  " if 0, check filetype: :set filetype? (should be python/js/etc, not empty)
:Codeium Enable
:echo codeium#log#Logfile()  " cat file log untuk lihat error"
```

**Language Server `.gz` corrupt (4.1M)?**
```bash
ls -lh ~/.local/share/.codeium/bin/*/language_server_linux_x64.gz
rm -f ~/.local/share/.codeium/bin/*/language_server_linux_x64.gz
curl -Lo /tmp/ls.gz https://github.com/Exafunction/codeium/releases/download/language-server-v1.20.8/language_server_linux_x64.gz
mkdir -p ~/.local/share/.codeium/bin/37f12b83df389802b7d4e293b3e1a986aca289c0
gzip -dc /tmp/ls.gz > ~/.local/share/.codeium/bin/37f12b83df389802b7d4e293b3e1a986aca289c0/language_server_linux_x64
chmod +x ~/.local/share/.codeium/bin/37f12b83df389802b7d4e293b3e1a986aca289c0/language_server_linux_x64
pkill -9 language_server; rm -rf /tmp/*codeium*
```

**Tab conflicts with other plugins?**
```vim
let g:codeium_no_map_tab = 1  " taruh di atas semua imap di ~/.vimrc
```

**`E723` / `E10: \ should be followed by` di Vim 9.2 Linux?**
Vim 9.2 on Linux has `cpo` containing `C` so `autoload/codeium.vim` fails to parse. Fixed in `jhonoryza/windsurf-vim-linux` (`set cpo&vim`).

---

## 5. Linux-Specific Notes (Windsurf)

Mac works directly, Linux requires:

1. Use `windsurf.vim` (not the old `codeium.vim`)
2. API key via file (above) — without `register_user`
3. Default binary `~/.local/share/.codeium/bin/<sha>/language_server_linux_x64` — if in `~/.local/bin`, set `let g:codeium_bin = $HOME.'/.local/bin/language_server_linux_x64'`
4. Keys with `sk-ws-` automatically use `server.windsurf.com` (Linux fork handles this)

---

## Download (Automatic)

No manual download needed. Auto `curl` on `PlugInstall`. Links are only for reference if auto fails:

* https://github.com/Exafunction/codeium/releases

---

## Conclusion

1. **Prepare `~/.vimrc`** first (install vim-plug + plug + keymaps)
2. **Install the plugin** (`:PlugInstall`)
3. **Set up the API key** to file (`~/.codeium` Mac / `~/.config/Codeium` Linux) — verify with `:echo codeium#command#ApiKey()`

Done — open a `.py` / `.js` file, start typing, and ghost text will appear.
