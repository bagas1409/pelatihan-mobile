# Hari 15: Evaluasi Fase 1 - Ujian Pembuatan UI Mobile
## Tantangan Akhir: Membuat Antarmuka Kartu Profil Relawan PMI Dengan NativeWind, ScrollView, Serta Menggunakan custom Google Fonts

Selamat datang di Hari 15! Ini adalah **hari terakhir dari Fase 1**.

Dua minggu ini Anda telah meletakkan batu fondasi terpenting untuk berkarir sebagai Mobile Coder. Anda telah memahami arsitektur baru React Native, menginstal Expo SDK, menguasai elemen JSX (`View`, `Text`, `Image`), styling kelas Tailwind (**NativeWind v4**), tata letak Flexbox (row, col, justify, align), input `TextInput`, tombol reaktif `TouchableOpacity`, gulir layar `ScrollView` di dalam proteksi `SafeAreaView`, render data FlatList & SectionList, konsep pengiriman Props TypeScript, reaktivitas `useState`, dan integrasi kustom font.

Hari ini Anda ditantang menyatukan seluruh materi tersebut ke dalam satu tantangan proyek visual.

---

## 1. Spesifikasi Proyek Ujian Akhir Fase 1

Anda diminta membuat halaman profil ringkas relawan donor darah PMI Lampung dengan ketentuan desain premium sebagai berikut:

*   **Header Halaman (Aksesibilitas)**:
    *   Wajib dibungkus di dalam `SafeAreaView` agar tidak tertabrak poni kamera HP.
    *   Menggunakan font kustom `'Outfit-Bold'` untuk judul header: *"Profil Pendonor"*.
*   **Area Scroll Konten (ScrollView)**:
    *   Seluruh konten diletakkan di dalam `ScrollView` yang halus tanpa garis scrollbar samping.
*   **Visual Foto Avatar Relawan (Image)**:
    *   Foto profil bulat sempurna berdiameter 100 piksel dengan bingkai warna merah tipis, dimuat dari tautan internet.
*   **Data Riwayat Menggunakan Props (Reusable Component)**:
    *   Mencetak minimal 3 riwayat donor terakhir relawan tersebut menggunakan komponen anak `KartuRiwayat` (Hari 12) yang menerima data via Props TypeScript.
*   **Interaksi Tombol & Input (useState)**:
    *   Menampilkan kolom input ketikan komentar masukan catatan medis.
    *   Menampilkan tombol penambah jumlah kantong donor yang reaktif (useState counter).

---

## 2. Struktur Berkas yang Direkomendasikan

```
donorku-mobile/
├── assets/
│   └── fonts/
│       ├── Outfit-Bold.ttf
│       └── Inter-Regular.ttf
├── components/
│   ├── KartuRiwayat.tsx      <-- Komponen penerima Props TypeScript (Hari 12)
│   └── CounterKantong.tsx    <-- Komponen state counter (Hari 13)
└── app/
    ├── _layout.tsx           <-- Memuat berkas font & mencegah splashscreen menutup (Hari 14)
    └── index.tsx             <-- Halaman utama ujian perakitan layout gabungan
```

---

## 3. Langkah Pengujian Ujian Mandiri

1.  Jalankan server proyek menggunakan perintah `npx expo start`.
2.  Buka aplikasi **Expo Go** di HP fisik Anda, scan QR Code, dan perhatikan tampilan layar:
    *   *Uji Coba 1*: Scroll layar ke bawah $\rightarrow$ Seluruh card riwayat donor di bawah harus tergulir mulus tanpa terpotong.
    *   *Uji Coba 2*: Klik tombol tambah (+) dan kurang (-) pada counter $\rightarrow$ Angka reaktif harus bertambah/berkurang instan tanpa lag.
    *   *Uji Coba 3*: Pastikan tidak ada teks yang menabrak icon baterai atas HP Anda (SafeAreaView aktif).

---

## 4. Pesan Kelulusan Fase 1

Jika seluruh elemen di atas tergambar manis di layar handphone Anda tanpa melempar layar merah error di Expo Go, selamat! Anda dinyatakan **LULUS FASE 1**. 

Anda sudah menguasai arsitektur dasar UI di perangkat mobile.

Di **Fase 2 (Hari 16 s.d Hari 35)**, kita akan naik kelas mempelajari penanganan **Expo Router** untuk membuat rute navigasi antar halaman sesungguhnya, navigasi tab bawah, navigasi samping, rute dinamis detail, dan hook siklus hidup navigasi React Native.
```
[FASE 1: FONDASI REACT NATIVE & NATIVEWIND SUKSES]
```
