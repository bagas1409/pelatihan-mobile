# Hari 6: Pengenalan Styling - NativeWind v4
## Menginstal & Menerapkan Tailwind CSS Pada Komponen Mobile Menggunakan NativeWind Versi Terbaru

Selamat datang di Hari 6! Hari ini kita akan mempelajari cara menghias aplikasi mobile kita menggunakan **NativeWind v4** (penerjemah utility class Tailwind CSS untuk React Native).

Menulis kode `StyleSheet` yang panjang seperti di Hari 5 terasa lambat dan memakan banyak baris file. Dengan NativeWind, kita bisa menggunakan kelas-kelas Tailwind yang sudah sangat kita kuasai (seperti `bg-red-500`, `rounded-3xl`, `flex-row`, `p-6`) langsung pada properti **`className`** komponen mobile kita.

---

## 1. Bagaimana NativeWind Bekerja?

NativeWind bertindak sebagai jembatan yang menerjemahkan string kelas Tailwind CSS menjadi objek `StyleSheet` native yang dipahami oleh Android/iOS.
*   **Contoh Web**: `<div class="bg-blue-500 p-4"></div>`
*   **Contoh NativeWind**: `<View className="bg-blue-500 p-4"></View>`

Di balik layar, NativeWind menerjemahkan `bg-blue-500` menjadi `backgroundColor: '#3b82f6'` dan `p-4` menjadi `padding: 16`.

---

## 2. Cara Mengonfigurasi NativeWind v4 di Expo

Proses instalasi membutuhkan beberapa konfigurasi file agar compiler Tailwind bisa bekerja secara native di Android/iOS:

1.  **Instal Paket Dependensi**:
    ```bash
    npm install nativewind@^4.2.5 tailwindcss@^3.4.1 react-native-reanimated
    ```

2.  **Inisialisasi File Tailwind Config (`tailwind.config.js`)**:
    Buat file **`tailwind.config.js`** di root folder proyek Anda. Daftarkan folder `app` dan `components` (dan versi `src` jika Anda menggunakannya seperti di proyek `belajar`):
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
      theme: {
        extend: {},
      },
      plugins: [],
    }
    ```

3.  **Daftarkan Preset Babel (`babel.config.js`)**:
    Buka file **`babel.config.js`** di root folder Anda dan sesuaikan agar NativeWind terdaftar sebagai **preset** (dan sesuaikan `jsxImportSource`):
    ```javascript
    module.exports = function (api) {
      api.cache(true);
      return {
        presets: [
          ['babel-preset-expo', { jsxImportSource: 'nativewind' }],
          'nativewind/babel',
        ],
        plugins: [
          'react-native-reanimated/plugin', // Tempatkan plugin reanimated jika digunakan
        ],
      };
    };
    ```

4.  **Buat File Global CSS & Daftarkan Tailwind Directives**:
    Buat file **`global.css`** (di root folder atau di `src/global.css` jika menggunakan struktur `src`). Isi file tersebut dengan direktif Tailwind:
    ```css
    @tailwind base;
    @tailwind components;
    @tailwind utilities;
    ```

5.  **Konfigurasi Metro Bundler (`metro.config.js`)**:
    Buat file **`metro.config.js`** di root folder proyek Anda untuk memberitahu Metro agar memproses file CSS menggunakan compiler NativeWind:
    ```javascript
    // metro.config.js
    const { getDefaultConfig } = require("expo/metro-config");
    const { withNativeWind } = require("nativewind/metro");

    const config = getDefaultConfig(__dirname);

    // Sesuaikan path input CSS (misal: "./src/global.css" atau "./global.css")
    module.exports = withNativeWind(config, { input: "./src/global.css" });
    ```

6.  **Import File CSS di Root Layout (`_layout.tsx`)**:
    Buka file **`src/src/app/_layout.tsx (atau app/_layout.tsx)`** (atau **`src/app/_layout.tsx (atau app/_layout.tsx)`**) dan impor file CSS global Anda di baris paling atas agar style Tailwind di-load ke seluruh layar:
    ```tsx
    import '../global.css'; // Sesuai relative path dari folder layout Anda
    import { Slot } from 'expo-router';
    // ...
    ```

---

## 3. Studi Kasus PMI: Card Posko Donor dengan NativeWind

Setelah konfigurasi selesai, mari kita tulis ulang komponen Card Posko Donor kita menggunakan utility class Tailwind CSS yang bersih dan rapi:

```tsx
// src/components/CardPoskoTailwind.tsx (atau components/CardPoskoTailwind.tsx)
import React from 'react';
import { View, Text, Image } from 'react-native';

export default function CardPoskoTailwind() {
  const LOGO_PMI = 'https://pmi.or.id/wp-content/uploads/2019/10/pmi-logo.png';

  return (
    // className menggantikan penulisan style sheet manual yang panjang!
    <View className="flex-row bg-white p-4 rounded-3xl border border-slate-100 items-center shadow-sm">
      
      <!-- Logo bulat merah -->
      <Image 
        source={{ uri: LOGO_PMI }} 
        className="w-12 h-12 rounded-full mr-3" 
      />

      <!-- Informasi Teks -->
      <View className="flex-1">
        <Text className="font-extrabold text-sm text-slate-800">UDD PMI Kedaton</Text>
        <Text className="text-xs text-slate-400 mt-1">Bandar Lampung</Text>
      </View>

      <!-- Badge Indikator Status Darurat -->
      <View className="bg-red-50 px-2.5 py-1 rounded-full border border-red-100">
        <Text className="text-[10px] font-bold text-red-500 uppercase">UDD PUSAT</Text>
      </View>

    </View>
  );
}
```

---

## 4. Latihan Soal Mandiri
1. Amati file konfigurasi `tailwind.config.js` bagian properti `content`.
2. Jelaskan fungsi dari baris array `content: ["./app/**/*.{js,jsx,ts,tsx}", ...]` tersebut dan apa akibatnya jika folder komponen baru kita lupa kita daftarkan di dalamnya.
3. Sebutkan kelebihan penulisan styling menggunakan NativeWind (Tailwind CSS) dibandingkan menggunakan `StyleSheet.create()` biasa dari segi kecepatan pengerjaan desain (*Rapid Prototyping*).

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kegunaan Properti Content Tailwind Config
*   **Penyaringan File (Purging)**: Properti `content` berfungsi memberi tahu compiler Tailwind direktori mana saja yang di dalamnya terdapat penulisan kelas-kelas Tailwind CSS.
*   **Akibat jika lupa**: Jika kita membuat folder baru bernama `src/components/` namun lupa mendaftarkannya di array `content`, compiler Tailwind tidak akan memeriksa folder tersebut. Akibatnya, seluruh kelas Tailwind yang kita tulis di file tersebut **tidak akan diterjemahkan**, menyebabkan tampilan komponen kita polos rusak tanpa style sama sekali.

### Jawaban 3: Keunggulan NativeWind
*   **Menghapus File Bloat & Duplikasi**: Kita tidak perlu lagi menulis ribuan baris variabel penampung style di bagian bawah file yang menjauhkan mata kita dari elemen JSX.
*   **Pemahaman Universal**: Developer web yang sudah menguasai Tailwind CSS bisa langsung menulis styling layout mobile tanpa perlu menghafal nama-nama properti StyleSheet React Native yang menggunakan penulisan camelCase (seperti `borderRadius` vs `rounded-3xl`), mempercepat durasi pengerjaan layout secara instan.
