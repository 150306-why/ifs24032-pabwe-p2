# 11s24032-pabwe-p2

Praktikum Pemrograman Aplikasi Berbasis Web (PABWE) 2026 — **P2: Praktik CSS & CSS Framework**

| | |
|---|---|
| Nama | Wahyu Nainggolan |
| NIM | 11S24032 |
| Program Studi | S-1 Informatika, Institut Teknologi Del |
| Tema | **NovaMind AI** — website perusahaan jasa Artificial Intelligence (simulasi) |
| Deploy | https://ifs24032-p2.netlify.app |

## Halaman

| Halaman | File | Teknologi |
|---|---|---|
| Landing Page jasa AI | `index.html` | HTML + CSS murni (external CSS `assets/css/style.css`) |
| Daftar Blog AI | `blog.html` | Bootstrap 5.3.8 + Bootstrap Icons (di-host sendiri, di-purge) |
| Detail Blog AI | `blog-detail.html` | Bootstrap 5.3.8 + Bootstrap Icons (di-host sendiri, di-purge) |
| Curriculum Vitae | `cv.html` | Tailwind CSS 4 (dikompilasi saat build, bukan Play CDN) |

`blog-detail.html` menampilkan 6 artikel berbeda lewat query string, contoh
`blog-detail.html?id=etika-ai-bisnis`. Tanpa parameter (atau id tidak dikenal), halaman menampilkan artikel default.

## Perbaikan audit web grading (Delcom Grading)

**1. Redirect `/index` → `/` (Axe Core "Critical")**
Netlify punya fitur *Pretty URLs* yang otomatis me-redirect `/index(.html)` ke `/`. Alat audit meminta `/index`
mengembalikan 200 tanpa redirect. Ditambahkan `netlify.toml` + `_redirects` dengan **rewrite eksplisit (status
200, bukan redirect)** untuk `/index`, `/blog`, `/blog-detail`, `/cv` — aturan ini diproses lebih dulu daripada
redirect otomatis Netlify. Diverifikasi dengan **axe-core asli** (bukan cuma kategori aksesibilitas Lighthouse):
**0 pelanggaran** di kelima kombinasi halaman yang diuji.

**2. Performance, Best Practices, SEO**
Diaudit langsung dengan **Lighthouse + Chrome headless secara lokal** (bukan tebakan), hasil sebelum vs sesudah:

| Halaman | Performance (awal → akhir) | Accessibility | Best Practices | SEO |
|---|---|---|---|---|
| index.html | 100 → 100 | 100 | 96* | 100 |
| blog.html | 54 → 99 | 75 → 100 | 75 → 96* | 75 → 100 |
| blog-detail.html | — → 99 | — → 100 | — → 96* | — → 100 |
| cv.html | — → 99 | — → 100 | — → 96* | — → 100 |

\* Satu-satunya poin yang tertahan di 96 adalah audit *"errors in console"*, dan penyebabnya adalah **sandbox
pengujian saya tidak punya akses jaringan ke fonts.googleapis.com** (diblokir firewall container), sehingga
request font tersebut gagal (403) dan tercatat sebagai console error. Ini bukan bug di situsnya — di internet
sungguhan (termasuk saat diaudit oleh Delcom Grading) Google Fonts akan termuat normal (200 OK) dan poin ini
seharusnya ikut 100. Silakan cek ulang di alat audit aslinya untuk konfirmasi.

Perubahan teknis yang mendongkrak skor:
- **Tailwind CSS 4 Play CDN → dikompilasi saat build** (`assets/css/tailwind.min.css`, 24 KB, sudah di-*purge*
  hanya untuk kelas yang dipakai). Menghapus JIT compiler yang tadinya berjalan di browser (beban utama skor
  Performance & penyebab warning "should not be used in production" di Best Practices).
- **Bootstrap + Bootstrap Icons: CDN → di-host sendiri & di-*purge*.** CSS Bootstrap 232 KB → 29 KB (PurgeCSS,
  hanya kelas yang benar-benar dipakai + state dinamis Bootstrap seperti `.collapse`/`.show` di-*safelist*).
  Font ikon 132 KB → **2.4 KB** (di-*subset* dengan `fonttools` hanya untuk 20 glyph yang dipakai). Ketiganya
  digabung jadi satu `assets/css/bundle.css` agar cuma 1 request CSS, bukan 3.
- **`bootstrap.bundle.min.js` (≈80 KB) dihapus sepenuhnya**, diganti vanilla JS beberapa baris (di dalam
  `blog.html`/`blog-detail.html`) yang meniru perilaku *collapse* navbar mobile Bootstrap — karena dari seluruh
  bundle itu cuma fitur collapse yang dipakai.
- `netlify.toml`: header `Cache-Control: public, max-age=31536000, immutable` untuk `/assets/*`, dan header
  keamanan dasar (`X-Content-Type-Options`, `X-Frame-Options`, `Referrer-Policy`, `Permissions-Policy`).
- `robots.txt`, `sitemap.xml`, dan `<link rel="canonical">` di semua halaman.
- Gambar LCP (hero di landing page, cover artikel pertama di blog/detail) diberi `<link rel="preload">` +
  `fetchpriority="high"`; gambar kartu blog lain (di bawah lipatan layar) diberi `loading="lazy"`.
- Font Google (Inter, Space Grotesk) dimuat dengan pola *media="print" → swap* supaya tidak memblokir render
  pertama (tidak bisa di-*host* sendiri karena `fonts.gstatic.com` diblokir di jaringan sandbox pengembangan ini).

## Catatan penting: prioritas target penilaian
Target kelulusan yang ditampilkan alat audit memberi bobot **Lighthouse Performance = 0** (tidak dihitung),
sementara Accessibility, Best Practices, SEO, dan Axe Core Accessibility masing-masing 25. Optimasi performa di
atas tetap dikerjakan penuh (karena diminta), tapi **tidak sampai mengorbankan keterbacaan kode** — `style.css`
dan `bootstrap-custom.css` sengaja **tidak di-minify** (tetap berkomentar & mudah dibaca) karena ini praktikum
CSS yang kodenya kemungkinan ditinjau langsung oleh dosen/asisten, dan dampak performanya sudah kecil setelah
Bootstrap (kontributor ukuran terbesar) di-*purge*. Yang di-minify hanya kode vendor pihak ketiga (Bootstrap,
Tailwind hasil build).

## Menjalankan

Tidak perlu build untuk sekadar melihat halaman — buka langsung di browser atau pakai *Live Server*. Build
tooling (Tailwind CLI, PurgeCSS, fonttools) hanya dipakai sekali di sisi developer untuk menghasilkan file yang
sudah ada di `assets/`; tidak ada langkah build yang perlu dijalankan ulang oleh pengguna/dosen.

Deploy ulang ke Netlify: unggah ulang folder ini (drag-and-drop atau CLI). `netlify.toml` akan otomatis dibaca
untuk redirect dan header — tidak perlu pengaturan tambahan di dashboard.

## Struktur folder

```
11s24032-pabwe-p2/
├── index.html                # Landing page (CSS)
├── blog.html                 # Daftar blog (Bootstrap 5, self-hosted)
├── blog-detail.html          # Detail blog (Bootstrap 5, self-hosted)
├── cv.html                   # CV (Tailwind 4, dikompilasi)
├── netlify.toml               # Redirect /index dkk (200, bukan 301) + header cache/keamanan
├── _redirects                 # Jaring pengaman redirect tambahan
├── robots.txt / sitemap.xml   # SEO
├── assets/
│   ├── css/
│   │   ├── style.css              # CSS landing page (murni, dikomentari)
│   │   ├── bootstrap-custom.css   # Sumber penyesuaian tema Bootstrap (dikomentari)
│   │   ├── bundle.css             # bootstrap+icons+custom digabung (dipakai blog/blog-detail)
│   │   ├── bootstrap-icons.woff2  # Font ikon, sudah di-subset (2.4 KB)
│   │   └── tailwind.min.css       # Hasil build Tailwind 4 (dipakai cv.html)
│   └── img/
│       ├── logo.svg, hero.svg, avatar.svg
│       └── cover-*.svg            # Cover artikel blog
└── README.md
```

## Catatan teknis lain

- Identitas visual konsisten di semua halaman: logo/nama brand yang sama, palet cyan–violet di atas latar gelap,
  font Space Grotesk (heading) dan Inter (teks).
- Semantic HTML5 (`header`, `nav`, `main`, `section`, `article`, `aside`, `footer`), skip link, `aria-*` pada
  navigasi, dan fokus keyboard yang terlihat.
- Responsive: landing page memakai Flexbox, CSS Grid, dan `@media`; blog memakai grid Bootstrap (`row`/`col-*`);
  CV memakai prefix `md:` Tailwind.
- Seluruh gambar berupa SVG lokal sehingga tidak bergantung pada URL eksternal.
- Form kontak (landing page) dan komentar (detail blog) hanya simulasi di sisi klien — belum terhubung ke backend.
  Input pengguna ditampilkan dengan `textContent` (bukan `innerHTML`) untuk mencegah XSS — sudah diuji otomatis.
- Konten perusahaan, statistik, dan artikel bersifat fiktif untuk keperluan praktikum.
