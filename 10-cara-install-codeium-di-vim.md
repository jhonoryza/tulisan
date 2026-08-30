# Cara Install Codeium di Vim

Codeium adalah AI autocomplete gratis (alternatif GitHub Copilot) yang bisa dipakai di Vim dan Neovim. Plugin akan otomatis download Language Server sesuai OS.

## Prasyarat

* Vim 9.0+ atau Neovim 0.6+
* Git
* Akun Codeium/Windsurf ([codeium.com](https://codeium.com) / [windsurf.com](https://windsurf.com))

---

## 0. Siapkan `~/.vimrc` Dulu

> Wajib sebelum `PlugInstall` — biar keymap tidak bentrok `Tab`.

`~/.vimrc` minimal yang dipakai sekarang (sinkron `12-my-vimrc-config.md`):

```vim
call plug#begin()
Plug 'Exafunction/windsurf.vim', { 'branch': 'main' }
" Linux fix (cpo + sk-ws): Plug 'jhonoryza/windsurf-vim-linux'
call plug#end()

syntax on
set number
set relativenumber
set tabstop=4
set shiftwidth=4
set expandtab
set cursorline

imap <script><silent><nowait><expr> <C-g> codeium#Accept()
imap <script><silent><nowait><expr> <C-h> codeium#AcceptNextWord()
imap <script><silent><nowait><expr> <C-j> codeium#AcceptNextLine()
imap <C-;>   <Cmd>call codeium#CycleCompletions(1)<CR>
imap <C-,>   <Cmd>call codeium#CycleCompletions(-1)<CR>
imap <C-x>   <Cmd>call codeium#Clear()<CR>
" Trigger manual (opsional)
" let g:codeium_manual = v:true
imap <C-Space> <Cmd>call codeium#Complete()<CR>
imap <M-Bslash> <Cmd>call codeium#Complete()<CR>
```

> Mac pakai config di atas langsung jalan. Linux lihat Catatan Khusus di bawah.

---

## 1. Install Plugin

### Opsi A: vim-plug (disarankan)

```vim
:PlugInstall
```
Restart Vim.

### Opsi B: Manual

```bash
# Vim
git clone https://github.com/Exafunction/windsurf.vim ~/.vim/pack/Exafunction/start/windsurf.vim
# atau Linux fix
git clone https://github.com/jhonoryza/windsurf-vim-linux ~/.vim/pack/Exafunction/start/windsurf.vim

# Neovim
git clone https://github.com/Exafunction/windsurf.vim ~/.local/share/nvim/site/pack/Exafunction/start/windsurf.vim
```

---

## 2. Pasang API Key (Tanpa Browser)

`:Codeium Auth` original buka browser + `curl https://api.codeium.com/register_user/` — sekarang deprecated, langsung tulis file:

**Mac** (`~/.codeium/config.json`):
```bash
mkdir -p ~/.codeium
echo '{"apiKey":"sk-..."}' > ~/.codeium/config.json
# atau di Vim:
:Codeium Auth sk-xxxx
```

**Linux** (Windsurf `sk-ws-`, `~/.config/Codeium/config.json`):
```bash
mkdir -p ~/.config/Codeium
echo '{"apiKey":"sk-ws-01-..."}' > ~/.config/Codeium/config.json
:Codeium Auth sk-ws-01-xxxx
```

Cek:
```bash
cat ~/.config/Codeium/config.json  # Linux
cat ~/.codeium/config.json          # Mac
:echo codeium#command#ApiKey()[:12]  # di Vim
```

> Language Server auto-download saat pertama buka Vim. Jika masih `.gz` lihat Troubleshooting.

---

## 3. Cara Pakai

Ghost text abu-abu muncul otomatis (atau `C-Space` kalau `g:codeium_manual`).

| Aksi | Keymap `~/.vimrc` |
| :--- | :--- |
| Terima semua | `Ctrl+g` |
| Terima kata | `Ctrl+h` |
| Terima baris | `Ctrl+j` |
| Next / Prev | `Ctrl+;` / `Ctrl+,` |
| Batal | `Ctrl+x` |
| Trigger manual | `Ctrl+Space` / `Alt+\` |

```vim
:Codeium Enable / Disable / Status
```

---

## 4. Troubleshooting

**Saran tidak muncul?** `:Codeium Status` → `:Codeium Enable`

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

**Tab bentrok?**
```vim
let g:codeium_no_map_tab = 1
```

**`E723` / `E10` di Vim 9.2 Linux?** `cpo` mengandung `C` → fix di `jhonoryza/windsurf-vim-linux` (`set cpo&vim`).

---

## 5. Catatan Khusus Linux (Windsurf)

Mac langsung jalan, Linux butuh:

1. Pakai `windsurf.vim` (bukan `codeium.vim` lama)
2. API key via file (di atas) — tanpa `register_user`
3. Binary default `~/.local/share/.codeium/bin/<sha>/language_server_linux_x64` — kalau di `~/.local/bin`, set `let g:codeium_bin = $HOME.'/.local/bin/language_server_linux_x64'`
4. Key `sk-ws-` otomatis pakai `server.windsurf.com` (fork Linux handle ini)

---

## Download (Otomatis)

Tidak perlu manual. Auto `curl` saat `PlugInstall`. Link hanya referensi jika auto gagal:

* https://github.com/Exafunction/codeium/releases

---

## Kesimpulan

1. **Siapkan `~/.vimrc`** dulu (plug + keymap)
2. **Install plugin** (`:PlugInstall`)
3. **Pasang API key** ke file (`~/.codeium` Mac / `~/.config/Codeium` Linux)

Selesai — autocomplete jalan tanpa browser.
