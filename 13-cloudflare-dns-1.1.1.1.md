# Panduan DNS Cloudflare 1.1.1.1 : Pilih Filter Sesuai Kebutuhan

Cloudflare menyediakan layanan DNS publik gratis `1.1.1.1` yang terkenal cepat, privat, dan global. Selain versi standar tanpa filter, Cloudflare juga menyediakan varian dengan filtering otomatis untuk blokir malware dan konten dewasa — cukup ganti alamat DNS di perangkat kamu.

## Ringkasan Varian DNS Cloudflare

| Varian | Alamat DNS | Fungsi |
| :--- | :--- | :--- |
| **Standard (No Filter)** | `1.1.1.1` dan `1.0.0.1` | DNS tercepat tanpa filtering. Hanya untuk lookup cepat dan privat |
| **Block Malware** | `1.1.1.2` dan `1.0.0.2` | Blokir malware & phishing otomatis di level jaringan |
| **Block Malware + Adult Content** | `1.1.1.3` dan `1.0.0.3` | Blokir malware + konten dewasa (cocok untuk family / parental control) |

---

## 1. Standard - `1.1.1.1` dan `1.0.0.1`

Ini adalah DNS publik standar Cloudflare:

* **Cepat** — jaringan global Cloudflare (rata-rata <15ms)
* **Privat** — tidak menjual data, support DNS-over-HTTPS (DoH) dan DNS-over-TLS (DoT)
* **Zero filtering** — tidak memblokir konten apapun, semua lookup diteruskan apa adanya

Cocok jika kamu hanya butuh kecepatan dan privasi tanpa pemblokiran.

```
1.1.1.1
1.0.0.1
```

## 2. Block Malware & Phishing - `1.1.1.2` dan `1.0.0.2`

Varian ini memblokir ancaman di level jaringan sebelum halaman sempat dimuat:

* Memblokir domain malware yang diketahui
* Memblokir situs phishing / penipuan
* Tetap cepat dan privat seperti varian standar

Cocok untuk penggunaan umum, kantor, atau laptop pribadi yang ingin proteksi ekstra tanpa memblokir konten dewasa.

```
1.1.1.2
1.0.0.2
```

## 3. Block Malware + Adult Content - `1.1.1.3` dan `1.0.0.3`

Varian paling ketat, menggabungkan proteksi malware + filter konten dewasa:

* Semua proteksi dari `1.1.1.2` (malware & phishing)
* Ditambah blokir situs dewasa / pornografi
* Ideal untuk **Family DNS** — rumah dengan anak-anak, sekolah, atau jaringan publik

```
1.1.1.3
1.0.0.3
```

---

## Cara Ganti DNS

### di Router (berlaku untuk semua device di jaringan)

Masuk ke admin router `192.168.1.1` → cari menu DNS / WAN → ganti Primary & Secondary DNS sesuai varian di atas → Save & Reboot router.

### di Windows

Settings → Network & Internet → Change adapter options → klik kanan koneksi aktif → Properties → IPv4 → Use the following DNS server addresses → masukkan `1.1.1.x` dan `1.0.0.x`.

### di macOS

System Settings → Network → pilih koneksi → Details → DNS → hapus DNS lama → tambah `1.1.1.x` dan `1.0.0.x`.

### di Android / iOS

Settings → Wi-Fi → tap jaringan yang terhubung → Configure DNS / IP Settings → Manual → tambah server DNS.

### DoH / DoT (opsional, lebih privat)

* DoH URL: `https://cloudflare-dns.com/dns-query`
* DoT Host: `one.one.one.one` atau `1.1.1.1`

Untuk varian filtered, gunakan:
* Malware: `https://security.cloudflare-dns.com/dns-query`
* Family: `https://family.cloudflare-dns.com/dns-query`

---

## Mana yang Harus Dipilih?

* **Butuh paling cepat tanpa blokir?** → `1.1.1.1` / `1.0.0.1`
* **Butuh proteksi malware/phishing?** → `1.1.1.2` / `1.0.0.2`
* **Butuh proteksi lengkap untuk anak/keluarga?** → `1.1.1.3` / `1.0.0.3`

Semua varian gratis, tidak perlu registrasi, dan bisa diganti kapan saja.

---

## Referensi

* Dokumentasi resmi: https://one.one.one.one/family/
* Cloudflare Blog: https://blog.cloudflare.com/introducing-1-1-1-1-for-families/
