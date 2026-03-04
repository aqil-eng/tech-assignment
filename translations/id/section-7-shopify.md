# Bagian 7 — Shopify (Opsional)

Bagian ini bersifat opsional. Menyelesaikannya menunjukkan keahlian tambahan dalam pengembangan ekosistem Shopify.

---

## 7.1 — Templating Liquid

Tulis cuplikan Liquid untuk halaman produk yang:

1. Merender galeri gambar produk dengan atribut `srcset` responsif menggunakan filter CDN gambar Shopify
2. Menampilkan pemilih varian (variant picker) yang memperbarui harga dan ketersediaan secara dinamis menggunakan blok `<script type="application/json">` untuk serialisasi data varian
3. Menangani kasus di mana produk tidak memiliki varian yang tersedia dengan baik (menampilkan status "Sold Out" dengan formulir notifikasi stok kembali / back-in-stock)
4. Menggunakan tag `{% render %}` Liquid untuk menyertakan komponen yang dapat digunakan ulang, dan jelaskan mengapa `{% render %}` lebih disukai daripada `{% include %}`

**Persyaratan Tambahan**
- Sebutkan 1 pertimbangan performa (lazy loading, responsive widths, dll.)

---

## 7.2 — Pengembangan Tema

Jawab pertanyaan berikut:

**1. Jelaskan struktur tema Shopify Online Store 2.0. Apa itu sections, blocks, dan app extensions, dan bagaimana mereka berhubungan melalui tag `{% schema %}`?**

**2. Anda perlu menambahkan section konten berbasis metafield kustom (misalnya FAQ) ke halaman produk. Jelaskan seluruh prosesnya: mendefinisikan metafield, membuat schema section, dan merender data di Liquid.**

**3. Jelaskan bagaimana `settings_schema.json` berbeda dari `{% schema %}` sebuah section, dan kapan Anda akan menggunakan masing-masing.**

---

## 7.3 — Custom Storefront / Headless

Tulis contoh kode (React + TypeScript) yang menggunakan Shopify Storefront API (GraphQL) untuk:

1. Mengambil 12 produk pertama dari sebuah koleksi, termasuk judul, harga, handle, dan URL gambar utama (featured image)
2. Mengimplementasikan paginasi berbasis cursor untuk memuat lebih banyak produk
3. Menggunakan tipe TypeScript yang tepat, dibuat dari atau dimodelkan berdasarkan schema Storefront API

Jelaskan secara singkat:
- Kapan Anda akan memilih pendekatan headless/Hydrogen dibanding tema Liquid tradisional
- Dua trade-off dari pendekatan headless (satu operasional, satu terkait fitur commerce)
