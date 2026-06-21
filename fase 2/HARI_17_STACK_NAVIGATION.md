# Hari 17: Navigasi Tumpukan (Stack Navigation)
## Memahami Cara Kerja Penumpukan Halaman Detail Di Atas Layar Utama Menggunakan Stack Navigator

Selamat datang di Hari 17! Hari ini kita akan mempelajari model navigasi terpenting di aplikasi mobile: **Stack Navigation** (Navigasi Tumpukan).

Bayangkan setumpuk piring di atas meja makan. Piring pertama ditaruh di bawah (layar utama). Saat piring kedua ditaruh di atasnya (layar detail), piring pertama tertutup namun tidak hilang. Saat piring kedua diambil kembali (tombol back ditekan), piring pertama langsung terlihat kembali. Ini adalah konsep navigasi **tumpukan kartu** yang wajib kita kuasai untuk membuat alur navigasi dari daftar stok darah masuk ke detail stok.

---

## 1. Mendaftarkan Stack Layout di `src/src/app/_layout.tsx (atau app/_layout.tsx)` (atau `src/app/_layout.tsx (atau app/_layout.tsx)`)

Untuk mengaktifkan model navigasi Stack, kita harus mendefinisikan pembungkus **`<Stack>`** di file layout global:

```tsx
// src/src/app/_layout.tsx (atau app/_layout.tsx) (atau src/app/_layout.tsx (atau app/_layout.tsx))
import React from 'react';
import { Stack } from 'expo-router';

export default function LayoutDinas() {
  return (
    // Stack bertindak sebagai pengatur tumpukan halaman
    <Stack
      screenOptions={{
        headerStyle: {
          backgroundColor: '#ef4444', // Mengatur warna dasar header bar PMI (merah)
        },
        headerTintColor: '#ffffff', // Warna teks judul & tombol back putih
        headerTitleStyle: {
          fontWeight: 'bold',
        },
      }}
    >
      <!-- Daftarkan rute index halaman utama -->
      <Stack.Screen 
        name="index" 
        options={{ title: 'Dashboard DonorKu' }} 
      />
      
      <!-- Daftarkan rute halaman detail -->
      <Stack.Screen 
        name="detail" 
        options={{ title: 'Rincian Logistik' }} 
      />
    </Stack>
  );
}
```

---

## 2. Pindah Halaman Menggunakan Link Rute Dinamis

Setelah `<Stack>` terpasang, kita bisa membuat link navigasi yang mendorong (*Push*) halaman baru ke atas tumpukan.

### Halaman Utama: `src/src/app/index.tsx (atau app/index.tsx)` (atau `src/app/index.tsx (atau app/index.tsx)`)
```tsx
// src/src/app/index.tsx (atau app/index.tsx) (atau src/app/index.tsx (atau app/index.tsx))
import React from 'react';
import { View, Text } from 'react-native';
import { Link } from 'expo-router';

export default function HomeDashboard() {
  return (
    <View className="flex-1 bg-slate-50 p-6 justify-center items-center space-y-4">
      <Text className="text-base font-bold text-slate-800">Dashboard Utama</Text>
      
      <Link href="/detail" className="bg-red-500 px-6 py-3 rounded-2xl">
        <Text className="text-white font-extrabold text-xs">Buka Rincian Logistik</Text>
      </Link>
    </View>
  );
}
```

---

### Halaman Detail: `src/src/app/detail.tsx (atau app/detail.tsx)` (atau `src/app/detail.tsx (atau app/detail.tsx)`)
Ketika Halaman detail dibuka, sistem operasi Android dan iOS akan otomatis **menggambar tombol panah kembali (Back Button)** di pojok kiri atas header bar tanpa perlu kita buat kodenya secara manual.

```tsx
// src/src/app/detail.tsx (atau app/detail.tsx) (atau src/app/detail.tsx (atau app/detail.tsx))
import React from 'react';
import { View, Text } from 'react-native';

export default function HalamanDetail() {
  return (
    <View className="flex-1 bg-white p-6 justify-center items-center">
      <Text className="text-sm font-bold text-slate-800">Detail Logistik Darah UDD</Text>
      <Text className="text-xs text-slate-400 mt-2 text-center">
        Stok aktif Golongan Darah AB saat ini: 12 Bag. Terpakai 2 Bag.
      </Text>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Amati bagaimana tombol panah kembali (Back Button) muncul di halaman `detail.tsx` secara otomatis.
2. Jelaskan konsep tumpukan (*Stack*) pada memori RAM handphone saat kita melakukan navigasi dari halaman `index` $\rightarrow$ `detail` $\rightarrow$ menekan tombol kembali.
3. Sebutkan parameter di dalam `options` pada `Stack.Screen` yang berfungsi untuk menyembunyikan header bar merah bagian atas secara total (misalnya untuk kebutuhan halaman Login polos).

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Kemudahan Bawaan Stack Navigator
*   **Tombol Back Otomatis**: Stack Navigator mendeteksi bahwa halaman `detail.tsx` berada di atas halaman `index.tsx` di dalam tumpukan. 
*   Navigator secara otomatis menyuntikkan tombol panah kiri beserta aksi `router.back()` bawaan sistem operasi agar pengguna bisa menutup halaman detail dengan mudah sesuai standar kebiasaan aplikasi mobile (*Native UX*).

### Jawaban 2: Siklus Memori Stack (Push & Pop)
*   **Push (Dorong)**: Saat berpindah ke `/detail`, instance halaman `index.tsx` ditahan dalam keadaan tidak aktif (hibernasi) di RAM, lalu `detail.tsx` dipasang di atasnya.
*   **Pop (Tarik Kembali)**: Saat tombol back ditekan, halaman `detail.tsx` **dihancurkan secara permanen dari memori RAM (di-destroy)**, lalu halaman di bawahnya (`index.tsx`) dibangunkan kembali ke status aktif tanpa memuat ulang state lokal, menghemat kapasitas penggunaan memori HP.

### Jawaban 3: Menyembunyikan Header
*   Gunakan parameter properti: **`headerShown: false`**.
*   Contoh: `<Stack.Screen name="login" options={{ headerShown: false }} />`.
