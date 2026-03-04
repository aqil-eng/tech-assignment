# Bagian 5 — PHP 8+

## 5.1 — Fitur PHP Modern

Tulis sebuah class PHP 8.2+ bernama `ProductDTO` yang mendemonstrasikan:

- Constructor property promotion dengan properti `readonly`
- Sebuah backed enum bernama `ProductStatus` (`active`, `draft`, `archived`) yang digunakan sebagai tipe properti
- Sebuah static factory method dengan named arguments `fromArray(array $data): self`
- Konstruksi aman: tolak kunci yang tidak dikenal dan kumpulkan error validasi (jangan gagal lebih awal / fail fast)
- Sebuah method yang menggunakan salah satu dari:
  - Ekspresi `match` DAN first-class callable syntax
  **ATAU**
  - Ekspresi `match` DAN fibers

---

## 5.2 — Desain API

Anda sedang membangun endpoint JSON REST API dalam PHP (tanpa framework, atau micro-framework pilihan Anda).

Tulis handler untuk:

`PATCH /api/products/{id}`

Handler ini harus:

1. Memvalidasi body JSON yang masuk, hanya menerima:
   - `name`
   - `price`
   - `status`
2. Menggunakan enum `ProductStatus` dari soal 5.1 untuk validasi status
3. Mengembalikan kode status HTTP yang sesuai (`200`, `400`, `404`, `422`) dengan respons error JSON
4. Mengimplementasikan sanitasi input terhadap XSS
5. Menggunakan return types dan union types yang type-safe

**Persyaratan Tambahan**
- Tunjukkan format JSON error yang tepat yang Anda kembalikan (harus mencakup `code`, `message`, dan `fields` opsional).
