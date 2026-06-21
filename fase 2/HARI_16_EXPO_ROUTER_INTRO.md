# Hari 16: Pengenalan Expo Router
## Menguasai Sistem Routing Berbasis Folder app/ Modern Yang Fleksibel Dan Mudah Dimengerti

Selamat datang di Hari 16! Hari ini kita memulai **Fase 2** dari program pelatihan mobile frontend kita.

Di fase ini, kita akan mempelajari cara membuat banyak halaman dan berpindah antar layar (*Navigation*). Di React Native klasik, kita harus membuat konfigurasi navigasi yang rumit menggunakan library React Navigation. Di ekosistem Expo terbaru, kita menggunakan **Expo Router**, sebuah library routing revolusioner berbasis folder (**File-based Routing**) yang menyerupai cara kerja Next.js atau Nuxt 3 di web.

---

## 1. Filosofi Folder `app/` di Expo Router

Di Expo Router, setiap file yang diletakkan di dalam direktori **`app/`** (atau **`src/app/`** jika proyek menggunakan struktur folder `src` seperti proyek `belajar` kita) otomatis didaftarkan sebagai rute halaman aktif.
*   `src/src/app/index.tsx (atau app/index.tsx)` (atau `src/app/index.tsx (atau app/index.tsx)`) $\rightarrow$ Halaman utama awal (diakses di rute `/`).
*   `src/src/app/profil.tsx (atau app/profil.tsx)` (atau `src/app/profil.tsx (atau app/profil.tsx)`) $\rightarrow$ Halaman profil petugas (diakses di rute `/profil`).
*   `src/app/stok/index.tsx` (atau `app/stok/index.tsx`) $\rightarrow$ Halaman stok darah (diakses di rute `/stok`).

---

## 2. Cara Kerja Hubungan Link Menggunakan `<Link>`

Untuk membuat teks atau tombol yang bisa diklik untuk pindah halaman, Expo Router menyediakan komponen **`<Link>`** yang sangat mirip dengan `<NuxtLink>` di Nuxt 3.

Mari kita buat halaman index awal di `src/src/app/index.tsx (atau app/index.tsx)` (atau `src/app/index.tsx (atau app/index.tsx)`) yang memiliki link menuju halaman profil:

### Berkas Halaman: `src/src/app/index.tsx (atau app/index.tsx)` (atau `src/app/index.tsx (atau app/index.tsx)`)
```tsx
// src/src/app/index.tsx (atau app/index.tsx) (atau src/app/index.tsx (atau app/index.tsx))
import React from 'react';
import { View, Text } from 'react-native';
import { Link } from 'expo-router';

export default function HalamanUtama() {
  return (
    <View className="flex-1 bg-slate-50 justify-center items-center p-6 space-y-4">
      <Text className="text-xl font-black text-slate-800">📊 Portal UDD PMI Lampung</Text>
      <Text className="text-xs text-slate-400 text-center">
        Gunakan tombol di bawah untuk masuk ke menu profil dinas petugas.
      </Text>

      <!-- 
        Link bertindak sebagai navigasi tap rute ke /profil
        asChild -> Meneruskan kemampuan klik Link ke komponen anak Pressable di bawahnya
      -->
      <Link href="/profil" className="bg-red-500 px-6 py-3 rounded-2xl mt-4">
        <Text className="text-white font-extrabold text-xs uppercase tracking-wider">Buka Profil Dinas</Text>
      </Link>
    </View>
  );
}
```

---

### Berkas Halaman Tujuan: `src/src/app/profil.tsx (atau app/profil.tsx)` (atau `src/app/profil.tsx (atau app/profil.tsx)`)
```tsx
// src/src/app/profil.tsx (atau app/profil.tsx) (atau src/app/profil.tsx (atau app/profil.tsx))
import React from 'react';
import { View, Text } from 'react-native';
import { Link } from 'expo-router';

export default function HalamanProfil() {
  return (
    <View className="flex-1 bg-white justify-center items-center p-6 space-y-4">
      <Text className="text-base font-bold text-slate-800">👤 Profil Petugas UDD</Text>
      <Text className="text-xs text-slate-500">Nama: Ahmad Syarif | ID: 19823</Text>

      {/* Link kembali ke halaman utama / index */}
      <Link href="/" className="bg-slate-100 px-6 py-2.5 rounded-xl">
        <Text className="text-slate-600 font-bold text-xs">Kembali</Text>
      </Link>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa kelebihan sistem routing berbasis file (**File-based Routing**) di Expo Router dibandingkan sistem rute manual di React Navigation klasik?
2. Buka kode `src/src/app/index.tsx (atau app/index.tsx)` (atau `src/app/index.tsx (atau app/index.tsx)`). Jelaskan kegunaan properti `href="/profil"` pada tag `<Link>` di atas.
3. Apa yang terjadi jika kita membuat berkas file baru bernama `src/app/tentang.tsx` (atau `app/tentang.tsx`) di dalam direktori proyek? Sebutkan nama rute URL penemu layar tersebut.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Keunggulan File-based Routing
*   **Tanpa Konfigurasi Manual**: Pada React Navigation klasik, kita harus mengimpor komponen layar satu persatu, lalu mendaftarkannya secara manual di file `App.js` menggunakan komponen `<Stack.Navigator>` dan `<Stack.Screen>`. Jika jumlah halaman mencapai 50, file konfigurasi akan menjadi sangat panjang dan rawan error konflik merge git.
*   Expo Router otomatis mendaftarkan halaman baru berdasarkan susunan folder secara fisik, menyelaraskan proses development mobile dengan gaya modern framework web saat ini.

### Jawaban 2: Kegunaan Properti href
*   **Target Alamat Rute**: Properti `href` (hypertext reference) menentukan alamat layar tujuan yang ingin dibuka saat komponen diklik oleh jari pengguna.

### Jawaban 3: Peta Halaman Otomatis
*   Menambah file `src/app/tentang.tsx` (atau `app/tentang.tsx`) akan langsung dideteksi oleh compiler Metro.
*   Rute baru akan **tercipta secara otomatis di alamat `/tentang`** tanpa perlu merubah satu barispun kode di file `_layout.tsx` atau file konfigurasi lain.
