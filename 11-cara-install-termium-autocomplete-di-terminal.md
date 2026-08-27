# Cara Install Termium (Autocomplete AI di Terminal)

Termium adalah tool autocomplete untuk terminal dari Codeium. Berbeda dengan autocomplete di editor, Termium bekerja langsung di shell (bash/zsh) dan memberikan saran command berdasarkan history dan output terminal kamu. Saat ini Termium masih dalam tahap **alpha/prototype**, jadi masih dalam pengembangan aktif.

Tersedia untuk **macOS** dan **Linux**.

---

## 1. Installation & Setup

Instalasi cukup dengan satu perintah di terminal.

### Langkah 1: Jalankan Script Installer

Buka terminal dan eksekusi perintah berikut:

```bash
curl -L https://github.com/Exafunction/codeium/releases/download/termium-v0.2.1/install.sh | bash
```

> **Catatan:** Panduan lama masih menggunakan `termium-v0.2.0`. Disarankan langsung pakai versi terbaru `v0.2.1` karena memperbaiki beberapa bug instalasi.

### Langkah 2: Autentikasi

Setelah terinstall, hubungkan Termium dengan akun Codeium:

```bash
termium auth
```

Ikuti instruksi di terminal untuk login dan memasukkan API token.

### Langkah 3: Install Shell Hook

Agar Termium terintegrasi dengan sesi terminal kamu, jalankan:

```bash
termium shell-hook install
```

Perintah ini akan menambahkan konfigurasi otomatis ke file shell kamu (`.bashrc` atau `.zshrc`).

### Langkah 4: Restart Terminal

Tutup dan buka kembali terminal agar perubahan konfigurasi aktif.

Selesai. Termium siap digunakan.

---

## 2. Cara Kerja (How It Works)

Termium berjalan sebagai layer di antara kamu dan terminal. Ia membaca history dan output perintah sebelumnya untuk memberikan saran yang relevan.

| Aksi | Cara |
| :--- | :--- |
| **Ghost Text** | Saat mengetik, akan muncul teks abu-abu (ghost text) sebagai saran command lengkap |
| **Terima Saran** | Tekan `Tab` untuk menerima dan melengkapi command |
| **Abaikan Saran** | Lanjutkan mengetik atau tekan `Esc` untuk mengabaikan |

Contoh penggunaan nyata:

* Setelah `git status`, Termium bisa menyarankan file yang perlu di-`git add`
* Saat mengetik `kubectl logs`, ia bisa melengkapi nama pod secara otomatis
* Sangat membantu untuk pola command yang repetitif / boilerplate

---

## 3. Pro Tip: Mencari Command Baru dengan Bahasa Natural

Jika kamu tidak hafal command yang ingin digunakan, kamu bisa memanfaatkan Termium dengan trik `echo`:

```bash
echo "show the logs for my kubernetes pod"
```

Setelah mengetik deskripsi natural language tersebut, Termium akan membaca konteksnya dan menyarankan command yang benar, misalnya:

```bash
kubectl logs [pod-name]
```

Cocok untuk eksplorasi command baru tanpa harus Googling.

---

## 4. Cara Menonaktifkan / Uninstall

Jika ingin menonaktifkan Termium, cukup hapus baris yang ditambahkan Termium di file konfigurasi shell:

* `~/.bashrc` untuk Bash
* `~/.zshrc` untuk Zsh

Hapus blok yang mengandung `termium` atau `shell-hook`, lalu restart terminal.

---

## Download & Release

Binary Termium dan Language Server terbaru bisa diunduh di halaman release resmi Codeium:

* **Download Page:** https://github.com/Exafunction/codeium/releases?page=1#release-language-server-v2.12.5

---

## Kesimpulan

1. Install: `curl ... install.sh | bash`
2. Auth: `termium auth`
3. Hook: `termium shell-hook install`
4. Restart terminal dan gunakan `Tab` untuk menerima saran

Dengan Termium, aktivitas di terminal jadi lebih cepat, terutama untuk command yang sering diulang seperti `git`, `docker`, dan `kubectl`.
