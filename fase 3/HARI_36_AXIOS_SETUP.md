# Hari 36: Dasar Otorisasi HTTP - Setup Axios
## Mengonfigurasi Axios Instance Lengkap Dengan Base URL, Header JSON, Serta Batas Waktu Response (Timeout)

Selamat datang di Hari 36! Hari ini kita memulai **Fase 3** dari pelatihan mobile frontend.

Di fase ini, kita akan melangkah jauh ke luar sistem lokal handphone dan menghubungkan aplikasi DonorKu Mobile kita dengan database server backend Express.js asli yang sudah kita kembangkan di fase-fase sebelumnya. Kita akan menggunakan **Axios** sebagai pustaka pengirim permintaan HTTP (*HTTP Request Client*) karena fiturnya yang sangat lengkap (seperti interceptors dan pengubahan format JSON otomatis).

---

## 1. Instalasi Axios

Buka terminal di root folder proyek Anda, lalu jalankan perintah penginstalan berikut:
```bash
npm install axios
```

---

## 2. Mengapa Memakai Axios Instance?

*   **Tanpa Duplikasi Kode**: Dibandingkan menuliskan alamat URL server secara berulang-ulang di setiap file (seperti `axios.get('http://10.0.2.2:5000/api/stok')`), kita membuat satu konfigurasi pusat (**Axios Instance**) yang sudah merekam domain dasar API, timeout, dan format header. 
*   File halaman lain tinggal mengimpor instance ini dan memanggil rute pendeknya saja (seperti `api.get('/stok')`).

---

## 3. Studi Kasus PMI: Setup Axios Client Terpusat (`utils/api.ts`)

Buat file baru di direktori **`utils/api.ts`** dan susun konfigurasinya sebagai berikut:

```typescript
// utils/api.ts
import axios from 'axios';

// 1. BUAT INSTANCE AXIOS KHUSUS
const api = axios.create({
  // Base URL menembak ke server backend
  // Catatan: Alamat IP 10.0.2.2 digunakan khusus untuk Android Emulator agar bisa menembak localhost laptop Anda (Hari 38)
  baseURL: process.env.EXPO_PUBLIC_API_URL || 'http://10.0.2.2:5000/api',
  
  // Batalkan koneksi jika server tidak merespon dalam waktu 10 detik (10000ms)
  timeout: 10000, 
  
  // Format komunikasi data wajib JSON
  headers: {
    'Content-Type': 'application/json',
    'Accept': 'application/json',
  },
});

export default api;
```

---

## 4. Latihan Soal Mandiri
1. Apa fungsi dari parameter `timeout: 10000` pada konfigurasi Axios Instance di atas? Apa efeknya jika koneksi internet HP sangat lambat/padam?
2. Jelaskan kegunaan pemakaian properti `process.env.EXPO_PUBLIC_API_URL` di atas bagi kemudahan penggantian alamat server saat kita merilis aplikasi ke server awan asli.
3. Sebutkan nama file format standar pertukaran data yang dideklarasikan di bagian properti `headers` pada Axios Instance di atas.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Mengatur Batas Jeda Koneksi (Timeout Handler)
*   **Penyelamat UI**: Properti `timeout` bertugas menyetop paksa antrean proses download data jika server backend tidak memberikan respon balasan sama sekali dalam batas waktu yang ditentukan (10 detik). 
*   Jika tidak disetel timeout, saat sinyal HP petugas mati di tengah jalan, aplikasi akan terus memutar loading spinner selamanya (*Infinite Loading*), membuat layar HP membeku tidak bisa diklik. Dengan timeout, Axios akan melempar pesan error *"Request Timeout"* setelah 10 detik agar kita bisa menampilkan tombol kustom *"Coba Lagi"*.

### Jawaban 2: Keamanan Env Variables di Expo
*   **Env Variables**: Kata kunci `EXPO_PUBLIC_` adalah standar bawaan Expo untuk membaca variabel lingkungan dari file `.env`. 
*   Kita bisa mengarahkan URL ke localhost saat belajar di laptop, dan otomatis berubah menembak ke server hosting internet asli (seperti Render/VPS) saat kita memproduksi APK rilis tanpa perlu mengubah baris kode di file `api.ts`.

### Jawaban 3: Format JSON (JavaScript Object Notation)
*   Format tersebut adalah **JSON**, yang ditandai dengan deklarasi header `'Content-Type': 'application/json'`.
```
[FASE 3: HARI 36 SUKSES]
```
