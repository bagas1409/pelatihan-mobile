# Hari 85: Eksekusi Perintah EAS Build
## Menjalankan Kompilasi File APK Uji Coba Di Server Cloud Expo Menggunakan Perintah Terminal eas build --profile preview

Selamat datang di Hari 85! Hari ini adalah hari bersejarah di mana kita akan **mengeksekusi proses perakitan fisik pertama** aplikasi mobile DonorKu PMI Lampung ke server cloud Expo.

Kita akan menggunakan profil `"preview"` yang sudah kita definisikan di Hari 84 untuk melahirkan file installer **`.apk`** pertama kita.

---

## 1. Perintah Eksekusi Perakitan APK Android

Buka terminal proyek Anda, pastikan laptop Anda terhubung ke internet yang stabil, lalu ketik perintah sakti berikut:

```bash
eas build --platform android --profile preview
```

---

## 2. Rantai Proses yang Terjadi di Terminal Anda

Ketika perintah di atas ditekan, terminal Anda akan memandu Anda melalui alur interaktif berikut:
1.  **Generate Credentials (Kunci Enkripsi)**: Server Expo akan bertanya: *"Apakah Anda ingin membiarkan Expo mengelola kunci tanda tangan digital Anda?"*. 
    *   **Pilih: `Yes`** (Biarkan sistem Expo mengurus sertifikat tanda tangan keamanan Anda di server mereka).
2.  **Upload Project**: Terminal akan memadatkan folder proyek Anda (kecuali folder `node_modules` yang diblokir oleh `.gitignore`) dan mengunggahnya ke server Expo Cloud.
3.  **Antrean Cloud (Build Queue)**: Terminal akan memunculkan tautan link (contoh: `https://expo.dev/builds/xxxx`). Anda bisa mengklik link ini untuk melihat progres logs perakitan Gradle di browser Anda.
4.  **Selesai & Scan QR**: Setelah menunggu sekitar 5 s.d 10 menit, terminal akan menggambar sebuah **QR Code besar**. Anda cukup mengaktifkan kamera HP Anda, mengarahkan lensa ke QR Code tersebut untuk membuka link download, mengunduh file `.apk` nya, dan memasangnya di HP Android Anda secara fisik!

---

## 3. Latihan Soal Mandiri
1. Sebutkan perintah terminal lengkap untuk memicu proses kompilasi awan khusus ditargetkan bagi platform sistem operasi Android dengan profil Preview!
2. Mengapa kita disarankan memilih jawaban "Yes" ketika terminal EAS bertanya mengenai pengelolaan sertifikat tanda tangan digital oleh pihak server Expo Cloud?
3. Sebutkan nama file pengecualian (`ignore file`) yang menjamin folder berukuran besar `node_modules` tidak ikut terkirim secara boros ke internet saat proses upload kode ke server Expo berlangsung.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Perintah Pemicu Kompilasi
*   Perintah terminal tersebut adalah **`eas build --platform android --profile preview`**.

### Jawaban 2: Kemudahan Pengelolaan Sertifikat (Automatic Provisioning)
*   Pembuatan sertifikat keamanan Android (*Keystore*) secara manual sangat rumit karena melibatkan perintah baris java keytool yang rawan salah. 
*   Membiarkan Expo mengelolanya secara otomatis (*Managed Credentials*) membebaskan developer dari keharusan merawat file sertifikat tersebut secara fisik, karena Expo akan menyimpannya di brankas awan mereka yang aman.

### Jawaban 3: File .gitignore
*   Berkas filter tersebut bernama **`.gitignore`** (atau `.easignore`).
```
[FASE 5: HARI 85 SUKSES]
```
