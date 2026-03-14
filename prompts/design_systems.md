# Design Systems v2 — Unified Visual Identity for Vibe Coding

> **Tujuan:** Tempelkan seluruh dokumen ini ke prompt AI (Claude, GPT, dll.) bersama konteks project baru. AI akan menghasilkan website dengan identitas visual yang konsisten, profesional, dan **fully offline**.
>
> **Versi:** 2.0 — Unified dari Interface 1, 2, 3 & DESIGN_SYSTEM.md
>
> **Prinsip utama:** Rounded-everything · Purple accent · Soft depth · Spring motion · Dark mode native

---

## 0. INSTRUKSI UNTUK AI

### Cara Menggunakan Dokumen Ini

```
Kamu adalah frontend developer expert. Bangun website [NAMA PROJECT] menggunakan
Design Systems v2 di bawah ini sebagai satu-satunya referensi visual.

ATURAN KETAT:
1. BACA seluruh dokumen ini sebelum menulis kode apapun.
2. GUNAKAN token warna dari Tailwind config — JANGAN hardcode hex kecuali untuk hover shade.
3. PASTIKAN setiap komponen memiliki pasangan dark: modifier.
4. VERIFIKASI konsistensi dengan Checklist di Section 15 sebelum output final.
5. Website HARUS berfungsi sepenuhnya OFFLINE (tanpa CDN eksternal).
```

### Chain-of-Thought: Alur Berpikir Saat Membangun

```
Langkah 1 → Pahami kebutuhan project (fungsi, target user, jumlah halaman)
Langkah 2 → Tentukan layout: apakah perlu sidebar? bottom nav? single page?
Langkah 3 → Siapkan HTML boilerplate dengan font & Tailwind OFFLINE (lihat Section 1)
Langkah 4 → Bangun struktur: Header → Main Content → Footer/Nav
Langkah 5 → Terapkan komponen dari Section 7–12 (Card, Button, Modal, dll.)
Langkah 6 → Tambahkan interaktivitas: dark mode toggle, toast, animasi
Langkah 7 → Pastikan responsive (mobile-first, breakpoint md:768px)
Langkah 8 → Jalankan Checklist Konsistensi (Section 15)
Langkah 9 → Verifikasi: baca ulang kode, cocokkan dengan design system
```

### Chain-of-Verification: Self-Check Sebelum Output

```
Setelah selesai menulis kode, jawab pertanyaan berikut secara internal:

✓ Apakah SEMUA tombol menggunakan rounded-full?
✓ Apakah SEMUA card menggunakan rounded-3xl?
✓ Apakah SEMUA input/select menggunakan rounded-xl?
✓ Apakah setiap elemen memiliki pasangan dark: modifier?
✓ Apakah hover shadow menggunakan warna semantik (shadow-primary/30)?
✓ Apakah toast dibuat via JS dengan .textContent (bukan .innerHTML)?
✓ Apakah tidak ada onclick inline — semua pakai addEventListener?
✓ Apakah font Inter di-embed offline (bukan Google Fonts CDN)?
✓ Apakah Tailwind dimuat secara offline?
✓ Apakah dark mode toggle menyimpan preferensi ke localStorage?

Jika ada yang TIDAK, perbaiki sebelum memberikan output.
```

---

## 1. TECH STACK & SETUP (OFFLINE-FIRST)

### Stack

| Layer       | Teknologi                          | Catatan                                  |
|-------------|------------------------------------|------------------------------------------|
| Markup      | HTML5 semantic                     | Tanpa framework JS                       |
| Styling     | Tailwind CSS (standalone/offline)  | Lihat opsi di bawah                      |
| Script      | Vanilla JavaScript (ES6+)          | Modular, event-driven                    |
| Font        | Inter (self-hosted / embedded)     | Weight: 400, 500, 600, 700              |
| Icons       | Inline SVG — Heroicons outline     | Tanpa library eksternal                  |
| Charts      | Chart.js 4.x (opsional, bundled)   | Hanya jika project butuh grafik          |

### Opsi Offline untuk Tailwind CSS

**Opsi A — Tailwind CLI (Rekomendasi Production)**
```bash
# Install
npm install -D tailwindcss
npx tailwindcss init

# Build
npx tailwindcss -i ./src/input.css -o ./dist/output.css --watch
```

**Opsi B — Pre-built Tailwind Standalone Binary**
```bash
# Download binary dari GitHub releases tailwindcss
# Jalankan tanpa Node.js:
./tailwindcss -i input.css -o output.css --minify
```

**Opsi C — Development Only (CDN, TIDAK untuk production/offline)**
```html
<!-- HANYA untuk prototyping cepat, BUKAN offline -->
<script src="https://cdn.tailwindcss.com"></script>
```

> **PENTING:** Untuk project offline, gunakan Opsi A atau B. Opsi C memerlukan internet.

### Font Inter — Self-Hosted (Offline)

Download file font dari [Google Fonts](https://fonts.google.com/specimen/Inter) atau [fontsource](https://fontsource.org/fonts/inter), lalu embed:

```css
/* fonts/inter.css — letakkan di folder project */
@font-face {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 400;
  font-display: swap;
  src: url('./fonts/Inter-Regular.woff2') format('woff2');
}
@font-face {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 500;
  font-display: swap;
  src: url('./fonts/Inter-Medium.woff2') format('woff2');
}
@font-face {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 600;
  font-display: swap;
  src: url('./fonts/Inter-SemiBold.woff2') format('woff2');
}
@font-face {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-display: swap;
  src: url('./fonts/Inter-Bold.woff2') format('woff2');
}
```

### Struktur Folder Project

```
project/
├── index.html
├── css/
│   ├── output.css          ← Tailwind compiled output
│   └── custom.css          ← Animasi, scrollbar, slider
├── fonts/
│   ├── Inter-Regular.woff2
│   ├── Inter-Medium.woff2
│   ├── Inter-SemiBold.woff2
│   └── Inter-Bold.woff2
├── js/
│   ├── app.js              ← Logika utama
│   └── utils.js            ← Toast, dark mode, helpers
├── lib/                    ← (opsional)
│   └── chart.min.js        ← Chart.js bundled offline
└── tailwind.config.js
```

### HTML Boilerplate

```html
<!DOCTYPE html>
<html lang="id" class="scroll-smooth">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[NAMA PROJECT]</title>

  <!-- Dark mode flash prevention — HARUS di <head> SEBELUM CSS -->
  <script>
    if (localStorage.getItem("darkMode") === "true") {
      document.documentElement.classList.add("dark");
    }
  </script>

  <!-- Font Inter (self-hosted) -->
  <link rel="stylesheet" href="css/fonts.css">

  <!-- Tailwind compiled CSS -->
  <link rel="stylesheet" href="css/output.css">

  <!-- Custom CSS (animasi, scrollbar) -->
  <link rel="stylesheet" href="css/custom.css">
</head>
<body class="bg-[#f8f9fa] dark:bg-gray-900 font-sans antialiased
             text-text dark:text-white transition-colors">

  <!-- HEADER (Section 8) -->
  <!-- MAIN CONTENT -->
  <!-- TOAST CONTAINER -->
  <div id="toastContainer"
       class="fixed bottom-6 right-6 z-[60] flex flex-col gap-3 pointer-events-none">
  </div>

  <script src="js/app.js"></script>
</body>
</html>
```

---

## 2. TAILWIND CONFIG

```js
// tailwind.config.js
module.exports = {
  darkMode: "class",
  content: ["./**/*.{html,js}"],
  theme: {
    extend: {
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
      },
      colors: {
        primary:    "#6246ea",   // Ungu — aksi utama, CTA, link, logo
        secondary:  "#d1d1e9",   // Ungu muda — border, disabled, divider
        danger:     "#e45858",   // Merah — hapus, error, SL, expense
        success:    "#58e49e",   // Hijau mint — sukses, income, TP
        background: "#fffffe",   // Putih bersih
        text:       "#2b2c34",   // Hitam keunguan — teks utama
      },
      boxShadow: {
        'primary-glow': '0 10px 25px -3px rgba(98, 70, 234, 0.3)',
        'danger-glow':  '0 10px 25px -3px rgba(228, 88, 88, 0.3)',
        'success-glow': '0 10px 25px -3px rgba(88, 228, 158, 0.4)',
      },
    },
  },
};
```

> **Jika menggunakan Tailwind CDN (dev only)**, letakkan config di `<script>`:
> ```html
> <script>
>   tailwind.config = { /* isi sama seperti di atas, tanpa content & module.exports */ };
> </script>
> ```

---

## 3. PALET WARNA

### 3.1 Brand Colors (Token)

| Token        | Hex       | Penggunaan Utama                                    |
|--------------|-----------|-----------------------------------------------------|
| `primary`    | `#6246ea` | CTA, active state, focus ring, logo bg, link, aksen |
| `danger`     | `#e45858` | Hapus, error, expense, SL, overdue                  |
| `success`    | `#58e49e` | Konfirmasi, download, income, TP, import            |
| `secondary`  | `#d1d1e9` | Border sidebar, divider halus, disabled state       |
| `background` | `#fffffe` | Background halaman (light mode override)            |
| `text`       | `#2b2c34` | Teks utama (light mode)                             |

### 3.2 Hover Shades (Hardcoded yang Diizinkan)

| Base Color | Hover Hex   | Tailwind Class       | Perubahan |
|------------|-------------|----------------------|-----------|
| `primary`  | `#5035cc`   | `hover:bg-[#5035cc]` | -15%      |
| `danger`   | `#cc4040`   | `hover:bg-[#cc4040]` | -15%      |
| `success`  | `#3abb7e`   | `hover:bg-[#3abb7e]` | -15%      |

### 3.3 Surface Colors

| Elemen            | Light Mode       | Dark Mode            |
|-------------------|------------------|----------------------|
| Page background   | `bg-[#f8f9fa]`   | `dark:bg-gray-900`   |
| Card / Panel      | `bg-white`       | `dark:bg-gray-800`   |
| Sidebar           | `bg-white`       | `dark:bg-gray-800`   |
| Input background  | `bg-gray-50`     | `dark:bg-gray-700`   |
| Tint surface      | `bg-[#EBEBFA]`   | `dark:bg-gray-700`   |

### 3.4 Border Colors

| Intensitas | Light Mode          | Dark Mode               |
|------------|---------------------|--------------------------|
| Halus      | `border-gray-100`   | `dark:border-gray-700`   |
| Kuat       | `border-gray-200`   | `dark:border-gray-600`   |

### 3.5 Text Colors (Dark Mode Pairs)

| Peran           | Light                  | Dark                      |
|-----------------|------------------------|---------------------------|
| Teks utama      | `text-text` (#2b2c34)  | `dark:text-white`         |
| Teks sekunder   | `text-gray-500`        | `dark:text-gray-400`      |
| Teks label      | `text-text`            | `dark:text-gray-300`      |
| Teks hint/sub   | `text-gray-400`        | `dark:text-gray-500`      |

### 3.6 Semantic / Data Colors

| Konteks                          | Light Mode  | Dark Mode   |
|----------------------------------|-------------|-------------|
| Nilai positif (profit, income)   | `#058a4e`   | `#58e49e`   |
| Nilai negatif (loss, expense)    | `#e45858`   | `#f87171`   |
| Warning / netral                 | `#d97706`   | `#f59e0b`   |

### 3.7 Kontras Teks di Atas Warna

| Background   | Teks yang BENAR       | ⚠️ Catatan                       |
|--------------|-----------------------|----------------------------------|
| `primary`    | `text-white`          | Aman — warna gelap               |
| `danger`     | `text-white`          | Aman — warna gelap               |
| `success`    | `text-gray-800`       | TERANG — jangan pakai text-white |
| `yellow-500` | `text-gray-800`       | TERANG — jangan pakai text-white |

### 3.8 Chart Color Palette (8 warna berurutan)

```js
const CHART_COLORS = [
  '#6246ea', '#58e49e', '#e45858', '#3b82f6',
  '#f97316', '#ec4899', '#8b5cf6', '#10b981'
];
```

### 3.9 Heatmap Colors (opsional, untuk activity grid)

| Level    | Light       | Dark        |
|----------|-------------|-------------|
| `heat-0` | `#f3f4f6`   | `#374151`   |
| `heat-1` | `#ddd6fe`   | `#4c1d95`   |
| `heat-2` | `#a78bfa`   | `#7c3aed`   |
| `heat-3` | `#7c3aed`   | `#a78bfa`   |
| `heat-4` | `#4c1d95`   | `#ddd6fe`   |

---

## 4. TIPOGRAFI

### Font: Inter — `font-sans antialiased`

| Elemen                    | Tailwind Class                                      |
|---------------------------|-----------------------------------------------------|
| Judul halaman (H1)       | `text-xl font-bold tracking-tight`                  |
| Judul seksi (H2)         | `text-lg font-semibold` atau `text-xl font-bold tracking-tight` |
| Section header (compact) | `text-sm font-semibold`                             |
| Label form               | `text-sm font-medium text-text dark:text-gray-300`  |
| Body / paragraf           | `text-base leading-relaxed` atau `text-sm`          |
| Teks sekunder / hint      | `text-sm text-gray-500 dark:text-gray-400`          |
| Sub-label                 | `text-xs text-gray-400 dark:text-gray-500`          |
| Badge / label kecil       | `text-xs font-medium`                               |
| Label uppercase           | `text-xs font-medium uppercase tracking-wide`       |
| KPI / angka besar         | `text-2xl font-bold` atau `text-3xl font-bold`      |
| Tombol                    | `font-medium` (normal) / `font-bold` (aksi kritis)  |
| Kode / kbd                | `text-sm font-semibold font-mono`                   |
| Brand name                | `text-xl font-bold tracking-tight`                  |
| Nav label mobile          | `text-[10px] font-medium`                           |

### Letter Spacing

| Konteks            | Class             |
|--------------------|-------------------|
| Heading / judul    | `tracking-tight`  |
| Tombol / badge     | `tracking-wide`   |
| Body               | default (none)    |

---

## 5. BORDER RADIUS — Hierarki Ketat

| Level | Class           | ~px  | Digunakan Pada                                        |
|-------|-----------------|------|-------------------------------------------------------|
| 1     | `rounded-full`  | 9999 | **SEMUA tombol**, badge, pill, toggle, progress bar   |
| 2     | `rounded-3xl`   | 24px | Card utama, modal, hero section, empty state          |
| 3     | `rounded-2xl`   | 16px | Panel sekunder, toast, grup radio/checkbox, container |
| 4     | `rounded-xl`    | 12px | Input, select, icon container, nav item, logo         |
| 5     | `rounded-lg`    | 8px  | Image/video container, thumbnail                      |
| 6     | `rounded-md`    | 6px  | Scrollbar thumb                                       |

> **ATURAN UTAMA:** Tombol = `rounded-full` tanpa pengecualian. Tidak pernah kurang dari full.

---

## 6. SHADOW SYSTEM

### Shadow per Konteks

| Elemen                  | Default       | Hover                           |
|-------------------------|---------------|---------------------------------|
| Card                    | `shadow-sm`   | `hover:shadow-lg`               |
| Modal                   | `shadow-2xl`  | —                               |
| Toast                   | `shadow-xl`   | —                               |
| Tombol primary          | —             | `shadow-lg shadow-primary/30`   |
| Tombol danger           | —             | `shadow-lg shadow-danger/30`    |
| Tombol success          | —             | `shadow-lg shadow-success/40`   |
| Hero / gradient card    | `shadow-lg shadow-primary/20` | —                  |
| FAB (mobile)            | `shadow-lg shadow-primary/40` | —                  |
| Logo icon header        | `shadow-lg shadow-primary/30` | —                  |
| Progress bar background | `shadow-inner`| —                               |
| Icon button             | `shadow-sm`   | —                               |

### Custom Glow Shadows (di tailwind.config)

```
primary-glow : 0 10px 25px -3px rgba(98, 70, 234, 0.3)
danger-glow  : 0 10px 25px -3px rgba(228, 88, 88, 0.3)
success-glow : 0 10px 25px -3px rgba(88, 228, 158, 0.4)
```

---

## 7. KOMPONEN — TOMBOL (BUTTON)

> Semua tombol: `rounded-full` + `transition-all duration-200` + `active:scale-95` atau `active:scale-[0.98]` + `flex items-center gap-2`

### 7.1 Primary (CTA Utama)

```html
<button class="py-3 px-6 bg-primary text-white font-medium rounded-full
               hover:bg-[#5035cc] hover:shadow-lg hover:shadow-primary/30
               transition-all duration-200 flex items-center gap-2
               active:scale-95">
  <!-- SVG icon w-5 h-5 (opsional) -->
  Label Tombol
</button>
```

### 7.2 Danger (Hapus / Destruktif)

```html
<button class="py-3 px-6 bg-danger text-white font-medium rounded-full
               hover:bg-[#cc4040] hover:shadow-lg hover:shadow-danger/30
               transition-all duration-200 flex items-center gap-2
               active:scale-[0.98]">
  Label Hapus
</button>
```

### 7.3 Success (Download / Konfirmasi Positif)

```html
<button class="py-3 px-6 bg-success text-gray-800 font-medium rounded-full
               hover:bg-[#3abb7e] hover:shadow-lg hover:shadow-success/40
               transition-all duration-200 flex items-center gap-2
               active:scale-[0.98]
               disabled:opacity-50 disabled:cursor-not-allowed">
  Label Sukses
</button>
```

### 7.4 Ghost / Netral

```html
<button class="py-3 px-6 bg-gray-100 dark:bg-gray-700
               text-gray-700 dark:text-white font-medium rounded-full
               hover:bg-gray-200 dark:hover:bg-gray-600
               transition-colors active:scale-[0.98]">
  Batal
</button>
```

### 7.5 Tint / Ghost Berwarna

```html
<button class="py-2.5 px-5 bg-[#EBEBFA] dark:bg-gray-700
               text-primary dark:text-secondary
               border border-secondary dark:border-gray-600
               font-medium rounded-full
               hover:bg-secondary/60 active:scale-[0.98]
               transition-all duration-200">
  Label Tint
</button>
```

### 7.6 Icon Only (Bulat, Tanpa Teks)

```html
<button class="p-2.5 rounded-full bg-gray-100 dark:bg-gray-700
               hover:bg-gray-200 dark:hover:bg-gray-600
               transition-all shadow-sm">
  <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"
       stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
    <!-- path -->
  </svg>
</button>
```

### 7.7 Ukuran Tombol

| Ukuran | Padding        | Konteks           |
|--------|----------------|-------------------|
| Besar  | `py-3 px-6`    | CTA utama, modal  |
| Kecil  | `py-2 px-4`    | Toolbar, inline   |
| Icon   | `p-2.5`        | Icon-only button  |
| Compact| `py-2.5 px-5`  | Sidebar, filter   |

### Aturan Tombol (Rangkuman)

- Selalu `rounded-full` — **tanpa pengecualian**
- Selalu ada `transition-all duration-200` atau `transition-colors`
- Selalu ada `active:scale-95` (besar) atau `active:scale-[0.98]` (kecil)
- Hover shadow **warna harus sesuai** variant (primary/danger/success)
- Disabled: `disabled:opacity-50 disabled:cursor-not-allowed`
- Gap ikon ke teks: `gap-2`

---

## 8. KOMPONEN — HEADER

```html
<header class="sticky top-0 z-50 bg-white dark:bg-gray-800
               shadow-sm border-b border-gray-100 dark:border-gray-700">
  <div class="max-w-7xl mx-auto px-4 md:px-6 py-3
              flex items-center justify-between relative">

    <!-- Logo + Judul (tengah, absolute) -->
    <div class="absolute left-1/2 -translate-x-1/2 flex items-center gap-2.5">
      <div class="w-10 h-10 bg-primary rounded-xl flex items-center justify-center
                  shadow-lg shadow-primary/30">
        <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor"
             viewBox="0 0 24 24" stroke-width="2"
             stroke-linecap="round" stroke-linejoin="round">
          <!-- Icon path project -->
        </svg>
      </div>
      <h1 class="text-xl font-bold text-text dark:text-white tracking-tight">
        Nama Project
      </h1>
    </div>

    <!-- Spacer kiri -->
    <div></div>

    <!-- Kontrol kanan -->
    <div class="flex items-center gap-3">
      <!-- Dark mode toggle (Section 13) -->
      <!-- Tombol lain sesuai kebutuhan -->
    </div>

  </div>
</header>
```

---

## 9. KOMPONEN — CARD

### 9.1 Card Standard

```html
<div class="bg-white dark:bg-gray-800
            border border-gray-100 dark:border-gray-700
            rounded-3xl p-5 shadow-sm
            hover:shadow-lg transition-shadow duration-200">
  <!-- konten -->
</div>
```

### 9.2 Card Hero / Gradient

```html
<div class="bg-gradient-to-br from-primary to-[#5035cc] text-white
            rounded-3xl p-6 shadow-lg shadow-primary/20">
  <!-- konten hero -->
</div>
```

### 9.3 Card Danger Zone

```html
<div class="bg-danger/10 dark:bg-danger/20
            border border-danger/20 rounded-3xl p-5">
  <!-- konten peringatan -->
</div>
```

### 9.4 Card Active / Selected State

```css
.card-active {
  border-color: #6246ea !important;
  box-shadow: 0 0 0 2px rgba(98, 70, 234, 0.3) !important;
}
```

---

## 10. KOMPONEN — FORM ELEMENTS

### 10.1 Input Text / Number

```html
<input type="text"
  class="w-full px-4 py-2.5
         border border-gray-200 dark:border-gray-600 rounded-xl
         bg-gray-50 dark:bg-gray-700
         text-text dark:text-white text-sm
         focus:outline-none focus:ring-2 focus:ring-primary/20 focus:border-primary
         transition-all placeholder:text-gray-400" />
```

### 10.2 Select / Dropdown

```html
<select class="w-full px-4 py-2.5
               border border-gray-200 dark:border-gray-600 rounded-xl
               bg-gray-50 dark:bg-gray-700
               text-text dark:text-white text-sm
               focus:outline-none focus:ring-2 focus:ring-primary/20 focus:border-primary
               transition-all">
  <option>Pilihan</option>
</select>
```

### 10.3 Label

```html
<label class="block text-sm font-medium text-text dark:text-gray-300 mb-2">
  Nama Field
</label>
```

### 10.4 Container Radio / Checkbox

```html
<div class="bg-gray-50 dark:bg-gray-700/50 p-4 rounded-2xl
            border border-gray-100 dark:border-gray-700 mb-5">
  <!-- radio/checkbox items -->
</div>
```

### 10.5 Range Slider

```html
<input type="range"
  class="w-full h-2 rounded-lg appearance-none cursor-pointer
         bg-gray-200 dark:bg-gray-700" />
```

CSS wajib untuk thumb:
```css
input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 18px;
  height: 18px;
  border-radius: 50%;
  background: #6246ea;
  cursor: pointer;
  transition: background 0.15s;
}
input[type="range"]::-webkit-slider-thumb:hover {
  background: #4d37b8;
}
```

---

## 11. KOMPONEN — MODAL

### 11.1 Struktur Modal

```html
<!-- Overlay -->
<div class="modal-overlay fixed inset-0 bg-gray-900/60 backdrop-blur-sm z-50
            flex items-center justify-center p-4">

  <!-- Content Card -->
  <div class="modal-content bg-white dark:bg-gray-800 rounded-3xl p-8
              w-full max-w-sm mx-4 shadow-2xl">

    <!-- Ikon warning (opsional, untuk confirm) -->
    <div class="w-16 h-16 bg-danger/20 dark:bg-danger/30 text-danger
                rounded-full flex items-center justify-center mb-6 mx-auto">
      <svg class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24"
           stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
        <!-- icon path -->
      </svg>
    </div>

    <h3 class="text-xl font-bold text-text dark:text-white mb-3
               text-center tracking-tight">
      Judul Modal
    </h3>
    <p class="text-gray-500 dark:text-gray-400 mb-8 text-center leading-relaxed">
      Deskripsi modal.
    </p>

    <div class="flex flex-col gap-3">
      <button class="w-full py-3 px-4 bg-danger text-white rounded-full
                     font-medium hover:bg-[#cc4040] transition-colors
                     shadow-sm active:scale-[0.98]">
        Ya, Hapus
      </button>
      <button class="w-full py-3 px-4 bg-gray-100 dark:bg-gray-700
                     text-gray-700 dark:text-white rounded-full font-medium
                     hover:bg-gray-200 dark:hover:bg-gray-600 transition-colors
                     active:scale-[0.98]"
              onclick="closeModal()">
        Batal
      </button>
    </div>

  </div>
</div>
```

### 11.2 Modal Besar (Form)

Ganti `max-w-sm` dengan `max-w-2xl` dan tambahkan `max-h-[92vh] overflow-y-auto`.

### 11.3 Animasi Modal (CSS)

```css
.modal-overlay {
  animation: overlayFadeIn 0.18s ease-out;
}
.modal-content {
  animation: modalPopIn 0.22s cubic-bezier(0.34, 1.3, 0.64, 1);
}

@keyframes overlayFadeIn {
  from { opacity: 0; }
  to   { opacity: 1; }
}
@keyframes modalPopIn {
  from { opacity: 0; transform: scale(0.92) translateY(8px); }
  to   { opacity: 1; transform: scale(1) translateY(0); }
}
```

---

## 12. KOMPONEN — TOAST / NOTIFIKASI

### 12.1 Container

```html
<div id="toastContainer"
     class="fixed bottom-6 right-6 z-[60] flex flex-col gap-3 pointer-events-none">
</div>
```

### 12.2 JavaScript (XSS-Safe)

```javascript
function showToast(message, type = "info") {
  const config = {
    success: { bg: "bg-success",     text: "text-gray-800", border: "border-black/10",  btnHover: "hover:bg-black/10"  },
    error:   { bg: "bg-danger",      text: "text-white",    border: "border-white/10",  btnHover: "hover:bg-white/20"  },
    warning: { bg: "bg-yellow-500",  text: "text-gray-800", border: "border-black/10",  btnHover: "hover:bg-black/10"  },
    info:    { bg: "bg-primary",     text: "text-white",    border: "border-white/10",  btnHover: "hover:bg-white/20"  },
  };

  const c = config[type] || config.info;

  const toast = document.createElement("div");
  toast.className = `toast px-5 py-3.5 rounded-2xl ${c.text} ${c.bg}
    shadow-xl flex items-center gap-3 font-medium tracking-wide
    border ${c.border} backdrop-blur-md pointer-events-auto`;

  const msg = document.createElement("span");
  msg.className = "flex-1";
  msg.textContent = message; // ← WAJIB textContent, BUKAN innerHTML

  const btn = document.createElement("button");
  btn.className = `p-1 ${c.btnHover} rounded-full transition-colors`;
  btn.innerHTML = `<svg class="w-5 h-5" fill="none" stroke="currentColor"
    viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round"
    stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>`;
  btn.addEventListener("click", () => toast.remove());

  toast.appendChild(msg);
  toast.appendChild(btn);
  document.getElementById("toastContainer").appendChild(toast);

  // Auto-dismiss setelah 3.5 detik
  setTimeout(() => {
    toast.style.animation = "slideOut 0.3s ease-out forwards";
    setTimeout(() => toast.remove(), 300);
  }, 3500);
}
```

### 12.3 Toast Colors

| Type      | Background      | Text             | Border             |
|-----------|-----------------|------------------|--------------------|
| `success` | `bg-success`    | `text-gray-800`  | `border-black/10`  |
| `error`   | `bg-danger`     | `text-white`     | `border-white/10`  |
| `warning` | `bg-yellow-500` | `text-gray-800`  | `border-black/10`  |
| `info`    | `bg-primary`    | `text-white`     | `border-white/10`  |

### 12.4 Animasi Toast (CSS)

```css
.toast { animation: slideIn 0.3s ease-out; }

@keyframes slideIn {
  from { transform: translateX(100%); opacity: 0; }
  to   { transform: translateX(0);    opacity: 1; }
}
@keyframes slideOut {
  from { transform: translateX(0);    opacity: 1; }
  to   { transform: translateX(100%); opacity: 0; }
}
```

---

## 13. DARK MODE

### 13.1 Implementasi

- Method: class-based (`darkMode: "class"`)
- Toggle: tambah/hapus class `dark` pada `<html>`
- Persisten: simpan ke `localStorage`

### 13.2 Flash Prevention (di `<head>`)

```html
<script>
  if (localStorage.getItem("darkMode") === "true") {
    document.documentElement.classList.add("dark");
  }
</script>
```

### 13.3 Toggle Function

```javascript
function toggleDarkMode() {
  const isDark = document.documentElement.classList.toggle("dark");
  localStorage.setItem("darkMode", isDark);
  // Update ikon tombol sesuai state
}
```

### 13.4 Pasangan Warna Lengkap (Quick Reference)

| Elemen              | Light                    | Dark                       |
|---------------------|--------------------------|----------------------------|
| Page bg             | `bg-[#f8f9fa]`           | `dark:bg-gray-900`         |
| Card / Panel        | `bg-white`               | `dark:bg-gray-800`         |
| Header / Sidebar    | `bg-white`               | `dark:bg-gray-800`         |
| Input bg            | `bg-gray-50`             | `dark:bg-gray-700`         |
| Border halus        | `border-gray-100`        | `dark:border-gray-700`     |
| Border kuat         | `border-gray-200`        | `dark:border-gray-600`     |
| Teks utama          | `text-text`              | `dark:text-white`          |
| Teks sekunder       | `text-gray-500`          | `dark:text-gray-400`       |
| Teks label          | `text-text`              | `dark:text-gray-300`       |
| Hover bg ringan     | `hover:bg-gray-100`      | `dark:hover:bg-gray-700`   |
| Hover bg kuat       | `hover:bg-gray-200`      | `dark:hover:bg-gray-600`   |
| Tint surface        | `bg-[#EBEBFA]`           | `dark:bg-gray-700`         |

---

## 14. KOMPONEN — NAVIGASI

### 14.1 Sidebar (Desktop ≥ 768px)

```html
<aside class="hidden md:block w-64 sticky top-16
              bg-white dark:bg-gray-800
              border-r border-secondary dark:border-gray-700
              h-[calc(100vh-4rem)] overflow-y-auto">
  <nav class="p-4 space-y-1">
    <button class="nav-item w-full text-left px-4 py-2.5 rounded-xl
                   text-sm font-medium flex items-center gap-3
                   hover:bg-gray-50 dark:hover:bg-gray-700 transition-colors">
      <svg class="w-5 h-5 text-gray-400"><!-- icon --></svg>
      Label Menu
    </button>
    <!-- nav-item-active untuk item aktif -->
  </nav>
</aside>
```

Active state CSS:
```css
.nav-item-active {
  background: rgba(98, 70, 234, 0.08);
  color: #6246ea;
  border-left: 3px solid #6246ea;
}
.dark .nav-item-active {
  background: rgba(98, 70, 234, 0.15);
  color: #9b82f5;
  border-left-color: #9b82f5;
}
```

### 14.2 Bottom Navigation (Mobile < 768px)

```html
<nav class="md:hidden fixed bottom-0 left-0 right-0 z-40
            bg-white dark:bg-gray-800
            border-t border-gray-100 dark:border-gray-700
            flex items-center justify-around px-2 py-2">

  <!-- Nav item biasa -->
  <button class="flex flex-col items-center gap-0.5 py-1 px-3
                 text-gray-400 hover:text-primary transition-colors">
    <svg class="w-5 h-5"><!-- icon --></svg>
    <span class="text-[10px] font-medium">Home</span>
  </button>

  <!-- FAB Center -->
  <button class="w-14 h-14 bg-primary rounded-full -mt-5
                 flex items-center justify-center
                 shadow-lg shadow-primary/40 active:scale-95 transition-all">
    <svg class="w-7 h-7 text-white"><!-- plus icon --></svg>
  </button>

  <!-- Nav items lain... -->
</nav>
```

> Mobile: tambahkan `pb-24` pada konten utama untuk clearance bottom nav.

---

## 15. KOMPONEN TAMBAHAN

### 15.1 Empty State

```html
<div class="bg-white dark:bg-gray-800
            border border-gray-100 dark:border-gray-700
            rounded-3xl shadow-sm py-20 px-6 text-center">
  <div class="w-24 h-24 bg-[#EBEBFA] dark:bg-gray-700 rounded-full
              flex items-center justify-center mx-auto mb-6">
    <svg class="w-12 h-12 text-primary" fill="none" stroke="currentColor"
         viewBox="0 0 24 24" stroke-width="2"
         stroke-linecap="round" stroke-linejoin="round">
      <!-- icon -->
    </svg>
  </div>
  <h3 class="text-2xl font-bold text-text dark:text-white tracking-tight mb-3">
    Belum Ada Data
  </h3>
  <p class="text-gray-500 dark:text-gray-400 max-w-md mx-auto leading-relaxed mb-8">
    Klik tombol di atas untuk mulai menambahkan data.
  </p>
  <!-- CTA button opsional -->
</div>
```

### 15.2 Badge / Pill

```html
<!-- Info badge (stats bar) -->
<div class="flex items-center gap-3 px-5 h-12
            bg-white dark:bg-gray-800 rounded-full shadow-sm
            border border-gray-100 dark:border-gray-700
            text-sm font-medium text-gray-600 dark:text-gray-300">
  <span>Total: <span class="font-bold text-primary">42</span></span>
</div>

<!-- Counter label -->
<span class="text-sm font-semibold text-gray-600 dark:text-gray-300
             bg-gray-100 dark:bg-gray-700 px-3 py-1 rounded-full">
  20 items
</span>
```

### 15.3 Badge Status (Semantic)

```css
.badge {
  display: inline-flex;
  align-items: center;
  padding: 2px 10px;
  border-radius: 9999px;
  font-size: 0.7rem;
  font-weight: 600;
  white-space: nowrap;
}

/* Light */
.badge-open   { background: #dbeafe; color: #1d4ed8; }
.badge-closed { background: #f3f4f6; color: #6b7280; }
.badge-win    { background: #d1fae5; color: #065f46; }
.badge-lose   { background: #fee2e2; color: #991b1b; }

/* Dark */
.dark .badge-open   { background: #1e3a5f; color: #93c5fd; }
.dark .badge-closed { background: #374151; color: #9ca3af; }
.dark .badge-win    { background: #064e3b; color: #6ee7b7; }
.dark .badge-lose   { background: #7f1d1d; color: #fca5a5; }
```

### 15.4 Progress Bar

```html
<div class="w-full bg-gray-100 dark:bg-gray-700 rounded-full h-3
            shadow-inner overflow-hidden">
  <div class="bg-primary h-full rounded-full transition-all duration-300"
       style="width: 65%"></div>
</div>
```

### 15.5 Icon Container

```html
<!-- Kotak kecil (kategori icon) -->
<div class="w-8 h-8 bg-[#EBEBFA] dark:bg-gray-700 rounded-xl
            flex items-center justify-center">
  <svg class="w-4 h-4 text-primary"><!-- icon --></svg>
</div>

<!-- Bulat besar (modal, empty state) -->
<div class="w-16 h-16 bg-danger/20 dark:bg-danger/30 rounded-full
            flex items-center justify-center mx-auto">
  <svg class="w-8 h-8 text-danger"><!-- icon --></svg>
</div>

<!-- Kategori icon dengan warna dinamis (JS) -->
<script>
  // color = hex warna kategori
  iconEl.style.background = color + '22'; // ~13% opacity
  iconEl.className = 'w-12 h-12 rounded-2xl flex items-center justify-center text-2xl';
</script>
```

### 15.6 Data Table

```css
/* Tabel data */
table {
  border-collapse: separate;
  border-spacing: 0;
  width: 100%;
}
thead th {
  position: sticky;
  top: 0;
  z-index: 5;
  background: #f9fafb;
  padding: 12px;
  font-size: 0.75rem;
  font-weight: 600;
  letter-spacing: 0.05em;
  color: #6b7280;
  text-align: left;
}
.dark thead th {
  background: #1f2937;
  color: #9ca3af;
}
tbody tr {
  border-top: 1px solid #f9fafb;
  transition: background 0.15s;
}
tbody tr:hover {
  background: #f5f3ff;
}
.dark tbody tr {
  border-color: rgba(55, 65, 81, 0.6);
}
.dark tbody tr:hover {
  background: rgba(55, 65, 81, 0.25);
}
td {
  padding: 12px;
  font-size: 0.75rem;
}
```

---

## 16. ANIMASI & TRANSISI

### 16.1 Durasi & Easing

| Elemen           | Durasi  | Easing                                     |
|------------------|---------|--------------------------------------------|
| Hover umum       | 200ms   | `transition-all duration-200`              |
| Hover warna saja | 150ms   | `transition-colors`                        |
| Hover shadow     | 200ms   | `transition-shadow duration-200`           |
| Klik tombol      | instant | `active:scale-95` / `active:scale-[0.98]`  |
| Modal overlay    | 180ms   | `ease-out`                                 |
| Modal content    | 220ms   | `cubic-bezier(0.34, 1.3, 0.64, 1)` spring |
| Toast masuk      | 300ms   | `ease-out`                                 |
| Toast keluar     | 300ms   | `ease-out forwards`                        |
| Sidebar slide    | 300ms   | `ease`                                     |
| View fade-in     | 200ms   | `ease-out`                                 |
| Spinner          | 1000ms  | `linear infinite`                          |
| Count-up angka   | 800ms   | `ease-out`                                 |
| Chevron rotate   | 200ms   | `transition-transform duration-200`        |

### 16.2 CSS Keyframes Lengkap

```css
/* View / page transition */
@keyframes viewFadeIn {
  from { opacity: 0; transform: translateY(6px); }
  to   { opacity: 1; transform: translateY(0); }
}
.view-enter { animation: viewFadeIn 0.2s ease-out; }

/* Modal overlay */
@keyframes overlayFadeIn {
  from { opacity: 0; }
  to   { opacity: 1; }
}

/* Modal content — spring bounce */
@keyframes modalPopIn {
  from { opacity: 0; transform: scale(0.92) translateY(8px); }
  to   { opacity: 1; transform: scale(1) translateY(0); }
}

/* Toast slide in/out */
@keyframes slideIn {
  from { transform: translateX(100%); opacity: 0; }
  to   { transform: translateX(0);    opacity: 1; }
}
@keyframes slideOut {
  from { transform: translateX(0);    opacity: 1; }
  to   { transform: translateX(100%); opacity: 0; }
}

/* Pulse ring (active/recording state) */
@keyframes ringPulse {
  0%, 100% { box-shadow: 0 0 0 0    rgba(98, 70, 234, 0.7); }
  50%      { box-shadow: 0 0 0 10px rgba(98, 70, 234, 0);   }
}

/* Goal/achievement completion */
@keyframes goalPulse {
  0%   { transform: scale(1); }
  50%  { transform: scale(1.05); box-shadow: 0 0 20px rgba(98, 70, 234, 0.5); }
  100% { transform: scale(1); }
}

/* Count-up number transition */
.count-up { transition: all 0.8s ease-out; }
```

---

## 17. ICONS

### Style: Heroicons v2 — Outline Only

Atribut wajib pada SETIAP inline SVG:
```html
<svg class="w-5 h-5"
     fill="none"
     stroke="currentColor"
     viewBox="0 0 24 24"
     stroke-width="2"
     stroke-linecap="round"
     stroke-linejoin="round">
  <path d="..."/>
</svg>
```

### Ukuran per Konteks

| Konteks                    | Class        |
|----------------------------|--------------|
| Di dalam tombol kecil      | `w-4 h-4`   |
| Di dalam tombol reguler    | `w-5 h-5`   |
| Judul seksi / sidebar nav  | `w-5 h-5`   |
| Logo header                | `w-6 h-6`   |
| FAB center                 | `w-7 h-7`   |
| Modal icon / chart empty   | `w-8 h-8`   |
| Empty state utama          | `w-12 h-12` |

### Icon Path Reference (Sering Dipakai)

| Fungsi          | SVG Path `d`                                                              |
|-----------------|---------------------------------------------------------------------------|
| Plus / Tambah   | `M12 4v16m8-8H4`                                                         |
| Close / X       | `M6 18L18 6M6 6l12 12`                                                   |
| Chevron Up      | `M5 15l7-7 7 7`                                                          |
| Chevron Down    | `M19 9l-7 7-7-7`                                                         |
| Trending Up     | `M13 7h8m0 0v8m0-8l-8 8-4-4-6 6`                                        |
| Download        | `M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4`       |
| Trash / Hapus   | `M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16` |
| Edit / Pencil   | `M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z` |
| Moon (dark)     | `M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z` |
| Sun (light)     | `M12 3v1m0 16v1m9-9h-1M4 12H3m15.364-6.364l-.707.707M6.343 6.343l-.707.707m12.728 0l-.707-.707M6.343 17.657l-.707-.707M16 12a4 4 0 11-8 0 4 4 0 018 0z` |
| Settings / Gear | `M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065zM15 12a3 3 0 11-6 0 3 3 0 016 0z` |

> **Warna ikon:** Gunakan `text-primary`, `text-danger`, `text-gray-400`, `text-white`, atau `currentColor` via parent.

---

## 18. RESPONSIVE LAYOUT

### Breakpoint: Satu Utama — `md: 768px`

| Mode    | Kondisi    | Navigasi              | Sidebar    |
|---------|------------|-----------------------|------------|
| Mobile  | `< 768px`  | Bottom nav + FAB      | Hidden     |
| Desktop | `≥ 768px`  | Left sidebar `w-64`   | Visible    |

### Grid Patterns

```
Stat cards    : grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4
Chart row     : grid grid-cols-1 lg:grid-cols-2 gap-4
Card list     : grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4
3-col bottom  : grid grid-cols-1 lg:grid-cols-3 gap-4
Form 2-col    : grid grid-cols-1 md:grid-cols-2 gap-4
Preview chips : grid grid-cols-3 md:grid-cols-6 gap-3
```

### Spacing

```
Max content width  : max-w-5xl mx-auto (konten) atau max-w-7xl (full) atau max-w-[1400px]
Sidebar width      : w-64 (256px)
Header height      : ~64px (py-3 atau py-4)
Content padding    : px-4 md:px-6 py-4 md:py-6
Card padding       : p-5 (standard) / p-6 (hero)
Card grid gap      : gap-4
Bottom nav height  : ~72px
Mobile padding-bot : pb-24 (clearance for bottom nav)
Desktop padding-bot: pb-6
```

---

## 19. SCROLLBAR CUSTOM

```css
::-webkit-scrollbar { width: 8px; height: 8px; }
::-webkit-scrollbar-track { background: #f1f1f1; border-radius: 4px; }
::-webkit-scrollbar-thumb { background: #c1c1c1; border-radius: 4px; }
::-webkit-scrollbar-thumb:hover { background: #a1a1a1; }

.dark ::-webkit-scrollbar-track { background: #374151; }
.dark ::-webkit-scrollbar-thumb { background: #4b5563; }
.dark ::-webkit-scrollbar-thumb:hover { background: #6b7280; }
```

---

## 20. CHART.JS STYLE GUIDE (Opsional)

> Hanya jika project memerlukan grafik. Bundel `chart.min.js` ke folder `lib/`.

### Line Chart

```js
// Income line
{
  borderColor: '#58e49e',
  backgroundColor: 'rgba(88, 228, 158, 0.1)',
  tension: 0.4,
  fill: true,
  pointRadius: 4,
}
// Expense line
{
  borderColor: '#e45858',
  backgroundColor: 'rgba(228, 88, 88, 0.1)',
  tension: 0.4,
  fill: true,
  pointRadius: 4,
}
```

### Bar Chart

```js
// Income bar
{ backgroundColor: 'rgba(88, 228, 158, 0.8)', borderRadius: 6 }
// Expense bar
{ backgroundColor: 'rgba(228, 88, 88, 0.8)', borderRadius: 6 }
```

### Color Palette (8 warna berurutan)

```js
['#6246ea', '#58e49e', '#e45858', '#3b82f6', '#f97316', '#ec4899', '#8b5cf6', '#10b981']
```

---

## 21. KEAMANAN (XSS Prevention)

| Aturan | Detail |
|--------|--------|
| Teks dinamis | Selalu gunakan `.textContent`, **BUKAN** `.innerHTML` |
| HTML statis | `.innerHTML` hanya untuk konten yang kamu kontrol 100% |
| Event binding | **JANGAN** pakai `onclick=""` inline — gunakan `.addEventListener("click", ...)` |
| User input | Sanitize sebelum render ke DOM |
| localStorage | Hanya simpan non-sensitif (preferensi UI, tema) |

---

## 22. CHECKLIST KONSISTENSI

> **AI WAJIB menjalankan checklist ini sebelum memberikan output kode final.**

### Visual

- [ ] Semua tombol → `rounded-full`
- [ ] Semua card → `rounded-3xl`
- [ ] Semua input/select → `rounded-xl`
- [ ] Semua panel sekunder / toast → `rounded-2xl`
- [ ] Hover tombol → shadow berwarna sesuai variant
- [ ] Active tombol → `active:scale-95` atau `active:scale-[0.98]`
- [ ] Shadow card default → `shadow-sm`, hover → `shadow-lg`

### Dark Mode

- [ ] Setiap elemen memiliki pasangan `dark:` modifier
- [ ] Background page → `bg-[#f8f9fa]` / `dark:bg-gray-900`
- [ ] Card → `bg-white` / `dark:bg-gray-800`
- [ ] Input → `bg-gray-50` / `dark:bg-gray-700`
- [ ] Border → `border-gray-100` / `dark:border-gray-700`
- [ ] Teks → `text-text` / `dark:text-white`
- [ ] Dark mode toggle menyimpan ke `localStorage`
- [ ] Flash prevention script di `<head>`

### Offline

- [ ] Font Inter di-embed (self-hosted woff2), BUKAN Google Fonts CDN
- [ ] Tailwind CSS compiled offline (CLI/binary), BUKAN Play CDN
- [ ] Chart.js (jika dipakai) dibundel ke `lib/`
- [ ] Tidak ada `<script src="https://...">` atau `<link href="https://...">`
- [ ] Semua aset (font, CSS, JS, gambar) ada di folder project

### Keamanan

- [ ] Toast menggunakan `.textContent` (bukan `.innerHTML`)
- [ ] Tidak ada `onclick` inline — semua pakai `addEventListener`
- [ ] User input di-sanitize sebelum render

### Kode

- [ ] Transisi default → `duration-200`
- [ ] Shadow hover menggunakan token warna (`shadow-primary/30`)
- [ ] Scrollbar custom di-style via CSS
- [ ] Responsive layout mobile-first
- [ ] Struktur HTML semantik

---

## 23. FEW-SHOT EXAMPLES

### Contoh 1: Membuat Card Statistik

**Prompt:** "Buat card yang menampilkan total income"

**Output yang BENAR:**
```html
<div class="bg-white dark:bg-gray-800 border border-gray-100 dark:border-gray-700
            rounded-3xl p-5 shadow-sm hover:shadow-lg transition-shadow duration-200">
  <div class="flex items-center gap-3 mb-3">
    <div class="w-10 h-10 bg-success/20 rounded-xl flex items-center justify-center">
      <svg class="w-5 h-5 text-success" fill="none" stroke="currentColor"
           viewBox="0 0 24 24" stroke-width="2"
           stroke-linecap="round" stroke-linejoin="round">
        <path d="M13 7h8m0 0v8m0-8l-8 8-4-4-6 6"/>
      </svg>
    </div>
    <span class="text-sm font-medium text-gray-500 dark:text-gray-400">Total Income</span>
  </div>
  <p class="text-2xl font-bold text-text dark:text-white">Rp 12.500.000</p>
  <p class="text-xs text-success mt-1 font-medium">+12.5% dari bulan lalu</p>
</div>
```

**Output yang SALAH (jangan lakukan ini):**
```html
<!-- ❌ rounded-lg bukan rounded-3xl -->
<!-- ❌ tidak ada dark: modifier -->
<!-- ❌ tidak ada shadow-sm / hover:shadow-lg -->
<!-- ❌ tidak ada transition -->
<div class="bg-white rounded-lg p-4 border">
  <h3>Total Income</h3>
  <p>Rp 12.500.000</p>
</div>
```

### Contoh 2: Membuat Tombol dengan Ikon

**Prompt:** "Buat tombol tambah data"

**Output yang BENAR:**
```html
<button class="py-3 px-6 bg-primary text-white font-medium rounded-full
               hover:bg-[#5035cc] hover:shadow-lg hover:shadow-primary/30
               transition-all duration-200 flex items-center gap-2 active:scale-95">
  <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"
       stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
    <path d="M12 4v16m8-8H4"/>
  </svg>
  Tambah Data
</button>
```

**Output yang SALAH:**
```html
<!-- ❌ rounded-md bukan rounded-full -->
<!-- ❌ hardcode warna bukan token -->
<!-- ❌ tidak ada hover shadow berwarna -->
<!-- ❌ tidak ada active:scale -->
<!-- ❌ onclick inline -->
<button class="bg-purple-600 text-white rounded-md px-4 py-2" onclick="add()">
  + Tambah Data
</button>
```

### Contoh 3: Membuat Modal Konfirmasi Hapus

**Prompt:** "Buat modal konfirmasi hapus"

**Output yang BENAR:**
```html
<div class="modal-overlay fixed inset-0 bg-gray-900/60 backdrop-blur-sm z-50
            flex items-center justify-center p-4">
  <div class="modal-content bg-white dark:bg-gray-800 rounded-3xl p-8
              max-w-sm w-full mx-4 shadow-2xl">
    <div class="w-16 h-16 bg-danger/20 dark:bg-danger/30 text-danger
                rounded-full flex items-center justify-center mb-6 mx-auto">
      <svg class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24"
           stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
        <path d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"/>
      </svg>
    </div>
    <h3 class="text-xl font-bold text-text dark:text-white mb-3
               text-center tracking-tight">Hapus Data?</h3>
    <p class="text-gray-500 dark:text-gray-400 mb-8 text-center leading-relaxed">
      Data yang dihapus tidak dapat dikembalikan.
    </p>
    <div class="flex flex-col gap-3">
      <button class="w-full py-3 px-4 bg-danger text-white rounded-full
                     font-medium hover:bg-[#cc4040] transition-colors
                     shadow-sm active:scale-[0.98]">Ya, Hapus</button>
      <button class="w-full py-3 px-4 bg-gray-100 dark:bg-gray-700
                     text-gray-700 dark:text-white rounded-full font-medium
                     hover:bg-gray-200 dark:hover:bg-gray-600 transition-colors
                     active:scale-[0.98]">Batal</button>
    </div>
  </div>
</div>
```

---

## 24. PRINSIP DESAIN (Ringkasan Kepribadian Visual)

| # | Prinsip                    | Detail                                                                   |
|---|----------------------------|--------------------------------------------------------------------------|
| 1 | **Rounded-everything**     | Semua elemen membulat agresif. Card 24px, button pill, input 12px.       |
| 2 | **Soft purple accent**     | `#6246ea` sebagai satu-satunya warna aksi. Lainnya hanya semantik.       |
| 3 | **Tint surface**           | `#EBEBFA` untuk badge/chip bg — bukan warna solid.                       |
| 4 | **Spring animation**       | Modal = overshoot spring. Toast = slide kanan. Terasa hidup.             |
| 5 | **Dark mode native**       | Setiap komponen punya pasangan `dark:`. Transisi mulus.                  |
| 6 | **Outline icons only**     | Heroicons outline, stroke-width 2. Tidak ada fill solid.                 |
| 7 | **Semantic data colors**   | Positif = hijau. Negatif = merah. Netral = kuning/ungu.                  |
| 8 | **Toast dari kanan**       | Notifikasi slide-in dari kanan bawah, auto-dismiss 3.5s.                |
| 9 | **Hover shadow elevation** | Default `shadow-sm` → hover `shadow-lg`. Kedalaman tanpa border tebal.  |
| 10| **Micro-interaction**      | Semua tombol punya `active:scale` untuk feedback press yang nyata.       |
| 11| **Konten lega**            | Padding konsisten, gap teratur, tidak sesak.                             |
| 12| **Offline-first**          | Semua aset lokal. Tidak ada CDN dependency untuk production.             |

---

> **Design Systems v2** — Unified Visual Identity
> Dibuat dari: Interface 1 (Trading R/R), Interface 2 (MoneyWise), Interface 3 (General), & DESIGN_SYSTEM.md
> Untuk: Vibe Coding — AI-Assisted Web Development
