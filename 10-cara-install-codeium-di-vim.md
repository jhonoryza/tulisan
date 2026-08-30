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
Cek koneksi internet dan pastikan Vim memiliki akses untuk mengeksekusi binary di `~/.vim/pack/Exafunction/start/codeium.vim/`. Di Linux kadang file masih `.gz` (corrupt 4.1M) — extract manual:

```bash
ls -lh ~/.local/share/.codeium/bin/*/language_server_linux_x64.gz
rm -f ~/.local/share/.codeium/bin/*/language_server_linux_x64.gz
# biarkan plugin download ulang, atau manual:
curl -Lo /tmp/ls.gz https://github.com/Exafunction/codeium/releases/download/language-server-v1.20.8/language_server_linux_x64.gz
mkdir -p ~/.local/share/.codeium/bin/37f12b83df389802b7d4e293b3e1a986aca289c0
gzip -dc /tmp/ls.gz > ~/.local/share/.codeium/bin/37f12b83df389802b7d4e293b3e1a986aca289c0/language_server_linux_x64
chmod +x ~/.local/share/.codeium/bin/37f12b83df389802b7d4e293b3e1a986aca289c0/language_server_linux_x64
pkill -9 language_server; rm -rf /tmp/*codeium*
```

**3. Konflik dengan Tab?**
Jika `Tab` bertabrakan dengan plugin lain (seperti CoC atau Supertab), kamu bisa mapping ulang di `.vimrc`:

```vim
let g:codeium_no_map_tab = 1
imap <script><silent><nowait><expr> <C-g> codeium#Accept()
```

**4. Error `E723` / `E10: \ should be followed by` di Vim 9.2 Linux?**
Vim 9.2 di Linux `cpo` mengandung `C` sehingga `autoload/codeium.vim` gagal parse `Dictionary` dengan `\` continuation. Fix ada di fork Linux: https://github.com/jhonoryza/windsurf-vim-linux (tambah `set cpo&vim`).

---

## 5. Catatan Khusus Linux (Windsurf)

Di Mac instalasi di atas langsung jalan. Di Linux (terutama Vim 9.2, server `codeium.com` sudah deprecated untuk key `sk-ws-` Windsurf), butuh penyesuaian:

1. **Rebrand Codeium → Windsurf**: Install `windsurf.vim` (fork resmi Codeium):
   ```vim
   Plug 'Exafunction/windsurf.vim'
   " atau fix Linux: Plug 'jhonoryza/windsurf-vim-linux'
   ```

2. **API key tanpa call server**: Server `api.codeium.com/register_user` sudah tidak aktif / dipakai web lain. Plugin original `Auth` buka browser + `curl` ke `register_user` akan gagal. Fix minimal di `windsurf-vim-linux` baca langsung dari `~/.config/Codeium/config.json`:
   ```bash
   cat ~/.config/Codeium/config.json  # {"apiKey":"sk-ws-..."}
   # atau set manual tanpa browser:
   :Codeium Auth sk-ws-01-xxxx
   ```
   Tanpa `curl`, tanpa browser.

3. **Binary path**: Default `~/.local/share/.codeium/bin/<sha>/language_server_linux_x64` (bukan `~/.local/bin`). Jika kamu taro manual di `~/.local/bin`, set di `.vimrc`:
   ```vim
   let g:codeium_bin = $HOME.'/.local/bin/language_server_linux_x64'
   ```

4. **Server deprecated**: Untuk key `sk-ws-` harus pakai `server.windsurf.com` + `portal_url windsurf.com` (bukan `server.codeium.com`). Fork Linux otomatis set ini kalau key diawali `sk-ws-`.

> Di **Mac** aman tanpa custom — `cpo` dan `server.codeium.com` masih kompatibel.

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
