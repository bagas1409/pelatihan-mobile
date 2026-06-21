# Hari 39: Integrasi GET API - Mengambil Data Stok
## Menampilkan Daftar Logistik Kantong Darah Dari Database Pusat Menggunakan Custom Hook useApi

Selamat datang di Hari 39! Hari ini kita akan mempraktikkan proses penarikan data nyata pertama kita dari server backend database menggunakan custom hook **`useApi`** yang sudah kita rancang di Hari 37.

Kita akan menembak rute API `/stok` untuk mengunduh daftar golongan darah beserta status jumlah kantong darah terupdate yang ada di markas pusat PMI Lampung, lalu menggambarnya ke dalam layout mobile menggunakan NativeWind.

---

## 1. Persiapan Tipe Data TypeScript (`types/stok.ts`)

Agar data hasil download terdeteksi dengan aman oleh TypeScript, mari kita buat file kamus tipe data baru di **`types/stok.ts`**:

```typescript
// types/stok.ts
export interface StokDarah {
  id: string;
  golonganDarah: string; // Contoh: "A", "B", "O", "AB"
  jumlahBag: number;     // Jumlah kantong aktif
  kategori: 'Aman' | 'Kritis' | 'Menipis';
}
```

---

## 2. Studi Kasus PMI: Halaman Monitor Stok Darah Dinas

Buka atau buat file halaman baru di **`app/(tabs)/stok-monitor.tsx`**, lalu panggil custom hook `useApi` kita:

```tsx
// app/(tabs)/stok-monitor.tsx
import React from 'react';
import { View, Text, ActivityIndicator, FlatList, TouchableOpacity } from 'react-native';
import { useApi } from '../../hooks/useApi';
import { StokDarah } from '../../types/stok';

export default function StokMonitor() {
  
  // 1. TEMBAK API MENGGUNAKAN CUSTOM HOOK
  // Kita menyuplai tipe data generik <StokDarah[]> ke hook useApi
  const { data: listStok, loading, error, kocokUlang } = useApi<StokDarah[]>('/stok');

  // 2. TAMPILAN JIKA LOADING AKTIF
  if (loading) {
    return (
      <View className="flex-1 bg-slate-50 justify-center items-center">
        <ActivityIndicator size="large" color="#ef4444" />
        <Text className="text-xs text-slate-400 mt-2 font-medium">Mengunduh data stok terbaru...</Text>
      </View>
    );
  }

  // 3. TAMPILAN JIKA ERROR SAMBUNGAN SERVER
  if (error) {
    return (
      <View className="flex-1 bg-white justify-center items-center p-6 space-y-4 text-center">
        <Text className="text-xl">🚨</Text>
        <Text className="text-xs font-bold text-slate-800">Sambungan Gagal</Text>
        <Text className="text-xs text-slate-400 max-w-xs">{error}</Text>
        
        {/* Tombol Refresh Manual */}
        <TouchableOpacity 
          onPress={kocokUlang}
          className="bg-red-500 px-6 py-2.5 rounded-xl mt-2"
        >
          <Text className="text-white font-bold text-xs uppercase">Coba Lagi</Text>
        </TouchableOpacity>
      </View>
    );
  }

  return (
    <View className="flex-1 bg-slate-50 p-6">
      <Text className="text-base font-black text-slate-800 mb-6">🩸 Monitor Logistik Kantong Darah</Text>

      {/* 4. RENDER DATA REAL DARI SERVER */}
      <FlatList
        data={listStok}
        keyExtractor={(item) => item.id}
        showsVerticalScrollIndicator={false}
        renderItem={({ item }) => (
          <View className="bg-white p-5 rounded-3xl border border-slate-100 mb-4 flex-row justify-between items-center shadow-sm">
            <View className="space-y-1">
              <Text className="font-extrabold text-slate-800 text-sm">Golongan Darah {item.golonganDarah}</Text>
              
              {/* Badge Warna Status Dinamis */}
              <View className={`px-2 py-0.5 rounded-full border self-start ${
                item.kategori === 'Aman' ? 'bg-emerald-50 border-emerald-100' : 'bg-red-50 border-red-100'
              }`}>
                <Text className={`text-[8px] font-black uppercase ${
                  item.kategori === 'Aman' ? 'text-emerald-500' : 'text-red-500'
                }`}>{item.kategori}</Text>
              </View>
            </View>

            <Text className="text-xl font-black text-slate-800">
              {item.jumlahBag} <Text className="text-xs text-slate-400 font-medium">Bag</Text>
            </Text>
          </View>
        )}
      />
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari pemanggilan generic type `<StokDarah[]>` pada baris pemanggilan hook `useApi` di atas?
2. Jelaskan bagaimana alur penanganan kondisi layar jika server backend mendadak mati (padam) saat tombol monitor dibuka oleh petugas.
3. Sebutkan nama properti penentu warna badge status stok darah yang bernilai `'Aman'` di dalam kode di atas.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Keuntungan TypeScript Generics
*   **Keamanan Pengetikan**: Membantu TypeScript mengetahui bahwa variabel `listStok` memiliki struktur array objek yang beranggotakan properti `id`, `golonganDarah`, `jumlahBag`, dan `kategori`. 
*   VS Code otomatis memunculkan peringatan ketik jika kita mencoba memanggil properti fiktif (seperti `item.warna`) di dalam FlatList, mencegah error *undefined* saat run-time.

### Jawaban 2: Penanganan Fail-Safe
*   **Penyelamatan Tampilan**: Jika server mati, fungsi fetch melempar error caught. State `error` akan diisi pesan error dari Axios, state `loading` berubah menjadi `false`, dan React langsung merender blok visual *Sambungan Gagal* (Poin 3) yang menyajikan instruksi tombol "Coba Lagi" yang user-friendly, bukan melempar crash layar putih kosong di HP petugas.

### Jawaban 3: Penanda Visual Kategori
*   Properti tersebut adalah **`item.kategori === 'Aman'`**. Jika terpenuhi, badge diwarnai dengan nuansa hijau lembut (`bg-emerald-50` / `text-emerald-500`), dan jika kritis diwarnai merah lembut (`bg-red-50` / `text-red-500`).
```
[FASE 3: HARI 39 SUKSES]
```
