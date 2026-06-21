# Hari 82: Menangani Penerimaan Notifikasi
## Bereaksi Saat Notifikasi Diklik Dan Membaca Data Payload Untuk Navigasi Rute Halaman Secara Otomatis

Selamat datang di Hari 82! Kemarin kita sukses menembakkan alarm CITO dari backend Express.js. Hari ini kita akan kembali ke aplikasi mobile untuk merakit penerima alarm tersebut: **Notification Listeners**.

Kita harus merancang agar ketika relawan mengklik banner notifikasi CITO yang menyembul di atas layar HP mereka, aplikasi secara pintar mendeteksi data kargo rahasia `ruteTujuan` yang kita selipkan kemarin, lalu langsung **mengarahkan navigasi layar ke detail pasien kritis tersebut** secara otomatis.

---

## 1. Dua Macam Bentuk Notification Listener

Modul `expo-notifications` menyediakan dua sensor pendengar reaktif:
1.  **`useLastNotificationResponse`**: Sensor pendengar yang dipicu khusus **saat pengguna mengetuk/mengklik** banner notifikasi.
2.  **`addNotificationReceivedListener`**: Pendengar yang dipicu **saat notifikasi mendarat di HP** ketika aplikasi sedang dalam keadaan terbuka aktif dipegang tangan petugas (*Foreground State*).

---

## 2. Studi Kasus PMI: Auto Navigasi Rute CITO (`src/app/_layout.tsx (atau app/_layout.tsx)`)

Buka file layout utama global Anda di **`src/app/_layout.tsx (atau app/_layout.tsx)`** dan pasang listener penjinak notifikasi berikut di dalam `useEffect`:

```tsx
// src/app/_layout.tsx (atau app/_layout.tsx)
import React, { useEffect } from 'react';
import { Stack, router } from 'expo-router';
import * as Notifications from 'expo-notifications';

// Konfigurasi bagaimana notifikasi digambar saat aplikasi sedang terbuka aktif (Foreground)
Notifications.setNotificationHandler({
  handleNotification: async () => ({
    shouldShowAlert: true, // Tetap munculkan banner meskipun petugas sedang asyik membuka dashboard
    shouldPlaySound: true,
    shouldBadge: false,
  }),
});

export default function LayoutNotifikasiListener() {
  
  // 1. TANGKAP RESPONS KETUKAN NOTIFIKASI PENGGUNA
  const responTerakhir = Notifications.useLastNotificationResponse();

  useEffect(() => {
    // Cek apakah ada aksi klik notifikasi yang valid masuk?
    if (
      responTerakhir &&
      responTerakhir.actionIdentifier === Notifications.DEFAULT_ACTION_IDENTIFIER
    ) {
      // 2. EKSTRAK KARGO DATA RAHASIA (DIKIRIM BACKEND DI HARI 81)
      const dataNotif = responTerakhir.notification.request.content.data;
      
      console.log("[Notification Listener] Notifikasi diklik! Data Kargo:", dataNotif);

      // 3. JALANKAN LOGIKA NAVIGASI RUTE OTOMATIS
      if (dataNotif && dataNotif.ruteTujuan) {
        console.log(`[Notification Redirect] Melompat ke halaman: ${dataNotif.ruteTujuan}`);
        
        // Alihkan paksa layar ke detail stok kritis
        router.push(dataNotif.ruteTujuan); // Contoh melompat ke /stok/detail/30
      }
    }
  }, [responTerakhir]); // Memicu re-run useEffect setiap kali ada respon klik notifikasi baru!

  return (
    <Stack>
      <Stack.Screen name="(tabs)" options={{ headerShown: false }} />
    </Stack>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari konfigurasi global `Notifications.setNotificationHandler` pada baris awal kode di atas?
2. Buka kode blok pembaca kargo: `responTerakhir.notification.request.content.data`. Bagaimana alur pemindahan parameter rute URL tersebut ke parameter fungsi navigasi `router.push()`?
3. Mengapa kita memasang variabel `responTerakhir` ke dalam array dependensi `[responTerakhir]` pada fungsi React `useEffect`?

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Pengendali Notifikasi Aktif (Foreground Handler)
*   **Kebebasan Rendering**: Secara default di Android/iOS, jika sebuah notifikasi masuk saat aplikasi sedang dibuka aktif dimainkan tangan user, notifikasi tersebut akan disembunyikan membisu agar tidak mengganggu. 
*   Memanggil handler dengan properti `shouldShowAlert: true` memaksa banner notifikasi tersebut tetap menyembul turun bersuara di atas kepala petugas agar mereka tetap waspada jika ada laporan darah CITO darurat yang baru masuk dari server backend.

### Jawaban 2: Parsing Data Kargo Dinamis
*   Objek JSON kargo data rahasia dari server Express.js di Hari 81 (`{ ruteTujuan: 'stok/detail/30' }`) dibaca oleh library dan diumpankan ke properti `.data`. 
*   Kita mengecek eksistensi properti `ruteTujuan` tersebut. Jika bernilai truthy, kita mengalirkannya langsung menjadi parameter rute alamat tujuan di dalam fungsi navigasi `router.push(dataNotif.ruteTujuan)`, memicu navigasi layar instan.

### Jawaban 3: Pemicu Efek Reaktif (Dependency Array)
*   Memasukkan `responTerakhir` ke array dependensi memastikan bahwa setiap kali petugas mengklik banner notifikasi yang berbeda di luar aplikasi, React akan menangkap perubahan objek respon tersebut dan langsung mengeksekusi ulang blok fungsi navigasi di dalam `useEffect`.
```
[FASE 5: HARI 82 SUKSES]
```
