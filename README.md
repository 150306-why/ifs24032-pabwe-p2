# 11s24032-pabwe-p2

Praktikum Pemrograman Aplikasi Berbasis Web (PABWE) 2026 — **P2: Praktik CSS & CSS Framework**

| | |
|---|---|
| Nama | Wahyu Nainggolan |
| NIM | 11S24032 |
| Program Studi | S-1 Informatika, Institut Teknologi Del |
| Tema | **NovaMind AI** — website perusahaan jasa Artificial Intelligence (simulasi) |

## Halaman

| Halaman | File | Teknologi |
|---|---|---|
| Landing Page jasa AI | `index.html` | HTML + CSS murni (external CSS `assets/css/style.css`) |
| Daftar Blog AI | `blog.html` | Bootstrap 5.3.8 + Bootstrap Icons 1.13.1 |
| Detail Blog AI | `blog-detail.html` | Bootstrap 5.3.8 + Bootstrap Icons 1.13.1 |
| Curriculum Vitae | `cv.html` | Tailwind CSS 4 (Play CDN, `@theme`) |

`blog-detail.html` menampilkan 6 artikel berbeda. Artikel dipilih lewat query string, contoh
`blog-detail.html?id=etika-ai-bisnis`. Tanpa parameter (atau dengan id yang tidak dikenal), halaman menampilkan
artikel default. Judul dan tombol “Baca selengkapnya” di `blog.html` sudah mengarah ke id yang sesuai.

## Menjalankan

Tidak perlu build. Buka `index.html` di browser (atau gunakan ekstensi *Live Server* di VS Code).
Butuh koneksi internet untuk memuat CDN (Bootstrap, Bootstrap Icons, Tailwind, Google Fonts).

## Struktur folder

```
11s24032-pabwe-p2/
├── index.html              # Landing page (CSS)
├── blog.html               # Daftar blog (Bootstrap 5)
├── blog-detail.html        # Detail blog (Bootstrap 5)
├── cv.html                 # CV (Tailwind 4)
├── assets/
│   ├── css/
│   │   ├── style.css             # External CSS landing page
│   │   └── bootstrap-custom.css  # Penyesuaian kecil untuk halaman Bootstrap (warna brand, font)
│   └── img/
│       ├── logo.svg              # Logo brand (dipakai semua halaman)
│       ├── hero.svg, avatar.svg
│       └── cover-*.svg           # Cover artikel blog (aset lokal)
└── README.md
```

## Catatan teknis

- Identitas visual konsisten di semua halaman: logo/nama brand yang sama, palet cyan–violet di atas latar gelap,
  font Space Grotesk (heading) dan Inter (teks).
- Semantic HTML5 (`header`, `nav`, `main`, `section`, `article`, `aside`, `footer`), skip link, `aria-*` pada
  navigasi, dan fokus keyboard yang terlihat.
- Responsive: landing page memakai Flexbox, CSS Grid, dan `@media`; blog memakai grid Bootstrap (`row`/`col-*`);
  CV memakai prefix `md:` Tailwind.
- Seluruh gambar berupa SVG lokal sehingga tidak bergantung pada URL eksternal.
- Aset CDN dipin ke versi tertentu dan memakai Subresource Integrity (SRI).
- Form kontak (landing page) dan komentar (detail blog) hanya simulasi di sisi klien — belum terhubung ke backend.
  Input pengguna ditampilkan dengan `textContent` (bukan `innerHTML`) untuk mencegah XSS.
- Konten perusahaan, statistik, dan artikel bersifat fiktif untuk keperluan praktikum.
