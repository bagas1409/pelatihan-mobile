# Hari 87: Pembuatan Keystore Android
## Memahami Konsep Tanda Tangan Digital Keystore Keamanan Android Dan Menyimpannya Dengan Aman Di Cloud Credentials Expo

Selamat datang di Hari 87! Akun developer Google Play Store Anda di Hari 86 telah terverifikasi. Sebelum kita meluncurkan berkas ke Google, Google menetapkan aturan ketat: **Setiap file aplikasi yang disetor ke Play Store wajib ditandatangani secara digital menggunakan file sertifikat kunci rahasia yang bernama Keystore**.

Keystore bertindak layaknya "Cap Stempel Basah Tanda Tangan Basah Resmi" perusahaan PMI Lampung. Jika stempel ini hilang, kita tidak akan pernah bisa mengirimkan update pembaruan versi aplikasi DonorKu selamanya!

---

## 1. Apa itu Android Keystore?

*   **File Keystore** (biasanya bernama `my-release-key.keystore` atau `credentials.jks`) berisi kunci kriptografi privat bersandi rumit.
*   **Pembuktian Kepemilikan**: Google menggunakan kunci ini untuk mencocokkan apakah update aplikasi yang Anda kirimkan di masa depan ditandatangani oleh orang yang sama dengan yang membuat aplikasi pertama kali.
*   Jika di masa depan Anda kehilangan file Keystore ini, **Google akan menolak seluruh file update Anda**, memaksa Anda membuat nama paket aplikasi baru dari nol yang menyebabkan jumlah statistik ribuan download instalasi relawan Anda hangus musnah!

---

## 2. Cara Kerja Credentials Management di Expo Cloud

Expo CLI mempermudah proses pembuatan dan perawatan berkas Keystore ini. Di Hari 85, saat Anda memilih opsi **`Yes`** ketika EAS bertanya tentang credentials, sistem Expo Cloud secara otomatis melakukan langkah berikut di balik layar:
1.  Expo membuat file Keystore baru secara acak yang dienkripsi ketat di server mereka.
2.  Expo mencatat sandi *Key Password* dan *Store Password* di database credentials akun pengembang Anda.
3.  Setiap kali Anda mengetikkan perintah `eas build`, server cloud Expo menarik file Keystore dari brankas mereka untuk menyegel file biner aplikasi Anda sebelum diunduh.

---

## 3. Cara Membaca / Mem-backup Keystore Anda dari Cloud Expo

Untuk mengantisipasi jika di kemudian hari Anda ingin melepaskan diri dari layanan Expo dan ingin merakit aplikasi secara manual memakai Android Studio komputer lokal, Anda wajib mengunduh cadangan (*Backup*) file Keystore tersebut ke harddisk laptop Anda:

Ketik perintah berikut di terminal proyek:
```bash
eas credentials
```
*Perintah interaktif ini akan menanyakan platform mana yang ingin dilihat (Pilih Android), lalu pilih opsi "Credentials" -> "Download Keystore" untuk menyalin kunci rahasia tersebut ke laptop Anda. Simpan file ini di Google Drive rahasia organisasi PMI.*

---

## 4. Latihan Soal Mandiri
1. Apa fungsi dari berkas **Keystore** dalam ekosistem sistem operasi Android? Mengapa file ini bertindak sebagai stempel tanda tangan digital resmi?
2. Jelaskan konsekuensi buruk jangka panjang yang akan menimpa developer jika mereka tidak sengaja menghapus atau kehilangan file Keystore yang terasosiasi dengan aplikasi yang sudah terbit di Google Play Store!
3. Sebutkan perintah terminal EAS CLI untuk mengekstrak atau mem-backup file credentials Keystore Anda dari awan server Expo ke media penyimpanan harddisk laptop lokal.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Pembuktian Keabsahan File (Code Signing Proof)
*   **Segel Keamanan**: Keystore bertindak sebagai tanda tangan digital bersandi kriptografi privat. 
*   Google memakainya untuk memverifikasi bahwa file pembaruan (update) aplikasi yang di-upload ke Play Store benar-benar dibuat oleh developer asli yang memegang otoritas penuh, bukan file palsu tiruan yang disusupi peretas (*Man-in-the-middle attack*).

### Jawaban 2: Kehilangan Hak Akses Update Aplikasi Selamanya
*   **Kiamat Aplikasi**: Google Play Store tidak memiliki tombol reset manual untuk Keystore lama yang hilang demi alasan keamanan. 
*   Developer terpaksa menghapus aplikasi lamanya dari Play Store publik, merubah nama paket (*Package Name / ID Application*), dan meng-uploadnya kembali sebagai aplikasi baru. Petugas relawan lama tidak akan mendapatkan notifikasi update otomatis dan terpaksa menghapus manual aplikasi lamanya untuk mengunduh ulang aplikasi yang baru.

### Jawaban 3: Perintah eas credentials
*   Perintah terminal tersebut adalah **`eas credentials`**.
```
[FASE 5: HARI 87 SUKSES]
```
