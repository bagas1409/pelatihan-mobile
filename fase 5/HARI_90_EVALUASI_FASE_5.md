# Hari 90: Evaluasi Fase 5 & Ujian Kelulusan Akhir (Graduation Day)
## Tantangan Final: Mengintegrasikan Grafik Stok Darah Terkini, Mengaktifkan Alarm Push Notification CITO, Dan Panduan EAS Production Build

Selamat datang di Hari 90! Hari ini adalah **hari puncak kelulusan dari seluruh rangkaian program 90 Hari Belajar Mobile Front-End Developer**.

Hari ini, Anda ditantang menyelesaikan **Ujian Akhir Kelayakan Produksi (Final Graduation Exam)** yang menggabungkan visualisasi data grafis, sistem keamanan credentials, otorisasi data, dan penerimaan notifikasi real-time ke dalam satu kesatuan sistem komersial.

---

## 1. Spesifikasi Proyek Ujian Akhir Akhir (PMI DonorKu Dashboard)

Anda diminta membangun modul analisis dashboard untuk markas pusat UDD PMI Lampung dengan spesifikasi sebagai berikut:

1.  **Halaman Dashboard Analitik Petugas** (`app/(tabs)/index.tsx`):
    *   Sajikan **BarChart** yang secara visual dinamis menggambarkan perbandingan stok darah ter-update (mengunduh data asli dari endpoint backend `/blood-stocks` yang telah kita saring menggunakan variabel `.env`).
    *   Di bawah grafik batang, pasang **PieChart** donat yang menyajikan data komposisi persentase donasi golongan darah bulan ini.
    *   Tampilkan notifikasi banner merah tebal jika ada salah satu golongan darah yang sisa stoknya di bawah 40 bag.
2.  **Sistem Registrasi Push Notification**:
    *   Saat petugas pertama kali masuk ke dashboard, panggil fungsi `ambilPushTokenPetugas` (Hari 80).
    *   Jika token berhasil diterbitkan oleh OS HP, kirimkan string token tersebut ke backend API Express.js `/users/save-token` via Axios POST untuk disimpan secara permanen di database.
3.  **Pengujian Aliran Notifikasi CITO (End-to-End)**:
    *   Gunakan aplikasi Postman / pemicu API backend untuk memanggil endpoint Express.js `/api/v1/notifications/send-cito` yang kita rancang di Hari 81.
    *   HP penguji **wajib menerima banner push notification berdering** bertuliskan *"🚨 PANGGILAN CITO DARURAT"*.
    *   Saat banner notifikasi diklik, aplikasi yang sedang ditutup harus otomatis terbangun terbuka, melompati halaman login, dan langsung menyajikan layar peta posisi rumah sakit peminta CITO tersebut.
4.  **Kompilasi Akhir UAT APK**:
    *   Kemas konfigurasi profile preview di file `eas.json`.
    *   Jalankan kompilasi `eas build --platform android --profile preview` di terminal.
    *   Unduh file `.apk` hasil rakitan server Expo Cloud melalui QR Code, instal di HP Android Anda, dan tunjukkan kepada dosen/penguji PMI Lampung bahwa aplikasi berjalan lancar 60 FPS tanpa lag memori.

---

## 2. Checklist Rilis Produksi Google Play Store

Sebelum menekan tombol submit rilis, pastikan berkas administrasi Play Store Anda sudah centang hijau:
*   [ ] Akun Google Play Console berstatus aktif penuh (Biaya $25 USD lunas terverifikasi KTP - Hari 86).
*   [ ] Berkas Keystore tanda tangan digital aman tersimpan rapi di Cloud Credentials Expo (Hari 87).
*   [ ] URL API Server backend diubah dari localhost `10.0.2.2` menjadi alamat domain HTTPS production asli PMI (misalnya `https://api.pmilampung.or.id`) di dalam file `.env` (Hari 89).
*   [ ] File biner AAB produksi sukses ter-upload otomatis ke dashboard Play Console via perintah `eas submit` (Hari 88).

---

## 3. Selamat Atas Kelulusan Anda! 🎓

Jika Anda berhasil menyelesaikan proyek integrasi analitik chart dan push token ini, serta sukses menginstal file APK hasil rakitan EAS Cloud di handphone Anda sendiri, selamat! **Anda kini resmi dinyatakan LULUS sebagai FRONT-END MOBILE DEVELOPER SIAP KERJA.**

Anda telah bermutasi dari seorang pemula nol yang buta pemrograman mobile, menjadi developer profesional yang mampu:
*   Membangun tata letak UI mobile yang modern, responsif, dan animatif dengan React Native + NativeWind.
*   Mengelola navigasi rute dinamis yang aman memanfaatkan sistem file-based Expo Router.
*   Mengendalikan manajemen data state global terpusat yang presisi menggunakan Zustand.
*   Menghubungkan aplikasi ke server basis data nyata Express.js secara aman berbekal JWT Token, Axios Interceptors, & SecureStore.
*   Mengaktifkan sensor native hardware tingkat tinggi: Peta Google Maps, GPS Tracker, Lensa Kamera, dan File System Sharing.
*   Mengoptimasi memori RAM list agar hemat energi, bersahabat dengan sinyal lelet pedesaan via Caching.
*   Mengaktifkan alarm detak jantung aplikasi Push Notification, dan merilisnya secara mandiri ke pasar Google Play Store.

Pertahankan semangat belajar Anda, ikuti terus perkembangan Expo SDK versi terbaru, dan jadilah pahlawan kemanusiaan digital melalui baris-baris kode aplikasi DonorKu PMI Anda!
```
[ROADMAP BELAJAR FRONT-END MOBILE 90 HARI SELESAI - KELAS PROFESIONAL]
```
