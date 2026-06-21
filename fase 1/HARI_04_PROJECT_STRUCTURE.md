# Hari 4: Anatomi Struktur Folder Expo SDK Terbaru
## Membedah Folder app, components, constants, assets, Serta Konfigurasi Penting app.json

Selamat datang di Hari 4! Hari ini kita akan mempelajari susunan peta direktori (**Folder Structure**) pada proyek Expo React Native modern.

Memahami letak penempatan file sangat penting agar kode kita tidak berantakan seiring bertambah kompleksnya fitur aplikasi DonorKu PMI. Kita juga akan membedah berkas konfigurasi pusat aplikasi yaitu `app.json`.

---

## 1. Peta Direktori Proyek Expo Modern

Secara umum, struktur folder proyek Expo modern tersusun sebagai berikut:

```
donorku-mobile/
├── assets/             <-- Tempat menyimpan file gambar statis (.png, .jpg), ikon, dan logo PMI.
├── app/                <-- Folder rute halaman (Expo Router). Setiap file di sini mewakili layar HP.
│   ├── (tabs)/         <-- Grup navigasi menu bawah (Beranda, Profil, dll).
│   ├── _layout.tsx     <-- Berkas penata layout global (mengatur font, splashscreen, header).
│   └── index.tsx       <-- Layar pertama kali yang dibuka (Home/Beranda).
├── components/         <-- Tempat menyimpan potongan UI yang bisa dipakai ulang (Button, Card, Input).
├── constants/          <-- Menyimpan variabel statis global (palette warna merah PMI, ukuran margin).
├── hooks/              <-- Tempat menyimpan fungsi custom hooks React (seperti useApi, useAuth).
├── package.json        <-- Daftar pustaka/library pihak ketiga yang kita gunakan.
├── tsconfig.json       <-- Konfigurasi compiler TypeScript.
└── app.json            <-- Konfigurasi pusat identitas aplikasi untuk Android & iOS.
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
