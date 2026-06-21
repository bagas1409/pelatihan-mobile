# Hari 83: Pengenalan EAS Build Setup
## Memahami Ekosistem EAS (Expo Application Services) Cloud Build Untuk Mengompilasi File Installer Android APK

Selamat datang di Hari 83! Kita telah menyelesaikan seluruh penulisan kode fungsional aplikasi mobile DonorKu PMI. Mulai hari ini, kita akan memasuki tahap persiapan rilis: **EAS Build Setup**.

Untuk menjalankan aplikasi mobile di handphone petugas tanpa kabel USB laptop, kita membutuhkan berkas installer fisik: **`.apk`** (untuk Android) atau **`.ipa`** (untuk iPhone/iOS). 
Dahulu, mengompilasi file ini mengharuskan kita mengunduh Android Studio berukuran 10 Gigabyte yang sangat memberatkan RAM laptop. Hari ini, kita akan memasang sistem kompilasi awan modern: **EAS Build** (Expo Application Services), di mana proses kompilasi kode kita akan dilemparkan ke server cloud komputer milik tim Expo secara gratis.

---

## 1. Apa Itu EAS?

**EAS (Expo Application Services)** adalah layanan cloud hosting khusus untuk melakukan siklus perakitan aplikasi mobile (CI/CD).
*   Kita mengirim kode React Native kita ke server Expo Cloud via terminal.
*   Server Expo Cloud merakitnya (membangun gradle/gradlew di OS Linux awan mereka).
*   Setelah selesai, server Expo Cloud mengirimkan link download file `.apk` mentah kepada kita untuk di-download langsung dari HP.

---

## 2. Langkah Persiapan Awal Pendaftaran Akun

1.  Buka situs resmi Expo di browser Anda: **[expo.dev](https://expo.dev)**.
2.  Daftarkan satu akun Developer gratis. Catat Username dan Password Anda.
3.  Buka terminal proyek VS Code Anda, instal modul global pembantu EAS CLI:
    ```bash
    npm install -g eas-cli
    ```
4.  Hubungkan terminal laptop Anda ke akun Expo awan yang baru dibuat tadi dengan mengetikkan perintah login:
    ```bash
    eas login
    ```
    *Masukkan email dan password akun Expo Anda di terminal prompt.*

---

## 3. Latihan Soal Mandiri
1. Apa singkatan dari istilah **EAS** pada ekosistem Expo modern? Jelaskan perbedaan mencolok kinerjanya dibandingkan kompilasi manual menggunakan Android Studio lokal.
2. Sebutkan ekstensi nama file installer paket aplikasi mentah yang wajib dihasilkan agar bisa langsung diinstal di handphone petugas berbasis OS Android.
3. Sebutkan perintah terminal cmd untuk menghubungkan identitas laptop lokal Anda dengan akun penyimpanan server awan Expo.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Expo Application Services (Cloud Build Engine)
*   **Penyelamat RAM Laptop**: Kompilasi lokal menuntut RAM minimal 16GB dan kapasitas harddisk kosong besar untuk Android SDK. 
*   EAS (Expo Application Services) melimpahkan seluruh beban kompilasi berat tersebut ke supercomputer awan milik Expo, membebaskan laptop kentang developer dari beban panas berlebih.

### Jawaban 2: File APK (.apk)
*   Berkas tersebut berformat **`.apk`** (Android Package Kit) atau format modern Google Play Store **`.aab`** (Android App Bundle).

### Jawaban 3: Perintah eas login
*   Perintah terminal tersebut adalah **`eas login`**.
```
[FASE 5: HARI 83 SUKSES]
```
