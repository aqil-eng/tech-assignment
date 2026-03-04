# Bagian 6 — Vite dan Build Tooling

## 6.1 — Konfigurasi

Tulis `vite.config.ts` untuk proyek React + TypeScript yang:

1. Mengonfigurasi path alias sehingga `@/components/*` dipetakan ke `./src/components/*`
2. Menyiapkan proxy untuk request `/api` ke `http://localhost:8000` selama pengembangan (development)
3. Mengonfigurasi build untuk menghasilkan vendor chunk terpisah untuk `react` dan `react-dom`
4. Menambahkan validasi environment variable yang memastikan `VITE_API_URL` didefinisikan pada saat build

**Persyaratan Tambahan**
- Sertakan cuplikan `tsconfig.json` `compilerOptions.paths` yang sesuai.

---

## 6.2 — Konsep

Jawab pertanyaan berikut dalam 2-3 kalimat masing-masing:

**1. Bagaimana arsitektur dev server Vite berbeda dari Webpack, dan mengapa ini lebih cepat?**

**2. Apa peran Rollup dalam build produksi Vite, dan kapan Anda mungkin memilih esbuild sebagai gantinya?**

**3. Anda menyadari bahwa sebuah library pihak ketiga menyebabkan reload halaman penuh alih-alih pembaruan HMR. Apa penyebab paling mungkin, dan bagaimana Anda akan memperbaikinya?**
