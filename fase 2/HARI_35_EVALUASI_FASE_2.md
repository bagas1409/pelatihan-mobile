# Hari 35: Evaluasi Fase 2 - Ujian Navigasi & Struktur Multi-Halaman
## Tantangan Akhir: Membangun Navigasi Tab Bawah Dinas Lengkap Dengan Halaman Detail Dinamis [id], Modal Filter, & Banner Sinyal Offline

Selamat datang di Hari 35! Ini adalah **hari terakhir dari Fase 2**.

Dalam 20 hari terakhir ini, Anda telah berhasil melompat dari pembuat layout satu halaman menjadi perancang sistem aplikasi multi-halaman yang dinamis. Anda telah menguasai Expo Router (File-based Routing), Stack Navigation, Tab Navigation, Drawer, navigasi terprogram (`router`), Dynamic Routes (`[id]`), pengiriman Query Params, kustomisasi header bar merah PMI, transisi Modal Screen, penanganan sensor Back fisik Android, siklus hidup React (`useEffect` & `useFocusEffect`), penjinakan Memory Leaks, Animasi Transisi khusus, penanganan Hydration, Custom Hooks, Dark Mode, dan deteksi sinyal NetInfo.

Hari ini Anda ditantang menyatukan seluruh sistem navigasi tersebut ke dalam satu proyek kesatuan utuh.

---

## 1. Spesifikasi Proyek Ujian Akhir Fase 2

Anda diminta membangun struktur folder navigasi portal operasional dinas PMI Lampung dengan arsitektur rute sebagai berikut:

*   **Navigasi Utama Tab Bawah (Tabs)**:
    *   **Tab 1: Beranda** (`app/(tabs)/index.tsx`) $\rightarrow$ Menampilkan daftar data simulasi relawan. Mengklik nama relawan wajib memicu navigasi terprogram (`router.push`) menuju rute detail dinamis `/relawan/[id]`.
    *   **Tab 2: Riwayat** (`app/(tabs)/riwayat.tsx`) $\rightarrow$ Menampilkan log pengiriman CITO. Memiliki tombol "Saring Data" yang jika ditekan akan membuka layar modal `/filter-stok` dari bawah ke atas.
*   **Halaman Detail Dinamis** (`app/relawan/[id].tsx`):
    *   Menampilkan ID/NIK relawan yang diklik secara dinamis di layar menggunakan hook `useLocalSearchParams()`.
    *   Memiliki header bar atas kustom berwarna merah PMI.
*   **Modal Screen** (`app/filter-stok.tsx`):
    *   Membuka lembaran pop-up filter dari bawah ke atas menggunakan konfigurasi `presentation: 'modal'`.
*   **Banner Pengaman Internet (NetInfo)**:
    *   Banner peringatan merah offline wajib terpasang secara global di halaman utama layout agar otomatis menyala jika sinyal dimatikan di emulator/HP Anda.

---

## 2. Struktur Direktori Hasil Akhir yang Wajib Terbentuk

```
donorku-mobile/
├── components/
│   ├── BannerInternetMati.tsx
│   └── JamPiketDinas.tsx
├── hooks/
│   └── useSisaHari.ts
└── app/
    ├── (tabs)/
    │   ├── _layout.tsx       <-- Tabs configuration (Beranda & Riwayat)
    │   ├── index.tsx         <-- Home (List relawan -> navigasi ke detail)
    │   └── riwayat.tsx       <-- History (Button filter -> open modal)
    ├── relawan/
    │   └── [id].tsx          <-- Dynamic page (header merah PMI)
    ├── filter-stok.tsx       <-- Modal screen (slide dari bawah)
    └── _layout.tsx           <-- Root Stack layout (PortalProvider & Banner internet)
```

---

## 3. Langkah Pengujian Kelayakan Navigasi

Jalankan server proyek Anda dengan `npx expo start` dan lakukan 4 langkah audit visual berikut di HP fisik Anda:
1.  **Uji Coba Tab Bawah**: Klik menu Beranda dan Riwayat di bagian bawah HP. Layar wajib berpindah halus secara instan.
2.  **Uji Coba Rute Dinamis [id]**: Klik nama relawan Budi di beranda $\rightarrow$ Layar wajib melakukan transisi slide dari kanan ke kiri menampilkan ID Budi secara tepat dengan judul header berwarna merah PMI. Tekan tombol back di pojok kiri atas untuk kembali.
3.  **Uji Coba Modal**: Klik tombol filter di halaman Riwayat $\rightarrow$ Layar modal filter wajib meluncur naik dari bawah ke atas. Klik tombol batal $\rightarrow$ Modal wajib meluncur turun menghilang kembali ke bawah.
4.  **Uji Coba Offline**: Matikan koneksi Wi-Fi/Paket Data di HP fisik Anda (atau nyalakan Airplane Mode) $\rightarrow$ Banner merah bertuliskan *"Koneksi Internet Terputus"* wajib langsung menyembul instan di bagian atas layar HP Anda secara otomatis. Nyalakan kembali Wi-Fi $\rightarrow$ Banner wajib lenyap seketika.

---

## 4. Pesan Kelulusan Fase 2

Jika seluruh mekanisme navigasi dan integrasi sensor di atas berjalan tanpa memicu crash di HP Anda, selamat! Anda dinyatakan **LULUS FASE 2**.

Di **Fase 3 (Hari 36 s.d Hari 55)**, kita akan masuk ke area pengodean backend terintegrasi: menginstal Axios, menembak server REST API database relawan asli, menangani CORS di mobile, membuat manajemen state global **Zustand** (menyimpan data login dinas & JWT token secara aman di KeyStore HP menggunakan `expo-secure-store`), serta merancang form validasi ketat menggunakan **React Hook Form** dan **Zod Schema**.
```
[FASE 2: EXPO ROUTER & LIFECYCLE COMPLETED - PROGRESS 35/90 DAYS]
```
