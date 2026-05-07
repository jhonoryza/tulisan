# Design Principle

Design principle adalah panduan atau aturan umum untuk membuat struktur kode yang:

- Mudah dipahami
- Mudah dirawat (maintainable)
- Mudah dikembangkan
- Tidak rapuh saat diubah

Mereka bukan aturan wajib seperti sintaks, tetapi best practice
yang sudah terbukti membantu menjaga kualitas perangkat lunak.

| Prinsip | Termasuk                         | Fokus                   |
| ------- | -------------------------------- |-------------------------|
| SOLID   | Object-Oriented Design Principle | Struktur OOP yang sehat |
| KISS    | General Design Principle         | Sederhana lebih baik    |
| DRY     | Code Quality Principle           | Hindari duplikasi       |
| YAGNI   | Agile / Development Principle    | Jangan over-engineer    |

## SOLID

Sekumpulan 5 prinsip OOP (Object-Oriented Programming):

S - Single Responsibility Principle

- Satu class / module hanya punya 1 tanggung jawab.
- ❌ Jangan biarkan satu class menangani database, validasi, dan pengiriman email sekaligus.
- ✅ Pisahkan jadi beberapa class.

O - Open/Closed Principle

- Kode harus terbuka untuk dikembangkan, tapi tertutup untuk diubah.
- Artinya: tambahkan fitur tanpa mengganggu kode lama.

L - Liskov Substitution Principle

- Subclass harus bisa menggantikan parent class tanpa merusak program.

I - Interface Segregation Principle

- Jangan paksa class mengimplementasikan interface yang tidak dia butuhkan.

D - Dependency Inversion Principle

- Bergantung pada abstraction (interface), bukan pada kelas konkret.

## KISS - Keep It Simple, Stupid

- Buat solusi sesederhana mungkin.
- ❌ Jangan buat sistem yang sangat kompleks jika masalahnya sederhana.
- ✅ Pilih solusi paling simpel yang bekerja dengan baik.

## DRY - Don't Repeat Yourself

- Jangan duplikasi kode / logic.
- Jika ada logika yang sama di banyak tempat → jadikan fungsi atau komponen
yang dapat digunakan kembali.

## YAGNI - You Aren’t Gonna Need It

- Jangan buat fitur yang belum dibutuhkan.
- ❌ "Nanti mungkin dibutuhkan..."
- ✅ Implementasikan hanya yang benar-benar diperlukan saat ini.
