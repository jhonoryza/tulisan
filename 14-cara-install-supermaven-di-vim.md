# Cara Install Supermaven di Vim 9

Supermaven adalah AI autocomplete cepat (alternatif Codeium/Windsurf) yang sekarang ada port Vim 9 dari `supermaven-nvim` (Neovim Lua → Vimscript). Cocok kalau kamu pakai Vim 9.0+ dan tidak mau pindah ke Neovim.

> Port: https://github.com/jhonoryza/supermaven-vim — ghost via `prop` (Vim) / `extmark` (Neovim), `job` stdio `SM-MESSAGE`.

## Prasyarat

```bash
vim --version | head -n1      # 9.0+
git --version
```
Akun Supermaven — daftar di [supermaven.com](https://supermaven.com) → **Settings → API Key** atau pakai Free Tier via `:SupermavenUseFree`.

---

## 0. Siapkan `~/.vimrc` Dulu

```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

`~/.vimrc` minimal (biar tidak bentrok Windsurf `Ctrl` → Supermaven pakai `Alt`):

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

" Supermaven keymap (Alt biar tidak bentrok Windsurf Ctrl)
let g:supermaven_disable_bindings = 1
imap <M-g> <Plug>(supermaven-accept)   " Terima semua
imap <M-]> <Plug>(supermaven-clear)    " Hapus
imap <M-j> <Cmd>call supermaven#Accept()<CR>  " Terima 1 kata
imap <M-Space> <Cmd>call supermaven#Complete()<CR>  " Trigger manual
```

> Jika mau Codeium tetap aktif, pakai `Ctrl` untuk Codeium dan `Alt` untuk Supermaven — jangan samakan.

---

## 1. Install Plugin

```vim
:PlugInstall
```
Restart Vim. Binary `sm-agent` akan auto-download ke `~/.supermaven/binary/v20/<platform>-<arch>/sm-agent` via `https://supermaven.com/api/download-path-v2`.

Manual:
```bash
git clone https://github.com/jhonoryza/supermaven-vim ~/.vim/pack/supermaven/start/supermaven-vim
```

---

## 2. Autentikasi

**Opsi A: Free Tier (tanpa API key):**
```vim
:SupermavenUseFree
" ikuti link aktivasi yang muncul di popup
```

**Opsi B: API Key:**
```bash
mkdir -p ~/.config/supermaven
echo '{"apiKey":"sm-..."}' > ~/.config/supermaven/config.json
# atau
:SupermavenAuth sm-xxxx
```

Cek:
```vim
:SupermavenStatus  " running / not running
:SupermavenShowLog  " tail -f $(find /tmp -name "*supermaven.log" | head -n1)
```

---

## 3. Cara Pakai

Ketik seperti biasa — ghost putih muncul otomatis (atau `Alt+Space` jika `g:supermaven_manual`).

| Aksi | Keymap |
| :--- | :--- |
| Terima semua | `Alt+g` |
| Hapus | `Alt+]` |
| Terima 1 kata | `Alt+j` |
| Trigger manual | `Alt+Space` |

Statusline:
```vim
set statusline+=%{supermaven#GetStatusString()}
```

---

## 4. Troubleshooting

**Ghost tidak muncul?**
```vim
:SupermavenStatus
:echo &filetype  " jangan kosong, cek g:supermaven_ignore_filetypes
:SupermavenShowLog
```

**Binary gagal download?**
```bash
ls -lh ~/.supermaven/binary/v20/*/sm-agent
curl -s "https://supermaven.com/api/download-path-v2?platform=linux&arch=x86_64&editor=neovim" | jq
```

**Mau pakai nvim-cmp (Neovim)?**
```lua
cmp.setup { sources = {{ name = "supermaven" }} }
```

---

## Download

Otomatis saat `PlugInstall`. Link referensi:

* https://github.com/jhonoryza/supermaven-vim
* https://github.com/supermaven-inc/supermaven-nvim (asli Neovim)

---

## Kesimpulan

1. **Siapkan `~/.vimrc`** (plug + keymap `Alt`)
2. **Install** (`:PlugInstall`)
3. **Auth** (`:SupermavenUseFree` atau file `~/.config/supermaven/config.json`)

Selesai — ghost Supermaven jalan di Vim 9 tanpa Neovim. Untuk Codeium/Windsurf lihat [Cara Install Codeium di Vim](./10-cara-install-codeium-di-vim.md).
