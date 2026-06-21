# Hari 89: Variabel Lingkungan & Keamanan API Key (.env)
## Menyembunyikan URL Server API & API Key Sensitif Dari Script Kode Menggunakan Library expo-constants

Selamat datang di Hari 89! Hari ini kita akan mempelajari topik keamanan kode sumber (Source Code Security): **Variabel Lingkungan (Environment Variables)**.

Selama proses latihan, kita sering menulis langsung (*Hardcode*) alamat IP server backend kita ke dalam file Axios: `baseURL: 'http://10.0.2.2:5000'`. 
Bahayanya: jika suatu saat kita mengunggah kode proyek kita ke repository publik (seperti GitHub), URL server rahasia PMI Lampung beserta kunci API Key pihak ketiga (seperti API Key Google Maps) akan bocor dan bisa dimanfaatkan oleh peretas untuk merusak sistem backend. Kita harus menyembunyikannya menggunakan file **`.env`**.

---

## 1. Konsep Variabel Lingkungan di Expo

Di lingkungan Expo modern (Expo SDK 49+), dukungan file `.env` sudah terpasang secara bawaan tanpa memerlukan library tambahan.
*   Kita membuat file **`.env`** di folder root proyek untuk menampung variabel rahasia.
*   Kita memanggil variabel tersebut di dalam kode menggunakan objek **`process.env.NAMA_VARIABEL`**.

---

## 2. Studi Kasus PMI: Menyembunyikan URL Endpoint API

Mari kita buat file **`.env`** di direktori root proyek mobile Anda:

```env
# .env (Letakkan di folder root)
# PENTING: Nama variabel wajib diawali dengan EXPO_PUBLIC_ agar diekspor ke aplikasi mobile!
EXPO_PUBLIC_API_URL=http://10.0.2.2:5000
EXPO_PUBLIC_GOOGLE_MAPS_KEY=AIzaSyA123456789_SecretMapKeyPMI
```

Kemudian, buka file konfigurasi Axios terpusat Anda di **`utils/api.ts`** dan modifikasi pemanggilan URL asalnya:

```typescript
// utils/api.ts
import axios from 'axios';

// Ambil nilai URL server dari variabel lingkungan secara aman!
const BASE_URL_SERVER = process.env.EXPO_PUBLIC_API_URL || 'http://localhost:5000';

console.log("[Axios Init] Menyambungkan base URL ke:", BASE_URL_SERVER);

const api = axios.create({
  baseURL: BASE_URL_SERVER,
  timeout: 10000,
});

export default api;
```

*Tambahkan baris `.env` ke dalam file `.gitignore` agar file rahasia ini tidak pernah sengaja ter-upload ke github publik.*

---

## 3. Latihan Soal Mandiri
1. Mengapa nama variabel yang kita buat di dalam file `.env` di atas wajib diawali dengan awalan kata khusus **`EXPO_PUBLIC_`**? Apa efek keamanannya jika kita hanya menuliskannya sebagai `API_URL`?
2. Buka kode penulisan Axios `utils/api.ts` di atas. Jelaskan fungsi dari penggunaan operator logika OR fallback `|| 'http://localhost:5000'` di baris pembaca variabel lingkungan.
3. Sebutkan nama file penyaring keamanan git yang wajib kita isi dengan tulisan `.env` agar file konfigurasi lokal kita tidak ikut ter-upload bocor ke github umum.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Gerbang Keamanan Bundler (Expo Bundler Filter)
*   **Filter Bundling**: Mesin kompilator Expo menyaring seluruh variabel sistem komputer developer demi alasan keamanan. 
*   Hanya variabel yang sengaja ditandai dengan prefix **`EXPO_PUBLIC_`** saja yang akan diizinkan lolos disuntikkan ke dalam file JavaScript APK HP petugas. 
*   Jika ditulis `API_URL` biasa, sistem Expo bundler akan membisu dan menolak memuatnya, menyebabkan nilai properti bernilai `undefined` saat dijalankan di HP.

### Jawaban 2: Penyelamat Error Nilai Kosong (Fallback Value)
*   **Penyedia Nilai Cadangan**: Jika suatu saat developer lupa membuat file `.env` di laptop baru mereka, variabel `process.env.EXPO_PUBLIC_API_URL` akan mengembalikan nilai kosong (`undefined`). 
*   Penggunaan operator `||` menjamin Axios tetap memiliki alamat cadangan baku (`localhost`) untuk mencegah aplikasi melempar error *Crash* kegagalan pembacaan variabel string.

### Jawaban 3: File .gitignore
*   Berkas filter tersebut bernama **`.gitignore`**.
```
[FASE 5: HARI 89 SUKSES]
```
