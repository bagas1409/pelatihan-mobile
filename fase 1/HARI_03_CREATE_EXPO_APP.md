# Hari 3: Inisialisasi Proyek Baru Expo SDK
## Membuat Proyek Pertama Donorku Mobile Menggunakan create-expo-app Terbaru Berbasis TypeScript

Selamat datang di Hari 3! Hari ini kita akan mempraktikkan cara membuat proyek aplikasi mobile pertama kita: **DonorKu Mobile** menggunakan script pembuat resmi dari Expo.

Kita akan menggunakan versi Expo SDK terbaru yang secara default sudah mendukung **TypeScript** (untuk keamanan pengetikan tipe data) dan kerangka routing berbasis file modern.

---

## 1. Perintah Membuat Proyek Baru

Buka terminal di komputer Anda, lalu arahkan folder aktif Anda ke lokasi workspace kerja (misalnya `d:\proyek kerja\pak zull\PMI\`). Jalankan perintah pembuatan proyek berikut di terminal:

```bash
npx create-expo-app@latest donorku-mobile --template blank-typescript
```

*   **`npx create-expo-app@latest`**: Menjamin kita selalu mengunduh versi Expo SDK terbaru yang paling stabil.
*   **`donorku-mobile`**: Nama folder proyek aplikasi yang akan dibuat oleh sistem.
*   **`--template blank-typescript`**: Memilih template dasar bersih (blank) yang sudah dikonfigurasi menggunakan TypeScript.

Tunggu sekitar 1 s.d 3 menit selagi npm mengunduh modul React Native, React, dan Expo SDK awal dari internet.

---

## 2. Cara Menyalakan Server Proyek (Start Project)

Setelah instalasi selesai, masuk ke dalam folder proyek baru Anda dan nyalakan server pembangunan menggunakan perintah berikut:

```bash
cd donorku-mobile
npx expo start
```

Di layar terminal Anda akan muncul gambar kode **QR Code** berukuran besar beserta alamat server lokal (misalnya `exp://192.168.1.10:8081`).

---

## 3. Cara Menghubungkan ke Handphone Fisik Anda

1.  Buka aplikasi **Expo Go** di HP Anda.
2.  **Untuk HP Android**: Klik tombol **Scan QR Code**, lalu arahkan kamera HP Anda ke gambar QR Code yang ada di layar terminal laptop Anda.
3.  **Untuk HP iPhone (iOS)**: Buka aplikasi kamera bawaan iOS, scan QR Code tersebut, lalu klik pop-up tautan link yang mengarahkan untuk membuka aplikasi Expo Go.
4.  Tunggu progress bar di HP menyentuh angka 100% (*Bundling JavaScript*). Anda akan melihat tampilan layar putih bertuliskan *"Open up App.tsx to start working on your app!"* terpampang nyata di layar HP Anda!

---

## 4. Latihan Soal Mandiri
1. Amati isi pesan terminal Anda saat menjalankan perintah `npx expo start`.
2. Jelaskan fungsi dari perintah `npx expo start` tersebut bagi kelancaran siklus perubahan kode program Anda secara langsung di layar HP (*Fast Refresh*).
3. Sebutkan berkas file utama di root folder proyek yang bertindak sebagai pintu masuk (*Entry Point*) pertama kali aplikasi digambar di layar HP pada template blank-typescript.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kegunaan Server Metro Bundler & Fast Refresh
*   **Metro Bundler**: Perintah `npx expo start` menyalakan alat bernama **Metro**, yaitu bundler JavaScript khusus untuk React Native. 
*   **Fungsi Fast Refresh**: Metro memantau perubahan teks di file kode kita secara real-time. Jika Anda mengedit kata di VS Code dan menekan tombol save, Metro akan langsung memaketkan perubahan tersebut dan menyuntikkannya ke aplikasi Expo Go di HP Anda lewat jaringan Wi-Fi dalam waktu kurang dari 1 detik (*Hot Reloading*), memotong waktu tunggu compile ulang yang biasanya memakan waktu menit-an pada sistem manual.

### Jawaban 3: Pintu Masuk Aplikasi (Entry Point)
*   Pada template dasar blank-typescript, file pintu masuk pertama kali adalah berkas **`App.tsx`** yang berada di root direktori proyek. Mengubah isi fungsi render di dalam `App.tsx` akan langsung merubah tampilan awal layar HP Anda.
