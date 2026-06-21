# Hari 10: Render List Besar dengan FlatList
## Mengelola Tampilan Ribuan Jadwal Kegiatan Donor Tanpa Menurunkan Kecepatan RAM Menggunakan Konsep Virtualized List

Selamat datang di Hari 10! Hari ini kita akan mempelajari komponen pembuat daftar terpenting di React Native: **`FlatList`**.

Saat mengoding web di Fase 2, kita merender list menggunakan looping `v-for` di dalam `<ul>`. Jika kita memaksakan meloop data menggunakan looping `.map()` biasa di dalam `<ScrollView>` mobile untuk jumlah data yang sangat besar (misal 5.000 riwayat donor darah), HP petugas akan langsung **kehabisan RAM, melambat panas, dan akhirnya aplikasi terhenti paksa (force close)**. Kita akan belajar bagaimana `FlatList` memecahkan masalah ini dengan cerdas.

---

## 1. Mengapa ScrollView Payah untuk List Besar?

*   **ScrollView**: Merender dan menggambar **seluruh 5.000 baris data sekaligus di memori RAM** di detik pertama halaman dibuka, meskipun yang terlihat di layar HP kecil Anda hanya 5 baris pertama. Ini memboroskan memori CPU.
*   **FlatList (Virtualized List)**: Hanya merender 5 s.d 10 baris data yang **sedang terlihat mata di layar** saat itu. Saat Anda melakukan scroll ke bawah, baris atas yang keluar dari layar akan **dihancurkan/dihapus sementara dari memori RAM** untuk mendaur ulang ruang memori guna menggambar baris baru yang akan masuk di bawah (*Memory Recycling*).

---

## 2. Struktur Tiga Parameter Wajib FlatList

Untuk menyalakan FlatList, kita wajib menyuplai 3 properti utama:
1.  **`data`**: Array data mentah dari database.
2.  **`renderItem`**: Fungsi callback penentu bagaimana satu baris data digambar (JSX component).
3.  **`keyExtractor`**: Fungsi penentu string kunci unik untuk masing-masing baris (seperti `:key` pada Vue `v-for`).

---

## 3. Studi Kasus PMI: Daftar Jadwal Donor Darah Keliling

Mari kita bangun daftar jadwal donor darah mobile menggunakan FlatList reaktif:

```tsx
// components/DaftarJadwalDonor.tsx
import React from 'react';
import { FlatList, View, Text } from 'react-native';

// 1. DATA DATABASE SIMULASI
const DATA_JADWAL = [
  { id: '1', tempat: 'Mall Boemi Kedaton', tanggal: '22 Juni 2026', jam: '09.00 - 15.00' },
  { id: '2', tempat: 'Kampus Universitas Lampung', tanggal: '23 Juni 2026', jam: '08.00 - 13.00' },
  { id: '3', tempat: 'Kantor Gubernur Lampung', tanggal: '25 Juni 2026', jam: '09.00 - 14.00' },
  { id: '4', tempat: 'Alun-alun Kota Metro', tanggal: '26 Juni 2026', jam: '16.00 - 20.00' }
];

export default function DaftarJadwalDonor() {
  
  // 2. COMPONENT BARIS INDIVIDU (ITEM RENDERER)
  const renderItemBaris = ({ item }: { item: typeof DATA_JADWAL[0] }) => (
    <View className="bg-white p-5 rounded-3xl border border-slate-100 mb-4 flex-row justify-between items-center shadow-sm">
      <View className="space-y-1">
        <Text className="font-extrabold text-slate-800 text-sm">{item.tempat}</Text>
        <Text className="text-xs text-slate-400">📅 {item.tanggal}</Text>
      </View>
      
      <View className="bg-red-50 px-3 py-1.5 rounded-2xl border border-red-100">
        <Text className="text-[10px] font-bold text-red-500">{item.jam}</Text>
      </View>
    </View>
  );

  return (
    <View className="flex-1 p-6 bg-slate-50">
      <Text className="text-base font-black text-slate-800 mb-6">📍 Agenda Donor Darah Terdekat</Text>

      <!-- 3. EKSEKUSI FLATLIST -->
      <FlatList
        data={DATA_JADWAL}
        renderItem={renderItemBaris}
        keyExtractor={(item) => item.id}
        showsVerticalScrollIndicator={false}
      />
    </View>
  );
}
```

---

## 4. Latihan Soal Mandiri
1. Apa nama konsep arsitektur yang digunakan oleh `FlatList` untuk menghemat konsumsi memori RAM handphone saat menggambar ribuan baris data?
2. Jelaskan fungsi dari parameter `keyExtractor` pada komponen `FlatList` di atas dan mengapa isinya disarankan bertipe data **String**, bukan bertipe data **Number**.
3. Sebutkan parameter properti pada FlatList yang bertugas merender baris visual kosong/pesan khusus jika array data yang datang dari database server ternyata bernilai kosong (`[]`).

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Konsep Virtualized List & Memory Recycling
*   **Virtualized List**: FlatList hanya merender item yang masuk area pandang layar (*Viewport*). 
*   Item yang sudah bergeser jauh ke atas akan dihancurkan untuk membebaskan ruang memori RAM, lalu komponen pembungkusnya didaur ulang (*Recycled*) untuk menampilkan data baris baru di bawahnya, menjaga kestabilan memori RAM HP petugas agar tetap dingin dan ringan.

### Jawaban 2: Kegunaan keyExtractor
*   **Pelacak Reaktivitas**: `keyExtractor` membantu React membedakan item mana saja yang mengalami perubahan nilai, terhapus, atau berpindah urutan secara reaktif tanpa perlu menggambar ulang seluruh list dari awal. 
*   **Wajib String**: Engine internal React Native menuntut key pengenal unik harus berupa tipe data **String** untuk mempermudah proses indexing di tingkat compiler native Android/iOS.

### Jawaban 3: Properti ListEmptyComponent
*   FlatList menyediakan properti **`ListEmptyComponent`** (seperti `v-else` pada loop). Jika array data bernilai kosong, kita bisa menyuplai komponen khusus (misalnya gambar ikon kartun relawan tersenyum bertuliskan *"Tidak ada jadwal donor untuk hari ini"*) agar layar tidak tampak kosong rusak.
