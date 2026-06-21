# Hari 56: Proteksi Rute (Route Guards)
## Membatasi Akses Halaman Dashboard Petugas Dan Mengarahkan Pengguna Non-Otorisasi Kembali Ke Layar Login

Selamat datang di Hari 56! Hari ini kita memulai **Fase 4** dari pelatihan mobile frontend.

Di fase ini, kita akan masuk ke area fungsional hardware native HP. Namun sebelum itu, kita harus memastikan gerbang keamanan rute halaman kita sudah tertutup rapat. Kita akan merancang sistem **Route Guards** (Proteksi Rute) memanfaatkan status `isAuthenticated` dari Zustand Auth Store (Hari 42). 

Jika terdeteksi pengguna liar mencoba mengakses halaman administratir khusus petugas (seperti dashboard stok darah), aplikasi harus segera mendeteksi ketiadaan token JWT, membatalkan rendering layar, dan melempar paksa pengguna tersebut kembali ke layar login awal.

---

## 1. Konsep Route Guard di Expo Router

Di web, kita terbiasa menggunakan middleware router. Di Expo Router, kita meletakkan logika pencegatan ini di file layout pembungkus utama **`src/app/_layout.tsx (atau app/_layout.tsx)`** menggunakan kombinasi React Hook `useEffect` dan hook navigasi `useSegments()`:
*   **`useSegments()`**: Mengembalikan array segmen rute URL aktif saat ini (contoh: jika user di halaman `/relawan/daftar`, segmennya adalah `['relawan', 'daftar']`).
*   **Logika Filter**: Jika user tidak berada di folder `login` dan status login `isAuthenticated` bernilai `false`, alihkan paksa ke `/login`.

---

## 2. Studi Kasus PMI: Kode Penjaga Rute di `src/app/_layout.tsx (atau app/_layout.tsx)`

Buka file layout utama global Anda di **`src/app/_layout.tsx (atau app/_layout.tsx)`** dan tanamkan fungsi pencegat otorisasi berikut:

```tsx
// src/app/_layout.tsx (atau app/_layout.tsx)
import React, { useEffect } from 'react';
import { Stack, useRouter, useSegments } from 'expo-router';
import useAuthStore from '../store/useAuthStore';

export default function LayoutPenjagaRute() {
  const { isAuthenticated } = useAuthStore();
  const segments = useSegments();
  const router = useRouter();

  useEffect(() => {
    // 1. CEK SEGMEN RUTE AKTIF SAAT INI
    // Apakah user sedang berada di dalam halaman grup otentikasi/login?
    const diHalamanLogin = segments[0] === 'login';

    console.log("[RouteGuard] Segmen URL Terdeteksi:", segments);
    console.log("[RouteGuard] Status Otorisasi Petugas:", isAuthenticated);

    // 2. JIKA BELUM LOGIN & MENCOBA MASUK KE DASHBOARD DINAS
    if (!isAuthenticated && !diHalamanLogin) {
      console.log("[RouteGuard] Akses Ilegal! Melempar user ke halaman login.");
      
      // Alihkan paksa rute ke gerbang login
      router.replace('/login');
    } 
    
    // 3. JIKA SUDAH LOGIN & MENCOBA KEMBALI KE FORM LOGIN KASAR
    else if (isAuthenticated && diHalamanLogin) {
      console.log("[RouteGuard] Sudah otorisasi. Alihkan langsung ke dashboard.");
      router.replace('/(tabs)');
    }

  }, [isAuthenticated, segments]); // Jalankan filter setiap kali status login atau segmen URL berubah!

  return (
    <Stack>
      {/* Daftarkan layar-layar rute aplikasi */}
      <Stack.Screen name="login" options={{ headerShown: false }} />
      <Stack.Screen name="(tabs)" options={{ headerShown: false }} />
    </Stack>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari pemanggilan hook `useSegments()` pada kode di atas?
2. Buka kode `useEffect` di atas. Jelaskan bahaya yang terjadi jika kita tidak menyertakan kondisi `!diHalamanLogin` di baris filter pencegatan (misal hanya menulis `if (!isAuthenticated) router.replace('/login')`). Apa efek loop yang akan melumpuhkan aplikasi?
3. Sebutkan nama hook navigasi yang kita gunakan untuk memicu pengalihan halaman secara terprogram di atas.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Pembaca Peta Koordinat URL (Rute Segments)
*   **Pembeda Wilayah**: Hook `useSegments()` memecah alamat rute URL aktif menjadi array string (contoh: `/stok/detail/12` diubah menjadi `['stok', 'detail', '12']`). 
*   Ini membantu program mendeteksi secara presisi di area folder folder mana user saat ini berada, guna menentukan apakah area tersebut membutuhkan proteksi token JWT atau merupakan area bebas token (seperti rute login atau pendaftaran publik).

### Jawaban 2: Bahaya Infinite Redirect Loop (Lingkaran Setan Navigasi)
*   **Loop Tanpa Henti**: Jika filter `!diHalamanLogin` dihilangkan, sistem akan mendeteksi user belum login (`isAuthenticated = false`), lalu menjalankan perintah `router.replace('/login')`. 
*   Setelah tiba di halaman login, segmen rute berubah menjadi `['login']`. Karena user di halaman login masih berstatus belum login, `useEffect` akan berjalan kembali, mendeteksi user belum login, dan menjalankan perintah `router.replace('/login')` lagi. 
*   Proses ini akan berulang miliaran kali per detik (*Infinite loop*), membuat processor HP panas, aplikasi membeku (*Freeze*), dan akhirnya mengalami crash internal.

### Jawaban 3: Hook useRouter
*   Hook tersebut bernama **`useRouter()`** (yang mengembalikan object router yang beranggotakan fungsi `replace`, `push`, dll).
```
[FASE 4: HARI 56 SUKSES]
```
