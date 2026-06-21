# Hari 88: Setor Aplikasi ke Google Play Store (EAS Submit)
## Mengunggah Otomatis File Biner AAB Android App Bundle Dari Terminal Ke Dashboard Console Google Play Store Menggunakan EAS Submit

Selamat datang di Hari 88! Kita telah memiliki file biner produksi berformat **`.aab`** dari hasil build di Hari 85. Hari ini kita akan mempelajari cara menyetorkan berkas tersebut menuju dasbor Google Play Console secara instan menggunakan perintah otomatis **`eas submit`**.

Dengan metode ini, Anda tidak perlu lagi mendownload file `.aab` berukuran puluhan Megabyte ke harddisk laptop, lalu mengunggahnya secara lambat lewat antarmuka browser web. Server Expo Cloud yang akan mengirimkannya langsung ke server Google Play Store melalui jalur pipa *Server-to-Server Transfer*.

---

## 1. Persiapan Awal Kunci Akses (Google Service Account Key)

Google mewajibkan otorisasi bot pengirim otomatis. Kita membutuhkan file kredensial JSON dari Google Cloud Console:
1.  Buka **Google Cloud Platform Console** yang terikat dengan Play Console Anda.
2.  Buat **Service Account** baru dengan akses peran sebagai *Release Manager*.
3.  Unduh file kunci Service Account tersebut dalam format berkas **JSON Key** (simpan berkas ini di laptop Anda).

---

## 2. Mengisi Konfigurasi Submit di `eas.json`

Buka kembali berkas konfigurasi **`eas.json`** Anda di VS Code. Pastikan di dalam objek `"submit"` terisi jalur target file JSON Service Account Anda tersebut:

```json
{
  "cli": {
    "version": ">= 9.0.0"
  },
  "build": {
    ...
  },
  "submit": {
    "production": {
      "android": {
        // Tembak letak rute file Service Account JSON yang didownload dari Google
        "serviceAccountKeyPath": "./google-service-account-key.json",
        "track": "internal" // Jalur rilis: 'internal' (Uji coba tertutup) | 'production' (Publik)
      }
    }
  }
}
```

---

## 3. Eksekusi Perintah Pengiriman (Submit)

Jalankan perintah pengirim ini di terminal proyek Anda:

```bash
eas submit --platform android
```

Terminal akan otomatis membaca database build terakhir Anda di akun Expo, mengambil file `.aab` versi produksi terbaru yang nangkring di awan, menyuntikkan kunci JSON Service Account, lalu menembakkannya langsung masuk ke laci **Internal Testing Track** di dalam dasbor Google Play Console Anda. Anda cukup memantau dasbor web Google Play Console untuk melihat status pemrosesan file tersebut.

---

## 4. Latihan Soal Mandiri
1. Apa fungsi dari berkas **Google Service Account Key JSON** yang wajib diunduh dari Google Cloud Platform Console?
2. Buka konfigurasi `eas.json` bagian submit di atas. Apa perbedaan jalur rilis jika properti `"track"` diatur ke nilai `"internal"` dibandingkan jika diatur langsung ke `"production"`?
3. Sebutkan perintah terminal lengkap untuk melepaskan pengiriman biner aplikasi Android Anda menuju Play Store!

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Surat Izin Masuk Bot Pengirim (API Auth Token)
*   **Akses Otomatisasi**: Berkas JSON Service Account Key bertindak sebagai password bot khusus. 
*   Dengan file ini, server Expo Cloud mendapatkan restu izin resmi dari Google untuk menulis dan meng-upload file ke dalam dasbor developer Anda tanpa harus melewati proses ketik sandi manual/verifikasi SMS OTP HP Anda setiap saat.

### Jawaban 2: Jalur Uji Coba Tertutup vs Siap Pajang
*   **`internal`**: File biner mendarat di gerbang uji coba tertutup (*Internal Testing*). Aplikasi hanya bisa diunduh oleh 100 orang tim kurir internal PMI Lampung yang emailnya didaftarkan secara khusus untuk bahan evaluasi. 
*   **`production`**: File biner langsung mendarat di gerbang rilis publik. Begitu disetujui tim Google, aplikasi akan langsung nampak dicari oleh seluruh jutaan masyarakat Provinsi Lampung di kolom search Play Store.

### Jawaban 3: Perintah eas submit
*   Perintah terminal tersebut adalah **`eas submit --platform android`**.
```
[FASE 5: HARI 88 SUKSES]
```
