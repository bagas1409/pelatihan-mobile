# Hari 44: Otorisasi Lintas Rute - Interceptors
## Mengintegrasikan Axios Request Interceptor Untuk Menyuntikkan Token JWT Secara Otomatis Di Setiap Permintaan API

Selamat datang di Hari 44! Hari ini kita akan mempelajari cara merancang **Axios Interceptor** untuk menyuntikkan token JWT secara otomatis ke dalam header HTTP.

Di Hari 43 kita telah berhasil menyimpan token JWT secara aman di KeyStore HP menggunakan `SecureStore`. Namun, sangatlah melelahkan jika setiap kali kita ingin melakukan Axios request (misal: mengambil data, mengedit, menghapus), kita harus memanggil `SecureStore.getItemAsync` secara manual dan menyisipkannya ke parameter headers. 

Hari ini kita akan mengonfigurasi **Request Interceptor** agar bertindak sebagai *"Bea Cukai"* otomatis yang bertugas menempelkan cap stempel Token Bearer pada setiap paket request API yang keluar dari laptop/HP kita menuju server backend.

---

## 1. Cara Kerja Axios Interceptor

Axios Interceptor bekerja seperti filter air atau gerbang tol:
1.  **Request Interceptor**: Mencegat paket request tepat sebelum dilepaskan ke jaringan internet. Di sini kita mengambil token dari `SecureStore` dan menempelkannya di header `Authorization`.
2.  **Response Interceptor**: Mencegat paket respon yang baru mendarat dari server backend (misal: jika mendeteksi error HTTP 401 Unauthorized, otomatis memicu aksi logout petugas).

---

## 2. Studi Kasus PMI: Menyuntikkan Token Dinas Otomatis (`utils/api.ts`)

Buka kembali file konfigurasi Axios Anda di **`utils/api.ts`**, lalu modifikasi kodenya untuk menempelkan detektor request interceptor:

```typescript
// utils/api.ts
import axios from 'axios';
import * as SecureStore from 'expo-secure-store';

const api = axios.create({
  baseURL: process.env.EXPO_PUBLIC_API_URL || 'http://10.0.2.2:5000/api',
  timeout: 10000,
});

// 1. PASANG AXIOS REQUEST INTERCEPTOR
api.interceptors.request.use(
  async (config) => {
    try {
      // Ambil token JWT terenkripsi dari hardware HP (Hari 43)
      const token = await SecureStore.getItemAsync('donorku_jwt_token');

      // Jika token ditemukan di memori HP, tempelkan stempel Bearer Token!
      if (token) {
        config.headers['Authorization'] = `Bearer ${token}`;
        console.log("[Interceptor] Berhasil menyuntikkan Token JWT.");
      }
    } catch (error) {
      console.error("[Interceptor Error] Gagal membaca token:", error);
    }
    
    return config; // Lanjutkan perjalanan request ke server backend!
  },
  (error) => {
    return Promise.reject(error);
  }
);

export default api;
```

---

## 3. Latihan Soal Mandiri
1. Apa kegunaan utama dari penggunaan kata kunci `Bearer` di depan string token saat kita menyuntikkan data ke header `Authorization`?
2. Buka kode interceptor di atas. Mengapa kita wajib mengembalikan objek `config` menggunakan perintah `return config;` di akhir fungsi pencegatan? Jelaskan akibatnya jika baris tersebut kita hapus.
3. Sebutkan nama interceptor yang bertugas memeriksa kode status error HTTP dari server backend sebelum data diserahkan ke kode program client.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Standar Bearer Authentication (RFC 6750)
*   **Identifikasi Skema**: Kata `Bearer` (secara harfiah berarti "pembawa") memberi tahu server backend bahwa pengirim request adalah pembawa token otentikasi yang sah. 
*   Server backend tinggal mengekstrak string token di belakang kata "Bearer " untuk memproses verifikasi hak akses database.

### Jawaban 2: Kelancaran Jalur Paket Data (Request Pipeline)
*   **Penyelamat Aliran**: Objek `config` berisi seluruh detail parameter HTTP yang akan dikirim (URL target, data body, metode POST/GET). 
*   **Akibat jika dihapus**: Jika Anda lupa menulis `return config;`, Axios akan menganggap proses pencegatan menghasilkan nilai kosong (`undefined`). Akibatnya, request Anda **akan dibatalkan di tengah jalan dan tidak akan pernah terkirim ke server backend**, menyebabkan aplikasi Anda diam membeku tanpa melahirkan respon jaringan apapun.

### Jawaban 3: Response Interceptor
*   Interceptor tersebut bernama **`api.interceptors.response`**.
```
[FASE 3: HARI 44 SUKSES]
```
