# Hari 78: Menggambar Grafik Donat (Pie/Donut Chart)
## Menyajikan Komposisi Persentase Distribusi Golongan Darah Menggunakan PieChart Dinamis Lengkap Dengan Legenda Warna

Selamat datang di Hari 78! Kemarin kita berhasil merender grafik perbandingan kuantitatif (stok darah) dengan Bar Chart. Hari ini kita akan merancang **Grafik Donat (Donut Chart)**.

Grafik donat sangat cocok digunakan untuk menyajikan proporsi persentase (kualitatif). Kita akan menggambar bagan donat yang memaparkan data **Golongan Darah yang Paling Banyak Didonorkan** di Provinsi Lampung sepanjang bulan ini.

---

## 1. Menampilkan PieChart vs DonutChart

*   **Pie Chart**: Bentuk lingkaran penuh seperti kue pai.
*   **Donut Chart**: Lingkaran dengan lubang di tengahnya. Donut chart dianggap memiliki estetika desain yang lebih modern dan premium karena menyisakan ruang kosong di tengah lingkaran yang bisa kita gunakan untuk menaruh label teks total angka secara rapi.

---

## 2. Studi Kasus PMI: Komposisi Distribusi Darah Lampung

Mari kita buat komponen grafik donat di berkas **`components/GrafikDonatKomposisi.tsx`**:

```tsx
// components/GrafikDonatKomposisi.tsx
import React from 'react';
import { View, Text } from 'react-native';
// Impor komponen PieChart (Donut diaktifkan via props khusus)
import { PieChart } from 'react-native-gifted-charts';

export default function GrafikDonatKomposisi() {
  
  // Data Persentase Distribusi Donor
  const dataDonat = [
    { value: 40, color: '#ef4444', text: '40%' }, // Merah (O)
    { value: 30, color: '#3b82f6', text: '30%' }, // Biru (A)
    { value: 20, color: '#f59e0b', text: '20%' }, // Kuning (B)
    { value: 10, color: '#10b981', text: '10%' }, // Hijau (AB)
  ];

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4">
      <View>
        <Text className="font-extrabold text-slate-800 text-sm">🍩 Proporsi Distribusi Penyaluran</Text>
        <Text className="text-[10px] text-slate-400 mt-0.5">Periode: Bulan Berjalan (Seluruh RS Lampung)</Text>
      </View>

      <View className="flex-row items-center justify-between py-2">
        {/* GRAFIK LINGKARAN DONAT */}
        <PieChart
          data={dataDonat}
          
          // AKTIFKAN MODE DONUT (MENCIPTAKAN LUBANG DI TENGAH)
          donut
          showText // Tampilkan label teks persentase di dalam irisan donat
          textColor="#ffffff"
          textSize={8}
          textBold
          
          radius={60} // Jari-jari lingkaran luar
          innerRadius={40} // Jari-jari lingkaran lubang dalam (Donut hole)
          innerCircleColor="#ffffff" // Warna lubang tengah wajib sama dengan warna background card
          
          // Bagian tengah lubang donat diisi teks informasi total
          centerLabelComponent={() => (
            <View className="items-center justify-center">
              <Text className="text-slate-800 font-black text-xs">3.4K</Text>
              <Text className="text-[7px] text-slate-400 font-extrabold uppercase">Kantong</Text>
            </View>
          )}
        />

        {/* KOLOM LEGENDA WARNA KANAN */}
        <View className="space-y-2 flex-1 pl-8">
          <View className="flex-row items-center space-x-2">
            <View className="w-2.5 h-2.5 rounded-full bg-red-500" />
            <Text className="text-[9px] font-bold text-slate-600">Gol. O (40%)</Text>
          </View>
          <View className="flex-row items-center space-x-2">
            <View className="w-2.5 h-2.5 rounded-full bg-blue-500" />
            <Text className="text-[9px] font-bold text-slate-600">Gol. A (30%)</Text>
          </View>
          <View className="flex-row items-center space-x-2">
            <View className="w-2.5 h-2.5 rounded-full bg-amber-500" />
            <Text className="text-[9px] font-bold text-slate-600">Gol. B (20%)</Text>
          </View>
          <View className="flex-row items-center space-x-2">
            <View className="w-2.5 h-2.5 rounded-full bg-emerald-500" />
            <Text className="text-[9px] font-bold text-slate-600">Gol. AB (10%)</Text>
          </View>
        </View>
      </View>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari pemanggilan properti boolean `donut` pada komponen `<PieChart>` di atas? Apa yang terjadi secara visual jika properti ini dihapus?
2. Buka konfigurasi `centerLabelComponent` di atas. Jelaskan kegunaan rendering komponen anak di dalam lubang donat bagi nilai guna visualisasi informasi data.
3. Sebutkan aturan seting warna properti `innerCircleColor` agar lubang tengah donat terlihat bolong transparan secara sempurna menembus wadah background kartu.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Pengaktifan Lubang Tengah (Donut Ring Mode)
*   **Mengaktifkan Donat**: Properti `donut` memerintahkan modul penggambar untuk melubangi pusat koordinat lingkaran. 
*   Jika properti ini dihilangkan, grafik otomatis akan berubah menjadi **Pie Chart konvensional** (lingkaran penuh padat seperti potongan kue) tanpa ada lubang di tengahnya.

### Jawaban 2: Ruang Informasi Tambahan (Contextual Labeling)
*   **Optimalisasi Ruang**: Area tengah donat chart adalah "ruang mati" yang tak berguna jika donat berlubang kosong. 
*   Menaruh angka agregat makro di sana (seperti total "3.4K Kantong") memudahkan mata petugas langsung mencerna jumlah total volume keseluruhan distribusi bulan ini secara langsung tanpa perlu menjumlahkan angka persentase legenda satu persatu.

### Jawaban 3: Penyelarasan Warna Latar Belakang (Color Matching Rule)
*   Properti `innerCircleColor` harus diatur dengan string warna yang **sama persis** dengan warna latar belakang komponen pembungkusnya (yaitu `'#ffffff'` / putih). 
*   Karena sebenarnya lubang donat tersebut tidaklah bolong melayang, melainkan lingkaran pie merah yang ditumpuk dengan lingkaran putih di atasnya untuk memberikan ilusi optik seolah-olah berlubang.
```
[FASE 5: HARI 78 SUKSES]
```
