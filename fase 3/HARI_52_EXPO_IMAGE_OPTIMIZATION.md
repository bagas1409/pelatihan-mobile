# Hari 52: Optimasi Gambar Lanjut dengan Expo Image
## Mengurangi Konsumsi Bandwidth Wi-Fi HP Menggunakan Caching Dan Format WebP Pada Komponen expo-image

Selamat datang di Hari 52! Hari ini kita akan mempelajari teknik optimasi performa aset visual menggunakan pustaka **`expo-image`**.

Di Fase 4 (Hari 74) kita belajar mengompres gambar menjadi WebP di web. Di handphone mobile, memuat gambar avatar relawan berukuran besar dari internet secara berulang-ulang menggunakan tag `<Image>` bawaan React Native akan **menghabiskan kuota data internet petugas** dan membuat proses scroll list terganggu patah-patah karena HP terus men-download ulang gambar yang sama. 

Hari ini kita akan memasang komponen alternatif terbaik kelas dunia buatan tim Expo yang memiliki fitur **caching otomatis di memori internal HP**.

---

## 1. Keunggulan `expo-image` Dibandingkan Image Biasa

1.  **Tembolok Pintar (Automatic Disk & Memory Caching)**: Gambar yang sudah di-download sekali akan disimpan di memori HP. Pembukaan kedua kali akan termuat instan dalam 0 milidetik tanpa kuota internet.
2.  **Gambar Pembuka Transisi (BlurHash Support)**: Menampilkan gambar bayangan pecah-blur (*BlurHash*) selama gambar asli sedang di-download, memberikan efek loading transisi yang sangat premium.
3.  **Animasi Halus**: Transisi dari gambar blur ke gambar tajam dimuat dengan efek memudar (*Cross-fade*) yang memanjakan mata petugas.

---

## 2. Instalasi Library expo-image

Jalankan perintah penginstalan berikut di terminal:
```bash
npx expo install expo-image
```

---

## 3. Studi Kasus PMI: Grid Gallery Foto Relawan dengan Caching Instan

Mari kita pasang komponen ini untuk merender foto KTP/Profil relawan secara optimal di berkas **`components/AvatarRelawanSafe.tsx`**:

```tsx
// components/AvatarRelawanSafe.tsx
import React from 'react';
import { View, Text } from 'react-native';
// Impor Image dari expo-image, bukan dari react-native!
import { Image } from 'expo-image';

export default function AvatarRelawanSafe() {
  const URL_AVATAR = 'https://images.unsplash.com/photo-1534528741775-53994a69daeb?w=150';
  
  // Blurhash: Representasi string teks mikro dari gambar blur yang sangat ringan
  const BLUR_HASH_HOLDER = 'LKN]~^%2_3t7_4RjMxof%MxuRjof';

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-100 items-center space-y-3 shadow-sm">
      <Text className="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Foto Anggota Terverifikasi</Text>

      {/* 
        EKSEKUSI EXPO-IMAGE
        cachePolicy="disk" -> Kunci dan simpan gambar di harddisk lokal HP petugas!
      */}
      <Image
        source={URL_AVATAR}
        placeholder={BLUR_HASH_HOLDER} // Tampilkan blur-hash jika internet lambat
        contentFit="cover" // Penyesuaian dimensi gambar (menggantikan resizeMode)
        transition={500} // Durasi animasi cross-fade memudar (500 milidetik)
        cachePolicy="disk" // Pilihan cache: 'disk' | 'memory' | 'none'
        className="w-24 h-24 rounded-full border-2 border-red-500"
      />

      <Text className="text-xs font-semibold text-slate-800">Siti Rahma - UDD Kedaton</Text>
    </View>
  );
}
```

---

## 4. Latihan Soal Mandiri
1. Apa kegunaan utama dari properti `cachePolicy="disk"` pada komponen `Image` expo-image di atas?
2. Buka kode `AvatarRelawanSafe.tsx`. Jelaskan fungsi dari properti `placeholder` yang diisi string `BLUR_HASH_HOLDER` tersebut bagi keindahan UX.
3. Sebutkan nama properti di dalam `expo-image` yang menggantikan fungsi `resizeMode="cover"` pada komponen `Image` React Native klasik.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Penghematan Bandwidth (Offline Caching)
*   **Penyimpanan Disk**: Opsi `cachePolicy="disk"` memerintahkan sistem untuk mengunci data biner file gambar di dalam media penyimpanan fisik HP (*Persistent Storage*). 
*   Saat petugas membuka kembali halaman profil di hari esok, HP tidak perlu lagi melakukan HTTP request menembak server internet, menghemat baterai HP, kuota internet petugas, dan menghadirkan kecepatan muat gambar secara instan.

### Jawaban 2: Penahan Visual Halus (Visual Placeholding)
*   **BlurHash**: String `BLUR_HASH_HOLDER` adalah representasi visual ringkas dari foto asli yang berukuran kurang dari 50 byte. 
*   Expo Image merender gambar kabur premium ini secara instan di detik pertama, lalu menimpanya dengan efek memudar saat file gambar asli yang berukuran kilobyte sukses selesai diunduh, mematikan lompatan putih kasar saat loading gambar.

### Jawaban 3: Properti contentFit
*   Properti tersebut bernama **`contentFit`** (berisi nilai `'cover' | 'contain' | 'fill'`).
```
[FASE 3: HARI 52 SUKSES]
```
