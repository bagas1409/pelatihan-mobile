# Hari 23: Kustomisasi Bar Navigasi (Custom Layouts)
## Mendesain Header Bar Berwarna Merah Khas PMI Lengkap Dengan Logo Instansi & Tombol Aksi Kustom

Selamat datang di Hari 23! Hari ini kita akan mempelajari cara merancang **Bar Navigasi Kustom** (Custom Header Layouts) di Expo Router.

Secara default, Stack Navigator menggambar bar navigasi atas menggunakan desain standar bawaan sistem operasi handphone yang terlihat monoton. Pada proyek aplikasi mobile DonorKu PMI Lampung, kita harus menyajikan identitas visual instansi yang premium: mewarnai bar navigasi dengan warna merah khas PMI, menggunakan font kustom, serta menyisipkan tombol aksi interaktif (seperti tombol lonceng notifikasi) langsung di dalam bar navigasi atas tersebut.

---

## 1. Properti Konfigurasi Kustom Header

Kita bisa melompati visual default dengan memanfaatkan opsi rute `options` di file `_layout.tsx` atau langsung di dalam masing-masing berkas halaman menggunakan komponen `<Stack.Screen />`:
*   **`headerStyle`**: Mengatur background container bar (warna, bayangan shadow).
*   **`headerTitle`**: Mengatur teks judul tengah atau bisa diganti menggunakan komponen gambar/ikon.
*   **`headerRight`**: Fungsi callback untuk menggambar tombol/ikon kustom di pojok kanan atas bar.
*   **`headerLeft`**: Fungsi callback untuk menggambar tombol kustom di pojok kiri atas (biasanya menggantikan panah back bawaan).

---

## 2. Studi Kasus PMI: Header Dashboard Bernuansa Merah PMI

Mari kita pasang kustomisasi header ini langsung di dalam file rute utama kita di **`app/index.tsx`**:

```tsx
// app/index.tsx
import React from 'react';
import { View, Text, TouchableOpacity, Alert } from 'react-native';
import { Stack } from 'expo-router';

export default function HomeDashboardDinas() {
  return (
    <View className="flex-1 bg-slate-50 p-6 justify-center items-center">
      
      <!-- 
        1. KONTROL HEADER BAR LANGSUNG DARI DALAM HALAMAN
        Ini melompati konfigurasi layout global khusus untuk halaman ini saja.
      -->
      <Stack.Screen
        options={{
          headerShown: true, // Pastikan header menyala
          title: 'DonorKu Lampung', // Judul text utama
          headerStyle: {
            backgroundColor: '#ef4444', // Warna Merah PMI
          },
          headerTintColor: '#ffffff', // Warna teks putih
          headerTitleStyle: {
            fontFamily: 'Outfit-Bold', // Font kustom (Hari 14)
            fontSize: 16,
          },
          // 2. SISIPKAN TOMBOL KUSTOM DI KANAN ATAS HEADER BAR
          headerRight: () => (
            <TouchableOpacity 
              onPress={() => Alert.alert('Peringatan', 'Tidak ada panggilan CITO darurat aktif.')}
              className="p-2 bg-white/10 rounded-full"
            >
              <Text className="text-white text-xs">🔔</Text>
            </TouchableOpacity>
          ),
        }}
      />

      <!-- Area Konten Halaman -->
      <Text className="text-slate-800 font-extrabold text-sm">Selamat Datang Petugas UDD</Text>
      <Text className="text-slate-400 text-xs mt-1 text-center">
        Header bar atas disesuaikan dengan standar desain PMI Lampung.
      </Text>

    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Amati tag `<Stack.Screen />` yang ditanamkan langsung di dalam file `app/index.tsx` di atas.
2. Jelaskan keuntungan menggunakan komponen `<Stack.Screen />` di dalam masing-masing file halaman untuk mengontrol opsi header, dibandingkan kita menuliskan semua opsi rute di file global `app/_layout.tsx`.
3. Sebutkan nama properti di dalam `options` yang bertugas menggambar komponen di pojok kanan atas header bar.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Keunggulan Sentralisasi Opsi per Halaman (Page-level Options)
*   **Kebebasan Desain**: Menulis opsi header di dalam file halaman memberikan kebebasan kustomisasi penuh yang terisolasi. 
*   Halaman Beranda bisa memiliki tombol lonceng `🔔` di kanan atas, sedangkan Halaman Edit bisa memiliki tombol simpan centang `✓`, dan Halaman Detil memiliki tombol share `📤`. Jika semua dipaksakan ditulis di `_layout.tsx`, file layout global akan dipenuhi oleh puluhan percabangan logika `if-else` rute yang sangat membingungkan.

### Jawaban 2: Properti headerRight
*   Properti tersebut bernama **`headerRight`** yang menerima nilai berupa fungsi callback yang mengembalikan elemen JSX React.
```
[FASE 2: HARI 23 SUKSES]
```
