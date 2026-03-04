# Bagian 2 — Tailwind CSS

## 2.1 — Komposisi Utility

Tanpa menggunakan `@apply`, buatlah komponen card responsif menggunakan hanya class utility Tailwind (dalam JSX/HTML).

**Persyaratan**
- Lebar maksimal `400px`, lebar penuh di perangkat mobile
- Bagian gambar atas dengan rasio aspek `16:9`
  *(jangan gunakan `aspect-video`; implementasi secara manual)*
- Bagian konten dengan judul, deskripsi, dan tombol CTA (call-to-action)
- Saat di-hover, card harus terangkat dengan transisi bayangan yang halus
- Dukungan dark mode untuk warna latar belakang dan teks
- CTA harus memiliki styling ring `focus-visible`

---

## 2.2 — Konfigurasi Tailwind

Tulis cuplikan `tailwind.config.ts` yang mencakup semua hal berikut:

1. Memperluas tema default dengan palet warna kustom bernama `brand` (gradasi `50` sampai `900`).
2. Menambahkan `fontFamily` kustom bernama `display` yang fallback ke `sans-serif`.
3. Mendaftarkan plugin kustom yang menambahkan utility `.text-balance` (mengatur `text-wrap: balance`).
4. Mengonfigurasi content paths yang sesuai untuk proyek Next.js App Router.
5. Plugin harus ditulis tanpa tipe `any`.
