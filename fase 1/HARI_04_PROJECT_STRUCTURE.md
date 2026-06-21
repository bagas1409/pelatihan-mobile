# Hari 4: Anatomi Struktur Folder Expo SDK Terbaru
## Membedah Folder app, components, constants, assets, Serta Konfigurasi Penting app.json

Selamat datang di Hari 4! Hari ini kita akan mempelajari susunan peta direktori (**Folder Structure**) pada proyek Expo React Native modern.

Memahami letak penempatan file sangat penting agar kode kita tidak berantakan seiring bertambah kompleksnya fitur aplikasi DonorKu PMI. Kita juga akan membedah berkas konfigurasi pusat aplikasi yaitu `app.json`.

---

## 1. Peta Direktori Proyek Expo Modern

Proyek Expo Router modern dapat dikonfigurasi dalam dua bentuk tata letak folder: **Root-level Layout** (folder di tingkat paling luar) atau **`src/` Folder Layout** (semua folder dibungkus di dalam folder `src/`). 

Di proyek **`belajar`** kita, template diinisialisasi menggunakan struktur **`src/` Folder Layout** dengan aliases `@/*` untuk kerapian.

### Opsi A: Struktur Folder `src/` (Digunakan di proyek `belajar` kita)
```
belajar/
├── assets/             <-- Menyimpan asset gambar statis (.png, .jpg), ikon, dan logo PMI.
├── src/                <-- Folder pembungkus utama kode sumber
│   ├── app/            <-- Folder rute halaman (Expo Router). Setiap file di sini mewakili layar HP.
│   │   ├── _layout.tsx <-- Layout global (font loading, splash screen, dll.)
│   │   ├── index.tsx   <-- Layar pertama kali terbuka (Home/Beranda)
│   │   └── explore.tsx <-- Layar menu explore
│   ├── components/     <-- Komponen UI reusable (Button, Card, Input)
│   ├── constants/      <-- Variabel statis global (misal: warna dasar merah PMI)
│   └── hooks/          <-- Custom hooks React (useApi, useAuth, dll.)
├── package.json        <-- Daftar dependensi npm proyek
├── tsconfig.json       <-- Konfigurasi TypeScript dan path alias (seperti @/* -> ./src/*)
└── app.json            <-- Konfigurasi identitas resmi aplikasi
```

### Opsi B: Struktur Folder Tingkat Root (Tanpa folder `src/`)
```
donorku-mobile/
├── assets/             <-- Tempat menyimpan file gambar statis, ikon, dan logo PMI.
├── app/                <-- Folder rute halaman (Expo Router).
│   ├── _layout.tsx     <-- Berkas penata layout global.
│   └── index.tsx       <-- Layar pertama kali yang dibuka.
├── components/         <-- Tempat menyimpan potongan UI reusable.
├── constants/          <-- Menyimpan variabel statis global.
├── hooks/              <-- Tempat menyimpan fungsi custom hooks React.
├── package.json        <-- Daftar pustaka/library pihak ketiga.
└── app.json            <-- Konfigurasi pusat identitas aplikasi.
```

---

## 2. Membedah File Konfigurasi `app.json`

File `app.json` adalah berkas KTP/Identitas resmi aplikasi kita. Isinya dibaca oleh sistem operasi Android dan iOS saat aplikasi diinstal ke HP.

Contoh konfigurasi standar:
```json
{
  "expo": {
    "name": "DonorKu Mobile",        // Nama aplikasi yang muncul di bawah ikon layar HP
    "slug": "donorku-mobile",        // ID nama proyek di akun server Expo Developer
    "version": "1.0.0",              // Versi aplikasi rilis (bisa dinaikkan saat update app)
    "orientation": "portrait",       // Mengunci layar agar tidak berputar ke samping (landscape)
    "icon": "./assets/icon.png",     // Gambar ikon logo aplikasi
    "scheme": "donorku",             // Nama deep link pemicu agar web bisa membuka app ini
    "userInterfaceStyle": "light",   // Mode visual awal (light / dark / automatic)
    "splash": {
      "image": "./assets/splash-image.png", // Gambar pembuka (splashscreen) saat aplikasi dinyalakan
      "backgroundColor": "#ffffff"
    },
    "ios": {
      "supportsTablet": false
    },
    "android": {
      "package": "com.pmi.donorku",  // NAMA PACKAGE UNIK (Wajib unik di seluruh dunia untuk Play Store!)
      "adaptiveIcon": {
        "foregroundImage": "./assets/adaptive-icon.png",
        "backgroundColor": "#ffffff"
      }
    }
  }
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari parameter `"android": { "package": "com.pmi.donorku" }` di dalam file `app.json` di atas? Mengapa nilainya tidak boleh sama dengan aplikasi milik orang lain?
2. Di dalam folder mana kita wajib meletakkan berkas gambar logo PMI Lampung agar bisa dipanggil oleh tag `<Image>` di kode program?
3. Apa perbedaan fungsi antara file `package.json` dengan file `app.json`?

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Pentingnya Android Package Name (Application ID)
*   **Identitas Unik**: Parameter `package` bertindak sebagai koordinat identitas fisik aplikasi di database Google Play Store dan di dalam memori internal OS Android. 
*   Jika Anda menggunakan nama package yang sama dengan aplikasi lain yang sudah ada di Play Store, Google akan menolak proses upload aplikasi Anda. Di dalam HP Android, memasang aplikasi dengan package yang sama juga akan menimpa/menghapus aplikasi yang lama karena dianggap sebagai program yang sama.

### Jawaban 2: Folder Assets
*   Seluruh file gambar statis, ilustrasi, suara notifikasi, dan dokumen lokal wajib diletakkan di dalam folder **`assets/`** agar aman dikemas oleh Expo compiler saat rilis APK nanti.

### Jawaban 3: Perbedaan package.json vs app.json
*   **`package.json`**: Mengelola dependensi kode pemrograman JavaScript/TypeScript dan library npm (seperti React, Axios, Zustand) yang dibutuhkan oleh developer saat menulis kode.
*   **`app.json`**: Mengelola konfigurasi kompilasi native biner untuk sistem operasi handphone (seperti perizinan kamera, ikon launcher, splashscreen, versi build Android/iOS, nama package Play Store).
