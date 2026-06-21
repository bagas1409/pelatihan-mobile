# Hari 14: Integrasi Google Fonts Kustom
## Mengunduh & Menerapkan Font Premium Inter Serta Outfit Menggunakan Library expo-font & expo-splash-screen

Selamat datang di Hari 14! Hari ini kita akan mempelajari cara mengganti jenis huruf (Font Family) bawaan sistem Android/iOS yang terkesan standar dengan font Google Fonts premium (**Inter** & **Outfit**) menggunakan modul **`expo-font`**.

Kita juga akan berkoordinasi dengan modul **`expo-splash-screen`** guna menahan layar splash screen pembuka tetap menyala selama proses pengunduhan file font sedang berlangsung di memori RAM HP, mencegah terjadinya lompatan perubahan bentuk tulisan (*Font Flickering*) saat aplikasi terbuka.

---

## 1. Langkah Instalasi Library Font

Buka terminal proyek Anda dan ketikkan perintah penginstalan berikut:
```bash
npx expo install expo-font expo-splash-screen
```

---

## 2. Studi Kasus PMI: Memasang Font di File Layout Global (`app/_layout.tsx`)

Buka berkas pengatur layout utama Anda di **`app/_layout.tsx`** (atau `App.tsx` jika menggunakan struktur non-router), lalu sesuaikan kodenya untuk mengimpor dan memuat font secara aman sebelum menggambar halaman.

```tsx
// app/_layout.tsx
import React, { useEffect } from 'react';
import { Stack } from 'expo-router';
import * as SplashScreen from 'expo-splash-screen';
import { useFonts } from 'expo-font';
import { Text } from 'react-native';

// 1. CEGAH SPLASH SCREEN MENUTUP OTOMATIS DI AWAL
SplashScreen.preventAutoHideAsync();

export default function LayoutUtamaGlobal() {
  
  // 2. KONTROL PENGUNDUHAN FILE FONT
  const [fontsLoaded, error] = useFonts({
    'Outfit-Bold': require('../assets/fonts/Outfit-Bold.ttf'),
    'Outfit-Medium': require('../assets/fonts/Outfit-Medium.ttf'),
    'Inter-Regular': require('../assets/fonts/Inter-Regular.ttf'),
  });

  // 3. SELESAI LOAD -> SEMBUNYIKAN SPLASH SCREEN
  useEffect(() => {
    if (fontsLoaded || error) {
      SplashScreen.hideAsync(); // Matikan layar loading awal, tampilkan UI asli!
    }
  }, [fontsLoaded, error]);

  // Jika font belum siap diunduh, jangan gambar layar dulu (tahan layar putih/splash)
  if (!fontsLoaded && !error) {
    return null; 
  }

  return (
    <Stack>
      <Stack.Screen name="index" options={{ headerShown: false }} />
    </Stack>
  );
}
```

---

## 3. Menerapkan Font di Halaman JSX

Setelah font terdaftar di tingkat layout global, Anda tinggal memanggil namanya di properti style CSS:

```tsx
// Contoh pemanggilan di Halaman Beranda
<Text style={{ fontFamily: 'Outfit-Bold' }} className="text-xl text-slate-800">
  DonorKu Lampung
</Text>
<Text style={{ fontFamily: 'Inter-Regular' }} className="text-xs text-slate-400">
  Selamat bertugas, Petugas UDD PMI Lampung.
</Text>
```

---

## 4. Latihan Soal Mandiri
1. Amati kode: `SplashScreen.preventAutoHideAsync()` di atas.
2. Jelaskan fungsi dari perintah `preventAutoHideAsync()` tersebut dan apa akibat buruknya jika baris tersebut lupa kita aktifkan saat memuat font kustom yang berukuran besar.
3. Mengapa kita disarankan menyimpan file font kustom berformat TrueType Font (`.ttf`) di dalam folder `assets/fonts/` daripada mengandalkan pemanggilan file font jarak jauh (*Remote Font URL*) via CDN internet?

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kegunaan Penahan Splash Screen
*   **Fungsi `preventAutoHideAsync()`**: Memaksa sistem operasi HP untuk tetap menampilkan gambar splash screen (layar pembuka awal) dan melarangnya menutup sebelum kita memberikan perintah setuju.
*   **Akibat jika dihapus**: Splash screen akan menutup instan dalam waktu 100 milidetik. Karena file font membutuhkan waktu 500ms untuk dibaca dari harddisk HP ke RAM, pengguna akan melihat tulisan di layar HP mendadak **berubah/melompat bentuknya dari font standar robot Android menjadi font Outfit secara mendadak** (*Font Flash / Layout Shift*), merusak kehalusan visual aplikasi.

### Jawaban 3: Kestabilan Offline (Offline Capabilities)
*   **Bisa Dibuka Offline**: Aplikasi mobile harus tangguh saat tidak mendapatkan sinyal internet (misal saat petugas PMI sedang bertugas donor darah keliling di pelosok Lampung). 
*   Menyimpan file `.ttf` di dalam folder assets menjamin font **100% siap dimuat kapan saja tanpa kuota internet**. Sebaliknya, mengunduh via URL CDN akan membuat font gagal termuat saat HP kehilangan sinyal, mengembalikan visual teks ke font bawaan HP Android yang tidak teratur.
