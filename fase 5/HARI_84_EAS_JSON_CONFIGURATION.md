# Hari 84: File Konfigurasi eas.json
## Merancang Pembagian Profil Build (Development, Preview, Production) Menggunakan File Konfigurasi eas.json

Selamat datang di Hari 84! Kemarin kita sukses menghubungkan terminal dengan cloud Expo. Hari ini kita akan merancang file konfigurasi utama perakitan yang bernama **`eas.json`**.

Kita tidak boleh merakit aplikasi rilis toko Google Play Store dengan setelan uji coba local development (misalnya setelan IP localhost `10.0.2.2`). Kita harus memilah profil kompilasi menjadi 3 jalur steril:
1.  **development**: Profil khusus untuk debug dengan koneksi kabel laptop.
2.  **preview**: Profil penghasil file `.apk` mentah untuk dibagikan ke internal tim PMI Lampung guna uji kelayakan (*UAT Testing*).
3.  **production**: Profil khusus peluncur biner `.aab` terenkripsi rahasia yang siap disetor ke Google Play Store publik.

---

## 1. Pembuatan File eas.json Secara Otomatis

Jalankan perintah inisialisasi awal berikut di terminal proyek Anda:
```bash
eas build:configure
```
*Perintah ini akan mendeteksi proyek Anda dan melahirkan satu file baru bernama `eas.json` di direktori root proyek.*

---

## 2. Struktur Kode File `eas.json` yang Premium

Buka file **`eas.json`** yang baru tercipta, lalu modifikasi isian konfigurasinya agar memiliki setelan pengaman rilis APK berikut:

```json
{
  "cli": {
    "version": ">= 9.0.0"
  },
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal"
    },
    
    // 1. PROFIL PREVIEW: PENGHASIL APK INSTALAN MANUAL
    "preview": {
      "distribution": "internal",
      "android": {
        "buildType": "apk" // PENTING: Paksa server menghasilkan file .apk biasa agar bisa di-share via WA!
      }
    },
    
    // 2. PROFIL PRODUCTION: PENGHASIL BINER GOOGLE PLAY STORE
    "production": {
      "android": {
        "buildType": "app-bundle" // Menghasilkan file .aab (syarat mutlak Play Store modern)
      }
    }
  },
  "submit": {
    "production": {}
  }
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari pemanggilan perintah inisialisasi awal `eas build:configure` di terminal proyek Anda?
2. Buka kode konfigurasi profil `"preview"` di atas. Mengapa kita wajib menyisipkan baris pengaturan `"buildType": "apk"` di sana? Apa efek buruknya bagi kurir penguji jika kita lupa menyetelnya?
3. Sebutkan perbedaan format output file kompilasi yang dihasilkan antara profil `"preview"` dengan profil `"production"`.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Inisialisasi Berkas Kemudi Cloud (Config Generator)
*   **Generator Otomatis**: Perintah ini bertugas menganalisis susunan folder Anda, memeriksa keabsahan file `app.json`, mendaftarkan proyek ke dashboard cloud expo.dev Anda secara aman, lalu menulis berkas baru `eas.json` yang berisi kerangka dasar parameter perintah kompilator.

### Jawaban 2: Keharusan Output APK (Instalan Mentah)
*   **Kompatibilitas WhatsApp Share**: Secara default, server Expo akan menghasilkan file berformat `.aab` untuk seluruh profil. File `.aab` **tidak akan pernah bisa diinstal secara manual** dengan cara diklik langsung di file manager HP petugas. 
*   Memaksa `"buildType": "apk"` menjamin output kompilasi berupa file APK mandiri biasa yang bisa dengan bebas dikirim via WhatsApp Group PMI dan diinstal instan oleh para kurir lapangan untuk dites kelayakannya.

### Jawaban 3: APK vs AAB
*   Profil **Preview** menghasilkan file **`.apk`** (bisa diinstal langsung di HP manapun). 
*   Profil **Production** menghasilkan file **`.aab`** (Android App Bundle - hanya bisa dibaca oleh sistem internal konsol Google Play Store untuk didistribusikan ke publik).
```
[FASE 5: HARI 84 SUKSES]
```
