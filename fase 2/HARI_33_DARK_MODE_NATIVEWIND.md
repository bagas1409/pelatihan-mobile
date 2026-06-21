# Hari 33: Fitur Mode Gelap (Dark Mode)
## Memanfaatkan useColorScheme & Modifier dark: Pada NativeWind Untuk Tampilan Ramah Mata Malam Hari

Selamat datang di Hari 33! Hari ini kita akan mempelajari cara merancang **Mode Gelap** (Dark Mode) yang ramah mata petugas PMI saat bertugas jaga malam menggunakan hook **`useColorScheme`** dan modifier **`dark:`** pada NativeWind.

Petugas UDD PMI Lampung seringkali harus memproses panggilan CITO darurat di tengah malam dalam keadaan ruangan gelap. Layar HP yang terlalu terang putih akan menyiksa mata mereka. Kita akan mengonfigurasi aplikasi agar otomatis merubah skema warna layout menjadi hitam gelap jika sistem operasi handphone mereka diaktifkan mode malamnya oleh pengguna.

---

## 1. Bagaimana NativeWind Mengelola Mode Gelap?

NativeWind terintegrasi erat dengan mesin detektor tema bawaan React Native. Kita cukup menambahkan awalan modifier kelas **`dark:`** di depan kelas Tailwind biasa:
*   `bg-white dark:bg-slate-900` $\rightarrow$ Warna background putih di mode terang, dan otomatis berubah menjadi biru gelap di mode malam.
*   `text-slate-800 dark:text-slate-100` $\rightarrow$ Teks abu-abu gelap di mode terang, dan berubah menjadi putih abu-abu di mode malam.

---

## 2. Langkah Konfigurasi `tailwind.config.js`

Buka file `tailwind.config.js` di root folder proyek Anda, lalu daftarkan opsi deteksi mode malam berbasis class:

```javascript
// tailwind.config.js
module.exports = {
  content: [
    "./app/**/*.{js,jsx,ts,tsx}",
    "./components/**/*.{js,jsx,ts,tsx}",
    "./src/app/**/*.{js,jsx,ts,tsx}",
    "./src/components/**/*.{js,jsx,ts,tsx}"
  ],
  presets: [require("nativewind/preset")],
  darkMode: "class", // TAMBAHKAN BARIS INI (Menggunakan deteksi kelas untuk mode malam)
  theme: {
    extend: {},
  },
  plugins: [],
}
```

---

## 3. Studi Kasus PMI: Kartu Informasi Dinas Dukung Dark Mode

Mari buat komponen kartu info petugas yang responsif terhadap perubahan mode gelap malam hari:

```tsx
// src/components/KartuPetugasMalam.tsx (atau components/KartuPetugasMalam.tsx)
import React from 'react';
import { View, Text, useColorScheme } from 'react-native';

export default function KartuPetugasMalam() {
  // useColorScheme mengembalikan string 'light' atau 'dark' sesuai setelan OS HP saat ini
  const temaSistem = useColorScheme();

  return (
    // 1. CONTAINER RESPONSIF TEMA
    // bg-white (light mode) -> bg-slate-900 (dark mode)
    <View className="bg-white dark:bg-slate-900 p-6 rounded-3xl border border-slate-100 dark:border-slate-800 shadow-sm space-y-4">
      
      <View className="flex-row justify-between items-center">
        <Text className="text-slate-800 dark:text-slate-100 font-extrabold text-sm">👤 Petugas Jaga Malam</Text>
        
        {/* Lencana Indikator Status */}
        <View className="bg-red-50 dark:bg-red-950/40 px-2.5 py-1 rounded-full">
          <Text className="text-[10px] font-bold text-red-500">SHIFT 3</Text>
        </View>
      </View>

      <Text className="text-xs text-slate-500 dark:text-slate-400 leading-relaxed">
        Selamat bertugas di UDD PMI Kedaton. Saat ini handphone Anda sedang menggunakan tema:
        <Text className="font-bold text-red-500"> {temaSistem === 'dark' ? 'MODE GELAP 🌙' : 'MODE TERANG ☀️'}</Text>
      </Text>

    </View>
  );
}
```

---

## 4. Latihan Soal Mandiri
1. Apa fungsi dari pemanggilan hook `useColorScheme()` bawaan React Native di dalam kode di atas?
2. Buka berkas `tailwind.config.js`. Jelaskan mengapa kita menambahkan properti `darkMode: "class"` saat kita mengintegrasikan NativeWind.
3. Sebutkan utility class text yang tepat jika kita ingin membuat warna tulisan menjadi putih bersih khusus ketika mode malam aktif.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Detektor Sensor Tema OS
*   **Sensor Cahaya Layar**: Hook `useColorScheme()` mendengarkan setelan preferensi tampilan global sistem operasi handphone Android/iOS. 
*   Jika petugas menyalakan fitur "Dark Mode" di bilah menu notifikasi HP Android mereka, hook ini akan langsung menangkap perubahan tersebut secara reaktif dan mengembalikan nilai string `'dark'` agar kita bisa menyesuaikan visual UI.

### Jawaban 2: Penyelarasan Engine Tailwind
*   **Mode Deteksi**: Properti `darkMode: "class"` memberi tahu compiler NativeWind agar mengendalikan visualisasi modifier `dark:` berdasarkan penambahan class penanda di elemen paling atas (root). 
*   Ini memberi kebebasan bagi developer untuk memaksa aplikasi masuk ke mode gelap secara manual (menggunakan tombol sakelar di dalam menu pengaturan aplikasi) meskipun setelan handphone pengguna sedang menggunakan mode terang.

### Jawaban 3: Utility Class Mode Gelap
*   Kelas yang tepat adalah **`dark:text-white`** (atau `dark:text-slate-50`).
```
[FASE 2: HARI 33 SUKSES]
```
