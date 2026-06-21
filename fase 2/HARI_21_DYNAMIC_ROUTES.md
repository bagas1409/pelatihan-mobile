# Hari 21: Rute Dinamis (Dynamic Routes)
## Membuat Halaman Rincian Riwayat Transaksi Donor Darah Berdasarkan ID Menggunakan Kurung Siku [id]

Selamat datang di Hari 21! Hari ini kita akan mempelajari cara membuat **Rute Dinamis** (Dynamic Routes) di Expo Router.

Di dunia operasional UDD PMI Lampung, setiap kali petugas mengklik salah satu baris relawan atau riwayat transaksi logistik, aplikasi harus membuka halaman detail khusus untuk ID yang bersangkutan. Kita tidak mungkin membuat 1.000 file halaman manual seperti `detail-1.tsx`, `detail-2.tsx`, dst. Kita cukup membuat satu file penampung dinamis bernama **`[id].tsx`** menggunakan kurung siku agar bisa melayani ribuan ID yang berbeda secara serentak.

---

## 1. Aturan Penulisan Nama File Kurung Siku `[id]`

Di dalam struktur folder direktori `app/`, folder atau file yang dibungkus tanda kurung siku siku bertindak sebagai **variabel parameter** penangkap nilai rute dinamis.

*   **Pola Berkas**: `app/donor/[id].tsx`
*   **Pemetaan URL Rute**:
    *   Jika pengguna mengakses `/donor/101` $\rightarrow$ File `[id].tsx` dibuka dengan nilai variabel `id` bernilai `'101'`.
    *   Jika pengguna mengakses `/donor/O-POS` $\rightarrow$ File `[id].tsx` dibuka dengan nilai variabel `id` bernilai `'O-POS'`.

---

## 2. Studi Kasus PMI: Halaman Detail Relawan Berdasarkan NIK

Mari kita buat struktur rute dinamis untuk melihat rincian data relawan di direktori file **`app/relawan/[id].tsx`**:

```tsx
// app/relawan/[id].tsx
import React from 'react';
import { View, Text } from 'react-native';
// Mengimpor hook pembaca parameter rute
import { useLocalSearchParams } from 'expo-router';

export default function HalamanDetailRelawan() {
  
  // 1. MEMBACA PARAMETER ID DARI URL RUTE
  // Kunci properti 'id' harus sama persis dengan nama file [id].tsx
  const { id } = useLocalSearchParams<{ id: string }>();

  return (
    <View className="flex-1 bg-slate-50 justify-center items-center p-6 space-y-4">
      <View className="bg-white p-8 rounded-3xl border border-slate-200 shadow-sm items-center w-full max-w-sm">
        
        <View className="w-16 h-16 rounded-full bg-red-100 items-center justify-center mb-4">
          <Text className="text-2xl">👤</Text>
        </View>

        <Text className="text-xs text-slate-400 font-bold uppercase tracking-wider">Identitas Relawan</Text>
        
        <!-- MENAMPILKAN ID DINAMIS DI LAYAR -->
        <Text className="text-lg font-black text-slate-800 mt-1">NIK/ID: {id}</Text>

        <View className="w-full border-t border-slate-100 pt-4 mt-4 space-y-2 text-xs">
          <Text className="text-slate-500">Golongan Darah: <Text className="font-bold text-slate-800">O</Text></Text>
          <Text className="text-slate-500">Status Otorisasi: <Text className="font-bold text-emerald-500">Terverifikasi</Text></Text>
        </View>

      </View>
    </View>
  );
}
```

---

## 3. Cara Mengarahkan Navigasi ke Rute Dinamis

Untuk mengalihkan layar petugas ke halaman detail relawan dengan ID `187102`, kita cukup menuliskan kode navigasi sebagai berikut:

```tsx
// Melalui tag Link
<Link href="/relawan/187102">Lihat Detail Relawan</Link>

// Melalui helper router programmatic
router.push('/relawan/187102');
```

---

## 4. Latihan Soal Mandiri
1. Apa fungsi dari penulisan tanda kurung siku `[id]` pada penamaan file di dalam Expo Router?
2. Buka kode `app/relawan/[id].tsx` di atas. Sebutkan nama hook bawaan Expo Router yang digunakan untuk menangkap nilai parameter `id` tersebut di dalam komponen.
3. Apa yang terjadi jika nama file diganti menjadi `[nik].tsx`? Bagaimana cara mengakses variabel tersebut menggunakan hook pembaca parameter rute?

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Konsep Wildcard / Dynamic Param
*   **Fungsi Wildcard**: Tanda kurung siku bertindak sebagai penangkap variabel bebas (*Wildcard / Parameterized Segment*). 
*   Dia memberi tahu sistem navigasi untuk mencocokkan string apa saja yang diinputkan di posisi koordinat segment URL tersebut dan memindahkannya ke dalam memori variabel lokal agar bisa dikonsumsi oleh script logika halaman.

### Jawaban 2: Hook useLocalSearchParams
*   Hook tersebut bernama **`useLocalSearchParams()`**.

### Jawaban 3: Penyelarasan Nama Variabel
*   Jika nama file diubah menjadi `[nik].tsx`, maka variabel pencocokan di dalam kode pemrograman juga akan berubah kuncinya. 
*   Kita harus memanggil properti `nik` di dalam destructing hook: `const { nik } = useLocalSearchParams();` agar nilainya berhasil tertangkap, menyelaraskan nama berkas fisik dengan nama variabel JavaScript.
