# Hari 7: Tata Letak Flexbox Mobile
## Menyusun Kisi Layout Horizontal Serta Vertikal Menggunakan Flex Direction, Justify Content, & Align Items

Selamat datang di Hari 7! Hari ini kita akan menguasai sistem tata letak utama di React Native: **Flexbox Layouts**.

Berbeda dengan peramban web yang memiliki banyak sistem tata letak (seperti block, inline, table, grid, dan flex), React Native **hanya memiliki sistem Flexbox** sebagai satu-satunya cara menata posisi anak komponen. Kita akan mempelajari tiga pilar kendali sumbu Flexbox untuk menyusun antarmuka yang responsif di berbagai ukuran layar HP.

---

## 1. Tiga Pilar Sumbu Utama Flexbox

### A. Flex Direction (Arah Aliran Sumbu Utama)
Menentukan apakah anak-anak komponen disusun berbaris ke samping (Horizontal) atau menumpuk ke bawah (Vertikal).
*   **`flex-col` (Default)**: Sumbu utama berjalan dari **Atas ke Bawah**.
*   **`flex-row`**: Sumbu utama berjalan dari **Kiri ke Kanan**.

### B. Justify Content (Perataan Sumbu Utama - Main Axis)
Mengatur jarak spasi antar komponen di sepanjang arah sumbu utama yang dipilih.
*   **`justify-start` / `justify-end`**: Merapatkan ke ujung awal atau akhir.
*   **`justify-center`**: Mengelompokkan tepat di tengah-tengah ruang.
*   **`justify-between`**: Menyebar komponen ke ujung kiri-kanan dan memberi ruang kosong di tengahnya secara merata.

### C. Align Items (Perataan Sumbu Silang - Cross Axis)
Mengatur letak tinggi/posisi komponen di sepanjang sumbu yang berlawanan dengan sumbu utama.
*   **`items-center`**: Meratakan tinggi komponen agar tepat berada di garis tengah vertikal (sangat krusial untuk menyelaraskan teks dan ikon kecil agar sejajar).

---

## 2. Studi Kasus PMI: Layout Header Statistik Dashboard DonorKu

Mari kita susun komponen baris statistik menggunakan kombinasi sumbu Flexbox NativeWind:

```tsx
// components/StatistikBaris.tsx
import React from 'react';
import { View, Text } from 'react-native';

export default function StatistikBaris() {
  return (
    // Flex Row menyusun 2 kotak info berdampingan kiri-kanan secara horizontal
    <View className="flex-row justify-between items-center bg-slate-50 p-6 rounded-3xl border border-slate-100">
      
      <!-- Kolom Kiri: Info Jumlah Relawan -->
      <View className="flex-1 items-start space-y-1">
        <Text className="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Total Relawan</Text>
        <Text className="text-xl font-black text-slate-800">1.450</Text>
        <Text className="text-[9px] text-emerald-500 font-bold">↑ 12% Minggu ini</Text>
      </View>

      <!-- Garis Pembatas Vertikal Tengah -->
      <View className="w-[1px] h-10 bg-slate-200 mx-4" />

      <!-- Kolom Kanan: Info Kantong Darah Terkumpul -->
      <View className="flex-1 items-start space-y-1">
        <Text className="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Kantong Stok</Text>
        <Text className="text-xl font-black text-red-500">230 <Text className="text-xs text-slate-400 font-medium">Bag</Text></Text>
        <Text className="text-[9px] text-amber-500 font-bold">⚡ Kritis (Butuh O)</Text>
      </View>

    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa perbedaan arah default sumbu utama (**Flex Direction**) antara browser web biasa dengan React Native?
2. Buka kode `StatistikBaris` di atas. Jelaskan fungsi dari penulisan kelas `flex-1` pada kolom kiri dan kanan dan apa jadinya jika kelas tersebut kita hilangkan.
3. Sebutkan utility class justify yang tepat jika kita ingin menaruh tombol navigasi panah kembali tepat di pojok kiri layar, dan tombol ikon profil petugas tepat di pojok kanan layar.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Perbedaan Flex Direction Default
*   **Web Browser**: Default-nya adalah `row` (komponen diletakkan mendatar ke samping).
*   **React Native**: Default-nya adalah **`column`** (komponen diletakkan menumpuk ke bawah). Hal ini dikarenakan layar smartphone berbentuk memanjang ke bawah (Portrait), sehingga orientasi kolom mempermudah perancangan halaman mobile.

### Jawaban 2: Kegunaan flex-1 (Membagi Lebar Rata)
*   **Fungsi `flex-1`**: Menginstruksikan komponen untuk tumbuh (*Grow*) memenuhi seluruh ruang kosong yang tersisa. Karena kolom kiri dan kanan sama-sama menggunakan `flex-1`, mereka akan **membagi sisa lebar layar secara adil (masing-masing 50%)**.
*   **Akibat jika dihapus**: Lebar kolom akan menciut hanya selebar konten teks di dalamnya. Jika teks kolom kiri pendek, visual garis pembatas vertikal tengah akan tergeser ke kiri, membuat susunan grid menjadi tidak simetris/berat sebelah.

### Jawaban 3: Pilihan Justify Content
*   Pilihan terbaik adalah menggunakan **`justify-between`** pada view pembungkus flex-row. Ini akan mendorong komponen pertama ke ujung paling kiri, dan komponen terakhir ke ujung paling kanan secara otomatis.
