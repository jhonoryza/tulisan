# Cara Install Codeium di Vim

Codeium adalah AI autocomplete gratis (alternatif GitHub Copilot) yang bisa digunakan langsung di Vim dan Neovim. Plugin `codeium.vim` akan otomatis mengunduh dan mengelola Language Server yang sesuai dengan sistem operasi kamu.

## Prasyarat

* Vim 9.0+ atau Neovim 0.6+
* Git terinstall
* Akun Codeium (bisa daftar gratis di [codeium.com](https://codeium.com))

---

## 1. Install Plugin

### Opsi A: Menggunakan vim-plug (Paling Mudah)

Tambahkan baris ini ke file konfigurasi kamu:

* Untuk **Vim**: `~/.vimrc`
* Untuk **Neovim**: `~/.config/nvim/init.vim` atau `init.lua` (dengan syntax vim-plug)

```vim
Plug 'Exafunction/codeium.vim'
```

Kemudian buka Vim dan jalankan:

```vim
:PlugInstall
```

### Opsi B: Install Manual (Tanpa Plugin Manager)

Jika kamu tidak menggunakan plugin manager, clone langsung ke folder package Vim:

```bash
git clone https://github.com/Exafunction/codeium.vim ~/.vim/pack/Exafunction/start/codeium.vim
```

Untuk Neovim, path-nya:

```bash
git clone https://github.com/Exafunction/codeium.vim ~/.local/share/nvim/site/pack/Exafunction/start/codeium.vim
```

Setelah selesai, restart Vim/Neovim.

---

## 2. Autentikasi

Setelah plugin terpasang, buka Vim dan jalankan perintah:

```vim
:Codeium Auth
```

Langkah selanjutnya:

1. Browser akan terbuka otomatis ke halaman login Codeium.
2. Login / daftar akun, kemudian copy API token yang diberikan.
3. Paste token tersebut kembali ke terminal Vim saat diminta.
4. Jika berhasil, akan muncul notifikasi `Codeium: Authenticated`.

> Language Server akan otomatis terunduh saat pertama kali autentikasi berhasil. Tunggu beberapa detik hingga selesai.

---

## 3. Cara Penggunaan

Codeium akan otomatis memberikan saran kode berwarna abu-abu (ghost text) saat kamu mengetik.

| Aksi | Shortcut Default |
| :--- | :--- |
| Terima saran | `Tab` |
| Batalkan saran | `Ctrl + ]` |
| Saran berikutnya | `Alt + ]` |
| Saran sebelumnya | `Alt + [` |

Kamu juga bisa cek status dengan:

```vim
:Codeium Enable
:Codeium Disable
:Codeium Status
```

---

## 4. Troubleshooting

**1. Saran tidak muncul?**
Pastikan kamu sudah `:Codeium Auth` dan cek status dengan `:Codeium Status`. Coba `:Codeium Enable` jika status masih disabled.

**2. Language Server gagal download?**
Cek koneksi internet dan pastikan Vim memiliki akses untuk mengeksekusi binary di `~/.vim/pack/Exafunction/start/codeium.vim/`.

**3. Konflik dengan Tab?**
Jika `Tab` bertabrakan dengan plugin lain (seperti CoC atau Supertab), kamu bisa mapping ulang di `.vimrc`:

```vim
let g:codeium_no_map_tab = 1
imap <script><silent><nowait><expr> <C-g> codeium#Accept()
```

---

## Download & Release

Download Language Server dan binary terbaru Codeium tersedia di halaman release resmi:

* **Download Page:** https://github.com/Exafunction/codeium/releases?page=1#release-language-server-v2.12.5

---

## Kesimpulan

Cukup 2 langkah utama untuk menggunakan Codeium di Vim:

1. **Install plugin** via `vim-plug` atau manual clone
2. **Autentikasi** dengan `:Codeium Auth`

Setelah itu kamu sudah bisa menikmati autocomplete AI gratis langsung di dalam Vim/Neovim tanpa konfigurasi tambahan.
