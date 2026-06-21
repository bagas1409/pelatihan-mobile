# Hari 40: Fitur Tarik untuk Memperbarui (Pull to Refresh)
## Mengintegrasikan RefreshControl Pada FlatList Untuk Memperbarui Data Stok Darah Secara Instan

Selamat datang di Hari 40! Hari ini kita akan mempelajari cara mengintegrasikan fitur **Pull to Refresh** (Tarik untuk Memperbarui) pada komponen `FlatList`.

Fitur tarik-layar-kebawah-untuk-loading adalah standar interaksi emas (*User Experience*) di platform mobile. Saat petugas PMI Lampung sedang berdiri memantau persediaan kantong darah, mereka butuh metode cepat untuk menyegarkan tampilan data di layar HP tanpa harus mencari-cari tombol refresh kecil. Kita akan merakit fitur ini menggunakan komponen bawaan **`RefreshControl`** dari React Native.

---

## 1. Mengenal Komponen `RefreshControl`

`RefreshControl` disisipkan ke dalam properti **`refreshControl`** milik `FlatList` atau `ScrollView`. Dia bertugas:
1.  Menangkap usapan jempol pengguna yang menarik bagian atas list ke arah bawah.
2.  Menampilkan indikator loading melingkar bawaan OS (*Spinner*) di bagian atas list.
3.  Menjalankan fungsi callback pemanggil API yang kita sediakan, lalu menutup loading spinner secara otomatis setelah proses unduh selesai.

---

## 2. Studi Kasus PMI: Pull to Refresh pada Monitor Stok

Mari kita modifikasi halaman `stok-monitor.tsx` kita agar mendukung fitur tarik untuk refresh:

```tsx
// app/(tabs)/stok-monitor.tsx
import React, { useState } from 'react';
import { View, Text, FlatList, RefreshControl } from 'react-native';
import { useApi } from '../../hooks/useApi';
import { StokDarah } from '../../types/stok';

export default function StokMonitorRefreshable() {
  const { data: listStok, loading, error, kocokUlang } = useApi<StokDarah[]>('/stok');
  
  // State lokal penanda apakah proses pull-to-refresh sedang aktif berjalan
  const [refreshing, setRefreshing] = useState(false);

  // Fungsi pemegang kendali tarik layar
  const handleTarikLayar = async () => {
    setRefreshing(true);
    console.log("[PullToRefresh] Petugas menarik layar. Merefresh database...");
    
    await kocokUlang(); // Jalankan ulang Axios fetcher dari custom hook (Hari 37)
    
    setRefreshing(false); // Selesai -> Matikan loading spinner di atas layar HP!
    console.log("[PullToRefresh] Sinkronisasi sukses.");
  };

  return (
    <View className="flex-1 bg-slate-50 p-6">
      <Text className="text-base font-black text-slate-800 mb-6">🩸 Monitor Logistik Kantong Darah</Text>

      <FlatList
        data={listStok}
        keyExtractor={(item) => item.id}
        showsVerticalScrollIndicator={false}
        
        // 1. PASANG REFRESHCONTROL DI FLATLIST
        refreshControl={
          <RefreshControl
            refreshing={refreshing} // State pelacak loading
            onRefresh={handleTarikLayar} // Fungsi pemicu saat ditarik
            colors={['#ef4444']} // Warna spinner melingkar khusus Android (Merah PMI)
            tintColor="#ef4444" // Warna spinner melingkar khusus iOS
          />
        }

        renderItem={({ item }) => (
          <View className="bg-white p-5 rounded-3xl border border-slate-100 mb-4 flex-row justify-between items-center shadow-sm">
            <Text className="font-bold text-slate-700 text-xs">Golongan {item.golonganDarah}</Text>
            <Text className="font-black text-slate-800 text-sm">{item.jumlahBag} Bag</Text>
          </View>
        )}
      />
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari pemanggilan state `setRefreshing(false)` di akhir fungsi `handleTarikLayar` di atas? Apa efek visualnya di layar HP jika baris tersebut lupa kita tulis?
2. Mengapa kita disarankan menggunakan `RefreshControl` bawaan React Native dibandingkan membuat sensor sentuhan geser manual menggunakan `useRef` atau `Animated` API?
3. Sebutkan properti pada `RefreshControl` yang bertugas memberi warna pada lingkaran loading khusus di handphone sistem operasi **iOS**.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Penghentian Animasi Loading Spinner
*   **Menyudahi Animasi**: Fungsi `setRefreshing(false)` memberi tahu komponen `RefreshControl` bahwa transaksi unduh data terbaru dari server API telah sukses diselesaikan. 
*   **Efek jika lupa**: Indikator lingkaran berputar merah di bagian atas layar HP **akan terus berputar selamanya tanpa pernah mati/hilang**, membuat petugas bingung karena mengira aplikasi mereka sedang macet loading terus menerus.

### Jawaban 2: Keunggulan Native UX & Performa
*   **Efisiensi Gestur**: `RefreshControl` langsung terikat pada thread UI asli handphone (*Native UI Thread*). 
*   Dia mendeteksi batas gesekan atas secara sangat presisi, aman dari bug konflik scroll, dan berkinerja sangat mulus di HP spesifikasi rendah tanpa perlu membebani memori CPU untuk melakukan kalkulasi posisi koordinat piksel secara manual.

### Jawaban 3: Properti tintColor
*   Properti tersebut bernama **`tintColor`** (khusus iOS), sedangkan untuk OS Android menggunakan properti **`colors`** (menerima array warna).
```
[FASE 3: HARI 40 SUKSES]
```
