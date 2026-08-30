# Cara Install Codeium di Vim

Codeium adalah AI autocomplete gratis (alternatif GitHub Copilot) untuk Vim dan Neovim. Plugin akan otomatis download Language Server sesuai OS — tidak perlu download manual.

## Prasyarat (Cek Dulu)

Buka terminal, pastikan versi cukup:

```bash
vim --version | head -n1      # harus 9.0.0185 atau lebih baru
# atau untuk Neovim
nvim --version | head -n1      # harus 0.6 atau lebih baru
git --version                  # harus ada
```

* Akun Codeium/Windsurf — daftar gratis di [windsurf.com](https://windsurf.com) (dulu codeium.com). Setelah login, buka **Settings → API Key** untuk copy key `sk-...` (Mac) atau `sk-ws-...` (Linux/Windsurf).

---

## 0. Siapkan `~/.vimrc` Dulu

> Wajib sebelum install plugin — biar keymap tidak bentrok `Tab`.

**1. Install vim-plug dulu (sekali saja, jika belum ada):**
```bash
# Vim
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
# Neovim
curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

**2. Buat/edit `~/.vimrc` (Vim) atau `~/.config/nvim/init.vim` (Neovim):**

`~/.vimrc` minimal yang dipakai sekarang (sinkron `12-my-vimrc-config.md`):

```vim
call plug#begin()
Plug 'Exafunction/windsurf.vim', { 'branch': 'main' }
" Linux fix (cpo + sk-ws): Plug 'jhonoryza/windsurf-vim-linux'
call plug#end()

syntax on           " Warna syntax biar kode mudah dibaca
set number          " Nomor baris kiri
set relativenumber  " Nomor relatif (biar 5j/3k cepat)
set tabstop=4
set shiftwidth=4
set expandtab       " Tab jadi spasi (standar Python/Go)
set cursorline      " Highlight baris cursor

" Keymap Windsurf (pakai Ctrl, bukan Tab biar tidak bentrok)
imap <script><silent><nowait><expr> <C-g> codeium#Accept()              " Terima semua saran
imap <script><silent><nowait><expr> <C-h> codeium#AcceptNextWord()      " Terima 1 kata
imap <script><silent><nowait><expr> <C-j> codeium#AcceptNextLine()      " Terima 1 baris
imap <C-;>   <Cmd>call codeium#CycleCompletions(1)<CR>   " Saran berikutnya
imap <C-,>   <Cmd>call codeium#CycleCompletions(-1)<CR>  " Saran sebelumnya
imap <C-x>   <Cmd>call codeium#Clear()<CR>                " Batal
" Trigger manual (opsional, kalau mau tidak auto)
" let g:codeium_manual = v:true
imap <C-Space> <Cmd>call codeium#Complete()<CR>
imap <M-Bslash> <Cmd>call codeium#Complete()<CR>
```

> Mac pakai config di atas langsung jalan. Linux lihat Catatan Khusus di bawah.

---

## 1. Install Plugin

Di Vim jalankan:

```vim
:PlugInstall
```

Tunggu sampai `windsurf.vim` terinstall, lalu restart Vim (`:qa` + buka lagi).

### Manual (tanpa vim-plug)

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

> Cara lama `:Codeium Auth` buka browser + `curl api.codeium.com` sudah deprecated, jadi langsung tulis file.

**Dapat key dulu:** Login di [windsurf.com](https://windsurf.com) → **Settings → API Key** → Copy `sk-...` atau `sk-ws-...`

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

**Verifikasi (wajib untuk new user):**
```bash
cat ~/.config/Codeium/config.json  # Linux
cat ~/.codeium/config.json          # Mac
```
Di Vim:
```vim
:echo codeium#command#ApiKey()[:12]  " harus keluar sk-... / sk-ws-...
:echo codeium#Enabled()              " harus v:true (kalau 0 cek :set filetype?)
```

> Language Server auto-download saat pertama buka Vim. Jika masih `.gz` lihat Troubleshooting.

---

## 3. Cara Pakai

Ketik seperti biasa — akan muncul **ghost text** (tulisan abu-abu transparan) sebagai saran. Tidak perlu apa-apa, saran muncul otomatis (atau tekan `Ctrl+Space` kalau `g:codeium_manual`).

| Aksi | Tombol di `~/.vimrc` |
| :--- | :--- |
| Terima semua saran | `Ctrl+g` |
| Terima 1 kata | `Ctrl+h` |
| Terima 1 baris | `Ctrl+j` |
| Saran berikutnya / sebelumnya | `Ctrl+;` / `Ctrl+,` |
| Batalkan saran | `Ctrl+x` |
| Munculkan manual | `Ctrl+Space` / `Alt+\` |

Cek status:
```vim
:Codeium Enable / Disable
:echo codeium#GetStatusString()  " 3/8 = saran ke-3 dari 8, * = loading, 0 = tidak ada saran
```

---

## 4. Troubleshooting (New User)

**Saran tidak muncul?**
```vim
:echo codeium#Enabled()  " kalau 0, cek filetype: :set filetype? (harus python/js/etc, bukan kosong)
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

**Tab bentrok dengan plugin lain?**
```vim
let g:codeium_no_map_tab = 1  " taruh di atas semua imap di ~/.vimrc
```

**`E723` / `E10: \ should be followed by` di Vim 9.2 Linux?**
Vim 9.2 Linux punya `cpo` dengan `C` sehingga file `autoload/codeium.vim` gagal parse. Fix sudah di `jhonoryza/windsurf-vim-linux` (`set cpo&vim`).

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

1. **Siapkan `~/.vimrc`** dulu (install vim-plug + plug + keymap)
2. **Install plugin** (`:PlugInstall`)
3. **Pasang API key** ke file (`~/.codeium` Mac / `~/.config/Codeium` Linux) — cek `:echo codeium#command#ApiKey()`

Selesai — buka file `.py` / `.js`, ketik, ghost text muncul.
