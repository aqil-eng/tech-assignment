# Bagian 3 — React dan TypeScript

## 3.1 — Custom Hook

Tulis sebuah custom React hook bernama `useDebounce<T>` dalam TypeScript.

Hook ini harus:
- Menerima `value` bertipe generik `T` dan `delay` dalam milidetik
- Mengembalikan nilai yang sudah di-debounce
- Membersihkan timer dengan benar saat unmount atau ketika input berubah
- Bertipe lengkap tanpa menggunakan `any`

**Persyaratan Tambahan**
- Hindari render yang tidak perlu (jangan set state jika nilai tidak berubah).

Kemudian tulis contoh singkat yang menunjukkan cara menggunakan hook ini untuk men-debounce input pencarian dan memicu panggilan API melalui `useEffect`.

---

## 3.2 — Desain Komponen

Desain dan implementasikan komponen React `<DataTable<T>>` yang sepenuhnya bertipe dalam TypeScript dengan persyaratan berikut:

- Menerima array data generik (`rows: T[]`)
- Menerima array konfigurasi `columns` yang memetakan kunci dari `T` ke:
  - label tampilan
  - fungsi `render` opsional
  - fungsi `sortValue` opsional
- Mendukung pengurutan sisi klien (client-side sorting) dengan mengklik header kolom (toggle naik/turun)
- Pengurutan harus **stabil** (jika nilai sama, pertahankan urutan asli)
- Menyertakan callback `onRowClick` yang mengembalikan data baris yang diklik, bertipe lengkap
- Menggunakan generic constraints yang sesuai sehingga `T` harus berupa objek dengan field `id` yang unik

**Anda tidak perlu memberi styling.**
