# Hari 77: Menggambar Grafik Batang (Bar Chart)
## Merender Grafik Batang Stok Darah Menggunakan react-native-gifted-charts Serta Penataan Warna Sesuai Identitas PMI

Selamat datang di Hari 77! Hari ini kita akan mempraktikkan proses perakitan **Grafik Batang (Bar Chart)** untuk memvisualisasikan data stok kantong darah dinas UDD PMI Lampung.

Petugas membutuhkan diagram yang secara jelas memperlihatkan tinggi kolom volume stok darah. Jika kolom golongan darah O terlihat sangat ceper (pendek) dibanding yang lain, petugas bisa langsung membunyikan alarm panggilan donor darurat.

---

## 1. Pemasangan Pustaka Gifted Charts

Jalankan perintah berikut di terminal proyek Expo Anda:
```bash
npm install react-native-gifted-charts react-native-svg
```
*(Catatan: Library ini membutuhkan pendamping `react-native-svg` untuk melukis bentuk geometri batang di layar).*

---

## 2. Studi Kasus PMI: Grafik Stok Golongan Darah Real-time

Mari kita buat komponen grafik batang stok darah di berkas **`components/GrafikStokDarah.tsx`**:

```tsx
// components/GrafikStokDarah.tsx
import React from 'react';
import { View, Text } from 'react-native';
// Impor komponen BarChart
import { BarChart } from 'react-native-gifted-charts';

export default function GrafikStokDarah() {
  
  // Format Data Array Khusus react-native-gifted-charts
  const dataStokDarah = [
    { value: 120, label: 'A', frontColor: '#ef4444' },  // Merah
    { value: 85,  label: 'B', frontColor: '#ef4444' },
    { value: 30,  label: 'O', frontColor: '#ef4444' },  // Stok menipis kritis!
    { value: 150, label: 'AB', frontColor: '#ef4444' },
  ];

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4">
      <View>
        <Text className="font-extrabold text-slate-800 text-sm">📊 Ketersediaan Kantong Darah</Text>
        <Text className="text-[10px] text-slate-400 mt-0.5">Satuan: Bag / Kantong (UDD Kedaton)</Text>
      </View>

      {/* RENDER GRAFIK BATANG */}
      <View className="items-center py-2">
        <BarChart
          data={dataStokDarah}
          barWidth={35} // Lebar fisik batang tiang agar mudah disentuh jari (Hari 76)
          barBorderRadius={8} // Tiang melengkung premium di ujung atas
          spacing={24} // Jarak antar tiang agar tidak dempet
          noOfSections={4} // Jumlah garis horizontal pembagi skala Y
          maxValue={200} // Nilai batas atas skala Y
          
          // Styling Garis Skala & Angka
          yAxisTextStyle={{ fontSize: 9, color: '#94a3b8', fontWeight: 'bold' }}
          xAxisLabelTextStyle={{ fontSize: 10, color: '#475569', fontWeight: 'extrabold' }}
          yAxisColor="#f1f5f9"
          xAxisColor="#cbd5e1"
          
          // Fitur Efek Visual Gradasi & Tooltip
          isAnimated
          animationDuration={800}
        />
      </View>

      {/* Warning Alert Banner (Kondisional render jika ada stok di bawah 40 bag) */}
      <View className="bg-red-50 p-3.5 rounded-2xl border border-red-100 flex-row items-center space-x-2">
        <Text className="text-sm">⚠️</Text>
        <Text className="text-[9px] font-bold text-red-500 uppercase tracking-wide flex-1">
          Peringatan: Stok golongan darah O kritis (di bawah batas aman 40 kantong)!
        </Text>
      </View>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari parameter properti `barWidth={35}` pada komponen `<BarChart>` di atas? Hubungkan dengan prinsip akurasi target sentuh jari pengguna HP!
2. Buka konfigurasi data array `dataStokDarah`. Mengapa kita menambahkan properti `frontColor: '#ef4444'` secara spesifik di tiap objek golongan darah?
3. Sebutkan nama properti boolean yang bertugas memberikan efek transisi tiang meluncur tumbuh ke atas dari angka nol secara animatif saat layar pertama kali dibuka.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Pengamanan Ergonomi Tap Target
*   **Sentuhan Stabil**: Mengatur `barWidth` sebesar 35 unit memastikan lebar kolom batang cukup luas di layar HP. 
*   Petugas dengan jari tangan besar sekalipun tidak akan kesulitan mengklik tiang golongan darah "O" untuk memunculkan informasi angka stok tanpa takut salah menyenggol tiang golongan darah "B" di sebelahnya.

### Jawaban 2: Konsistensi Desain Brand Identity
*   Warna `#ef4444` adalah kode warna merah heksadesimal resmi Tailwind untuk menyesuaikan dengan identitas organisasi Palang Merah Indonesia (PMI). Ini menghindari penggunaan warna default bawaan library yang terkadang berwarna biru/hijau neon cerolok yang tidak sesuai identitas logo PMI.

### Jawaban 3: Properti isAnimated
*   Properti tersebut bernama **`isAnimated`** (yang dikombinasikan dengan `animationDuration`).
```
[FASE 5: HARI 77 SUKSES]
```
