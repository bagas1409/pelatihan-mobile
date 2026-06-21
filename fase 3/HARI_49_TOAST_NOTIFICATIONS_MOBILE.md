# Hari 49: Toast Notifikasi Melayang (Toast Messages)
## Memasang Library react-native-toast-message Untuk Tampilan Notifikasi Melayang Yang Animatif & Premium

Selamat datang di Hari 49! Hari ini kita akan mempelajari cara merancang **Toast Notifikasi Melayang** menggunakan library **`react-native-toast-message`**.

Menggunakan dialog pop-up `Alert.alert` (seperti di Hari 48) terkesan kaku dan menghentikan alur interaksi pengguna secara kasar karena memaksa mereka mengklik tombol "OK" sebelum bisa melanjutkan aktivitas. Di aplikasi mobile modern, notifikasi melayang kecil di bagian atas atau bawah layar (*Toast Message*) yang otomatis memudar hilang dalam 2 detik jauh lebih disukai karena tidak mengganggu fokus pengguna (*Non-intrusive UI*).

---

## 1. Instalasi Library Toast

Jalankan perintah penginstalan berikut di terminal proyek Anda:
```bash
npm install react-native-toast-message
```

---

## 2. Studi Kasus PMI: Memasang Container Toast di Layout Global (`app/_layout.tsx`)

Sama halnya dengan Portal Provider, library Toast membutuhkan satu komponen pembungkus induk yang ditempelkan di bagian paling bawah layout global agar notifikasi bisa dirender melayang di atas seluruh rute halaman:

```tsx
// app/_layout.tsx
import React from 'react';
import { Stack } from 'expo-router';
// Impor komponen Toast utama
import Toast from 'react-native-toast-message';

export default function LayoutDinasToast() {
  return (
    <>
      <Stack>
        <Stack.Screen name="index" options={{ headerShown: false }} />
      </Stack>
      
      {/* 
        TEMPELKAN CONTAINER TOAST DI KORIDOR ROOT TERLUAR
        Ini menjamin notifikasi bisa menyembul melayang menembus batas halaman!
      */}
      <Toast />
    </>
  );
}
```

---

## 3. Cara Memanggil Toast dari Dalam Kode Program

Kita bisa memanggil notifikasi dari file mana saja secara instan menggunakan static helper method `Toast.show()`:

```tsx
import Toast from 'react-native-toast-message';

// 1. TOAST SUKSES (Hijau)
Toast.show({
  type: 'success', // Jenis visual: 'success' | 'error' | 'info'
  text1: '✓ Laporan Terkirim',
  text2: 'Data logistik UDD PMI Kedaton telah disimpan.',
  position: 'top', // Posisi sembul: 'top' | 'bottom'
  visibilityTime: 2500, // Tayang selama 2.5 detik (2500ms)
});

// 2. TOAST ERROR (Merah)
Toast.show({
  type: 'error',
  text1: '🚨 Gagal Sinkronisasi',
  text2: 'Koneksi internet tidak stabil.',
  position: 'top',
});
```

---

## 4. Latihan Soal Mandiri
1. Apa keuntungan penggunaan notifikasi **Toast** dibandingkan popup **Alert.alert** dari segi kenyamanan pengguna (*User Experience*) saat sedang menginput data cepat di lapangan?
2. Buka kode `app/_layout.tsx` di atas. Jelaskan mengapa kita menaruh tag `<Toast />` di bagian luar dan bawah tag `<Stack>`, bukan di dalam Stack Screen.
3. Sebutkan parameter properti pada `Toast.show` yang bertugas menentukan berapa milidetik lama tayangnya notifikasi sebelum memudar menghilang sendiri.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Non-intrusive UI (Tidak Mengganggu Alur)
*   **Kebebasan Navigasi**: `Alert.alert` bersifat memblokir layar (*Modal Dialog*). Petugas tidak bisa menekan menu lain sebelum menekan tombol OK. 
*   `Toast` menyembul halus tanpa menutupi tombol lain. Petugas tetap bisa melanjutkan pengetikan atau scroll layar selagi notifikasi perlahan memudar hilang di atas kepala mereka, menjaga fluiditas kerja petugas.

### Jawaban 2: Kebebasan Koordinat Rendering (Overlay Stack)
*   **Posisi Teratas**: Meletakkan `<Toast />` di bawah tag `<Stack>` memastikan bahwa container notifikasi digambar di luar siklus pergerakan halaman. 
*   Jika ditaruh di dalam salah satu halaman Stack, ketika terjadi transisi slide rute halaman, notifikasi toast tersebut akan ikut tergeser keluar layar secara aneh. Penempatan di tingkat root global menjamin notifikasi melayang diam di atas transisi rute manapun.

### Jawaban 3: Properti visibilityTime
*   Properti tersebut bernama **`visibilityTime`** (bernilai angka milidetik).
```
[FASE 3: HARI 49 SUKSES]
```
