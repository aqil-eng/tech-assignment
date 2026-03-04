# Bagian 4 — Next.js (App Router)

## 4.1 — Pengambilan Data dan Caching

Anda sedang membangun halaman daftar produk.

Jelaskan (dengan kode) bagaimana Anda akan:

1. Membuat **Server Component** yang mengambil daftar produk dari REST API eksternal
2. Mengimplementasikan **ISR** dengan jendela revalidasi 60 detik
3. Menambahkan **loading state** menggunakan konvensi berbasis file dari App Router
4. Menangani error dengan baik menggunakan konvensi error boundary dari App Router

**Persyaratan Tambahan**
- Anda harus menunjukkan penggunaan semantik caching/revalidation Next.js yang benar (misalnya `fetch(..., { next: { revalidate } })` atau revalidasi segmen route).

Berikan struktur file dan kode yang relevan untuk:

- `page.tsx`
- `loading.tsx`
- `error.tsx`

---

## 4.2 — Server Actions dan Mutasi

Tulis sebuah Next.js Server Action yang menangani pengiriman form untuk membuat produk baru.

Action ini harus:
- Memvalidasi input menggunakan `zod`
- Menyisipkan produk ke database (Anda boleh memock panggilan DB)
- Me-revalidate path `/products` setelah penyisipan berhasil
- Mengembalikan respons sukses/error yang bertipe

**Persyaratan Tambahan**
- Tipe return harus berupa discriminated union
- Klien harus bisa mempersempit (narrow) tipe tanpa assertion

Tunjukkan bagaimana action ini digunakan dari form Client Component.

---

## 4.3 — Middleware

Tulis file `middleware.ts` yang:

1. Mengalihkan (redirect) pengguna yang belum terautentikasi ke `/login` untuk semua route di bawah `/dashboard/*`
2. Menambahkan header kustom `x-request-id` (UUID) ke setiap respons untuk pelacakan (tracing)
3. Mengimplementasikan pengecekan rate-limiting dasar menggunakan penghitung berbasis header atau cookie (implementasi konseptual boleh)

**Persyaratan Tambahan**
- Sertakan 1-2 kalimat yang menjelaskan mengapa pendekatan rate limiting ini tidak sempurna di lingkungan serverless/edge.
