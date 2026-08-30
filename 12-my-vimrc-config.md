# My Vimrc Config

Konfigurasi `~/.vimrc` minimal yang saya pakai sehari-hari (update 31 Aug 2026 — sekarang pakai `windsurf.vim`). Fokusnya: nyaman untuk coding, indentasi konsisten, dan integrasi Windsurf/Codeium untuk autocomplete AI.

## File Lengkap

Lokasi file: `~/.vimrc`

```vim
call plug#begin()
" Tambahkan plugin Codeium
Plug 'Exafunction/windsurf.vim', { 'branch': 'main' }
call plug#end()

syntax on           " Enable syntax highlighting
set number          " Show line number
set relativenumber  " Show relative number
set tabstop=4       " Set tab width to 4 spaces
set shiftwidth=4    " Set indentation width to 4 spaces
set expandtab       " Use spaces instead of tabs
set cursorline      " Highlight the current line

imap <script><silent><nowait><expr> <C-g> codeium#Accept()
imap <script><silent><nowait><expr> <C-h> codeium#AcceptNextWord()
imap <script><silent><nowait><expr> <C-j> codeium#AcceptNextLine()
imap <C-;>   <Cmd>call codeium#CycleCompletions(1)<CR>
imap <C-,>   <Cmd>call codeium#CycleCompletions(-1)<CR>
imap <C-x>   <Cmd>call codeium#Clear()<CR>
" Trigger manual (biar tidak auto tiap ketik, uncomment jika mau manual only)
" let g:codeium_manual = v:true
imap <C-Space> <Cmd>call codeium#Complete()<CR>
imap <M-Bslash> <Cmd>call codeium#Complete()<CR>
```

---

## Penjelasan Setting Dasar

| Config | Fungsi |
| :--- | :--- |
| `syntax on` | Mengaktifkan syntax highlighting agar kode lebih mudah dibaca |
| `set number` | Menampilkan nomor baris absolut di gutter kiri |
| `set relativenumber` | Menampilkan nomor baris relatif (jarak ke cursor). Kombinasi `number` + `relativenumber` membuat navigasi `5j` / `3k` jauh lebih cepat |
| `set tabstop=4` | Lebar visual karakter Tab = 4 spasi |
| `set shiftwidth=4` | Lebar indentasi saat auto-indent (`>>`, `<<`, `=`) = 4 spasi |
| `set expandtab` | Saat menekan Tab, Vim akan memasukkan spasi, bukan karakter `\t`. Wajib untuk konsistensi di Git dan kolaborasi tim |
| `set cursorline` | Memberi highlight pada baris tempat cursor berada, memudahkan tracking posisi |

> Kombinasi `tabstop=4` + `shiftwidth=4` + `expandtab` adalah standar untuk Python, Go, dan kebanyakan style guide modern.

---

## Penjelasan Keymap Codeium

Saya tidak pakai `Tab` default dari Codeium agar tidak bentrok dengan completion lain. Semua keymap Codeium saya pindah ke `Ctrl`:

| Keymap | Fungsi Codeium |
| :--- | :--- |
| `Ctrl+g` `<C-g>` | `codeium#Accept()` — Terima **seluruh** saran ghost text |
| `Ctrl+h` `<C-h>` | `codeium#AcceptNextWord()` — Terima **satu kata** berikutnya dari saran |
| `Ctrl+j` `<C-j>` | `codeium#AcceptNextLine()` — Terima **satu baris** berikutnya dari saran |
| `Ctrl+;` `<C-;>` | `codeium#CycleCompletions(1)` — Cycle ke saran **berikutnya** |
| `Ctrl+,` `<C-,>` | `codeium#CycleCompletions(-1)` — Cycle ke saran **sebelumnya** |
| `Ctrl+x` `<C-x>` | `codeium#Clear()` — Hapus / batalkan saran yang sedang tampil |
| `Ctrl+Space` `<C-Space>` | `codeium#Complete()` — Trigger manual (jika `g:codeium_manual` aktif) |
| `Alt+\` `<M-Bslash>` | `codeium#Complete()` — Fallback trigger manual |

Atribut ` <script><silent><nowait><expr>` pada `imap` memastikan mapping berjalan tanpa delay dan tanpa echo di command line.

### Kenapa Dipisah Accept Word / Line?

Kadang saran Codeium terlalu panjang. Dengan `AcceptNextWord` dan `AcceptNextLine` kita bisa ambil sebagian saja, bukan langsung seluruh blok. Lebih kontrol daripada langsung `Tab`.

---

## Cara Pakai

1. Copy config di atas ke `~/.vimrc`
2. Restart Vim atau reload dengan:

```vim
:source ~/.vimrc
```

3. Pastikan Codeium sudah terinstall dan terautentikasi (`:Codeium Auth`). Lihat panduan lengkap di [Cara Install Codeium di Vim](./10-cara-install-codeium-di-vim.md).

---

## Tips Tambahan

* Jika `Ctrl+;` tidak terdeteksi di terminal tertentu (misal macOS Terminal), ganti ke `Alt` atau `Leader` sesuai preferensi.
* Untuk menonaktifkan mapping Tab bawaan Codeium sepenuhnya, tambahkan di atas semua `imap`:

```vim
let g:codeium_no_map_tab = 1
```
