# Bagian 1 — SCSS

## Token Bersama (Gunakan di seluruh bagian ini)

```scss
$tokens: (
  gutter: 24px,
  colors: (
    primary: #1d4ed8,
    danger: #dc2626,
    muted: #6b7280
  ),
  breakpoints: (
    sm: 640px,
    md: 768px,
    lg: 1024px
  )
);
```

---

## 1.1 — Mixin dan Fungsi

Tulis sebuah SCSS mixin bernama `responsive-grid` yang menerima:

- `$columns` (angka)
- `$gutter` (ukuran panjang)
- `$breakpoint` (opsional: ukuran panjang ATAU kunci token breakpoint seperti `md`)

Mixin ini harus menghasilkan layout CSS Grid yang:

- Menggunakan `$columns` di atas breakpoint
- Menjadi satu kolom di bawah breakpoint
- Menerapkan jarak antar elemen menggunakan `gap`

Sertakan juga fungsi SCSS pelengkap bernama `grid-width` yang menghitung lebar satu kolom sebagai persentase, dengan memperhitungkan gutter.

**Persyaratan Tambahan**
- Jika `$breakpoint` tidak diberikan, gunakan nilai default dari `$tokens.breakpoints.md`.
- Jika `$breakpoint` diberikan sebagai kunci (`md`, `lg`), ambil nilainya dari `$tokens`.
- `grid-width()` harus mengembalikan persentase yang dibulatkan ke **4 angka desimal**.

---

## 1.2 — Arsitektur dan Nesting

SCSS berikut ini bisa dikompilasi tetapi dianggap ditulis dengan buruk.

1. Identifikasi **minimal empat** masalah spesifik (jelaskan secara eksplisit).
2. Tulis ulang mengikuti praktik terbaik:
   - Penamaan BEM
   - Maksimal **2 level nesting**
   - Penggunaan variabel/maps
   - Peningkatan kemudahan pemeliharaan (maintainability)

```scss
.page {
  div {
    background: #ff0000;
    .page_title {
      font-size: 30px;
      color: #ff0000;
      span {
        font-size: 12px;
        a {
          color: blue;
          &:hover {
            color: blue;
          }
        }
      }
    }
    .page_title.active {
      color: green;
    }
  }
}
```
