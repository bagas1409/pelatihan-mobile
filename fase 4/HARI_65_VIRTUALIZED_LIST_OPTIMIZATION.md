# Hari 65: Optimasi Render List Jauh (Virtualized List)
## Menghemat Memori RAM HP Menggunakan Properti FlatList windowSize Dan maxToRenderPerBatch Untuk Ribuan Data Relawan

Selamat datang di Hari 65! Hari ini kita akan mempelajari topik khusus tentang perbaikan kinerja (Performance Tuning) di aplikasi mobile: **Optimasi Virtualized List**.

Di web, merender 1.000 list baris div (`<ul><li>`) ke layar monitor secara langsung mungkin masih bisa dilahap oleh komputer Desktop. Di handphone, RAM dan CPU sangatlah terbatas. Merender ratusan antrean riwayat pendonor PMI secara langsung dalam waktu satu siklus frame akan langsung menenggelamkan aplikasi Anda ke lautan crash *Out of Memory*. Kita harus memoles FlatList bawaan kita agar menjadi mesin hemat RAM.

---

## 1. Memahami Cara Kerja Virtualization FlatList

React Native `FlatList` secara alamiah menghancurkan visualisasi list item yang sudah tergulung jauh tersembunyi ke atas luar layar dan hanya merender blok item yang terlihat sedang dipandang mata (+ beberapa item penyangga).

Tiga properti optimasi memori paling penting:
1.  **`windowSize`**: Besarnya radius kanvas visual di luar area yang terlihat di HP (defaultnya cukup boros = 21 lapis). Menurunkannya akan drastis menekan pemborosan RAM.
2.  **`maxToRenderPerBatch`**: Jumlah batas kartu item maksimal yang dirender di setiap detakan bingkai *frame* UI. (Default 10 item. Kurangi menjadi 5 agar layar responsif cepat).
3.  **`removeClippedSubviews`**: Menginstruksikan modul asli Android/iOS untuk langsung membunuh node UI komponen luar yang terlipat potong dari pandangan mata.

---

## 2. Studi Kasus PMI: Buku Besar Riwayat Ribuan Donor

Mari kita terapkan ketiga jurus sakti ini pada daftar gulir data berukuran masif di **`app/(tabs)/riwayat-massal.tsx`**:

```tsx
// app/(tabs)/riwayat-massal.tsx
import React, { useState } from 'react';
import { View, Text, FlatList } from 'react-native';

export default function LayarRiwayatMassal() {
  
  // Simulasi dummy data masif: Mengenerate array sebanyak 3.000 anggota riwayat
  const ribuanDataRiwayat = Array.from({ length: 3000 }, (_, i) => ({
    id: String(i),
    nama: `Pendonor No. ${i + 1}`,
    tanggal: `2026-06-${(i % 30) + 1}`, // Tanggal random
  }));

  // Komponen Kartu yang sangat banyak namun render harus dihemat
  const renderKartuItem = ({ item }: any) => (
    <View className="bg-white p-4 mb-2 rounded-2xl border border-slate-100 flex-row justify-between items-center mx-6 shadow-sm">
      <View>
        <Text className="font-bold text-slate-800 text-xs">{item.nama}</Text>
        <Text className="text-[10px] text-slate-400 mt-1">Transfusi Gol. O</Text>
      </View>
      <Text className="text-[10px] font-black text-emerald-500 bg-emerald-50 px-2 py-1 rounded border border-emerald-100">
        {item.tanggal}
      </Text>
    </View>
  );

  return (
    <View className="flex-1 bg-slate-50 pt-12 pb-4">
      <Text className="font-extrabold text-slate-800 text-sm px-6 mb-4">Buku Besar Riwayat ({ribuanDataRiwayat.length} Baris)</Text>
      
      <FlatList
        data={ribuanDataRiwayat}
        keyExtractor={(item) => item.id}
        renderItem={renderKartuItem}
        
        // ==========================================
        // 🚀 BAGIAN VITAL OPTIMASI MEMORI RAM
        // ==========================================
        windowSize={5} // (Default 21) Turunkan radius pre-render jendela list untuk hemat RAM. 5 sudah cukup memadai untuk HP modern.
        maxToRenderPerBatch={5} // Jangan paksakan render 10 baris sekali hentak, kurangi agar scroll tidak patah-patah
        initialNumToRender={8} // Di detik pertama buka layar, render hanya 8 kartu saja agar loading super instan
        removeClippedSubviews={true} // WAJIB untuk Android: Potong off-screen node memori UI View fisik di OS level!
        
        // Catatan: Memperkirakan tinggi tetap kartu (misal 80px) agar engine tak perlu kalkulasi manual ulang
        getItemLayout={(data, index) => ({
          length: 80, offset: 80 * index, index 
        })}
      />
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi penyelamatan memori yang dihasilkan dari penyalaan parameter boolean `removeClippedSubviews={true}` pada properti `FlatList` versi Android?
2. Buka properti optimasi di atas. Mengapa kita mengatur `initialNumToRender={8}` pada aplikasi mobile, padahal kita bisa menyuruhnya me-render 30 item sekaligus di detik pertama halaman dibuka?
3. Apa kelebihan fungsi `getItemLayout` (seperti yang dicontohkan dengan tinggi 80px di baris terakhir blok FlatList)? Mengapa mengetahui tinggi fisik kartu secara pasti membebani engine scroll OS dengan beban lebih ringan?

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Penghentian Kalkulasi Render Layar Tepi
*   **Amnesia Paksa Komponen**: Di lingkungan mesin render C++ OS Android, sebuah kotak `View` yang tidak nampak di frame monitor masih membebani *layout engine* untuk merender bayangannya. 
*   Memanggil setelan tersebut menyuruh sistem memotong bersih tumpukan layer bayangan tersebut dari CPU cache. Komponen list di layar Anda murni hanya yang dilihat secara langsung oleh bola mata petugas, drastis menekan panas suhu baterai saat asik men-scroll cepat (*Flick Scrolling*).

### Jawaban 2: Persepsi Kecepatan Instan Pertama (TTV - Time to Visible)
*   **Psikologi Waktu Loading**: Resolusi layar HP sangat kecil. Dalam satu layar penuh, biasanya hanya muat 5 sampai 8 tumpukan kartu. 
*   Jika kita menyuruh FlatList merender 30 komponen di detik layar pertama, komponen ke-9 s.d 30 itu percuma di-render karena tidak nampak oleh mata petugas saat awal buka, membuang waktu mili-detik CPU loading di balik layar. Dengan membatasi nilai 8, HP bisa menyelesaikan render sepersekian detik lebih instan tanpa bottleneck layar blank putih.

### Jawaban 3: Menghilangkan Kerja Hitung Otomatis Tinggi Objek
*   **Pemutus Kalkulator List**: Secara default, algoritma FlatList harus diam-diam menembakkan sensor mistar pengukur tinggi pada setiap komponen kartunya (`onLayout()`) untuk menghitung posisi pasti geseran scroll. Jika ada ribuan data, ini jutaan proses perhitungan CPU. 
*   Menyediakan fungsi pembantu `getItemLayout={...}` ibarat kita telah "Membocorkan Jawaban Hitungan" ke mesin CPU. FlatList tidak perlu mengkalkulasi apapun, ia hanya tinggal men-scroll posisi piksel sesuai kali-kalian baku index (80 * 20), mempercepat putaran FPS dari 20 FPS mentok ke 60 FPS mentega murni.
```
[FASE 4: HARI 65 SUKSES]
```
