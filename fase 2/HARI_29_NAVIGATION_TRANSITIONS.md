# Hari 29: Animasi Transisi Navigasi
## Mengatur Jenis Transisi Layar Slide, Fade, & Pop Menggunakan Konsep Screen Options Stack Router

Selamat datang di Hari 29! Hari ini kita akan mempelajari cara mengatur **Animasi Transisi Halaman** (Page Transition Animations) di Stack Router.

Desain antarmuka premium tidak membiarkan perpindahan layar terjadi secara kasar seketika (*Instant cut*). Handphone modern (Android/iOS) memiliki standar estetika transisi visual tersendiri (seperti efek geser halus dari kanan ke kiri). Kita akan mempelajari bagaimana cara mengonfigurasi efek animasi geser (**slide**), memudar (**fade**), maupun memantul (**pop**) untuk memperkuat kemewahan visual portal DonorKu PMI Lampung.

---

## 1. Tiga Pilihan Efek Animasi Utama (`animation` Options)

Di Expo Router Stack, kita bisa memodifikasi transisi menggunakan properti **`animation`** di dalam konfigurasi `<Stack.Screen />`:

*   **`'slide_from_right'` (Default Android/iOS)**: Layar baru meluncur mendatar dari kanan ke kiri menutupi layar lama secara anggun.
*   **`'fade'`**: Layar lama memudar secara perlahan dan digantikan layar baru yang menguat visualnya (sangat cocok untuk transisi splashscreen ke login).
*   **`'slide_from_bottom'` / `'fade_from_bottom'`**: Layar meluncur vertikal dari bawah ke atas (alternatif ringan untuk modal).
*   **`'none'`**: Mematikan seluruh animasi, perpindahan halaman terjadi secara instan tanpa transisi (berguna untuk optimasi HP kentang / low-end).

---

## 2. Studi Kasus PMI: Konfigurasi Transisi Khusus Menu CITO Urgent

Kita ingin halaman detail CITO urgent dibuka dengan animasi memudar (`fade`) yang tenang, sedangkan halaman form edit dibuka dengan geseran dari bawah.

Buka file konfigurasi **`app/_layout.tsx`** dan pasang konfigurasi animasinya:

```tsx
// app/_layout.tsx
import React from 'react';
import { Stack } from 'expo-router';

export default function LayoutDinasAnimasi() {
  return (
    <Stack
      screenOptions={{
        headerStyle: { backgroundColor: '#ffffff' },
        headerTintColor: '#0f172a',
        // Set animasi default seluruh halaman menjadi slide dari kanan
        animation: 'slide_from_right', 
        animationDuration: 300, // Durasi animasi (300 milidetik / 0.3 detik)
      }}
    >
      <!-- HALAMAN UTAMA DASHBOARD -->
      <Stack.Screen name="index" options={{ title: 'Beranda' }} />

      <!-- 
        HALAMAN DETAIL CITO URGENT
        Menggunakan animasi FADE (memudar) agar visual terkesan dramatis & tenang
      -->
      <Stack.Screen 
        name="cito-detail" 
        options={{ 
          title: 'Detail Panggilan Darurat',
          animation: 'fade' // Melompati slide_from_right global!
        }} 
      />

      <!-- 
        HALAMAN EDIT DATA RELAWAN
        Menggunakan animasi SLIDE DARI BAWAH
      -->
      <Stack.Screen 
        name="edit-relawan" 
        options={{ 
          title: 'Edit Informasi Relawan',
          animation: 'slide_from_bottom' 
        }} 
      />

    </Stack>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari parameter `animationDuration: 300` pada konfigurasi Stack Router di atas?
2. Jelaskan dalam situasi apa kita disarankan menggunakan efek transisi `animation: 'fade'` dibandingkan menggunakan efek geser samping `slide_from_right`.
3. Mengapa untuk HP dengan spesifikasi hardware kelas bawah (low-end/kentang), opsi `animation: 'none'` terkadang dipilih oleh tim developer?

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Pengaturan Kecepatan Transisi
*   **Kecepatan Gerak**: Parameter `animationDuration` (dalam satuan milidetik) menentukan seberapa lama durasi waktu yang dibutuhkan oleh halaman baru untuk bergerak dari titik awal hingga terpasang penuh di layar HP. 
*   Nilai `300` milidetik (0.3 detik) dianggap sebagai angka emas *User Experience* karena tidak terlalu lambat (membuat bosan) dan tidak terlalu cepat (mengagetkan mata).

### Jawaban 2: Pemilihan Efek Fade (Memudar)
*   **Ketenangan Visual**: Efek `fade` sangat cocok digunakan untuk alur perpindahan halaman yang bersifat pasif, rahasia, atau transisi pembuka awal. 
*   Contohnya adalah saat aplikasi baru pertama kali dibuka, layar gambar Splashscreen memudar menghilang secara halus digantikan layar Login, memberikan kesan transisi aplikasi yang elegan dan profesional.

### Jawaban 3: Optimasi HP Kentang (Jank Reduction)
*   **Mengurangi Beban GPU**: Menggambar animasi slide membutuhkan kalkulasi grafis yang tinggi pada GPU smartphone pada laju 60 frame per detik (FPS). 
*   Pada HP spesifikasi rendah dengan GPU lambat, animasi ini seringkali patah-patah (*Jank/Stutter*). Mengatur `animation: 'none'` mematikan kalkulasi grafis tersebut, membuat perpindahan halaman terjadi instan tanpa lag di HP spesifikasi rendah.
```
[FASE 2: HARI 29 SUKSES]
```
