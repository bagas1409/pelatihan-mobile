# Hari 11: Render List Berkelompok dengan SectionList
## Menyajikan Daftar Nama Relawan Berdasarkan Kelompok Golongan Darah A, B, O, Dan AB

Selamat datang di Hari 11! Hari ini kita akan mempelajari komponen daftar tingkat lanjut: **`SectionList`**.

Jika `FlatList` merender baris data satu persatu secara linier dari atas ke bawah, `SectionList` memungkinkan kita merender data yang **terbagi ke dalam kelompok-kelompok kategori** (berisi Header Judul Kelompok dan baris-baris data anggota di bawahnya). Pada studi kasus PMI Lampung, kita butuh mengelompokkan nama-nama relawan berdasarkan golongan darahnya agar mempermudah petugas mencari pendonor yang sesuai dengan cepat.

---

## 1. Perbedaan Struktur Data: FlatList vs SectionList

*   **FlatList**: Menerima array objek datar satu level:
    ```javascript
    [{ nama: "Budi" }, { nama: "Siti" }]
    ```
*   **SectionList**: Menerima array objek berkelompok dua tingkat. Setiap objek wajib memiliki kunci bernama **`title`** (nama kelompok) dan kunci bernama **`data`** (array isi anggota kelompok):
    ```javascript
    [
      { title: "Golongan A", data: [{ nama: "Budi" }] },
      { title: "Golongan B", data: [{ nama: "Siti" }] }
    ]
    ```

---

## 2. Studi Kasus PMI: Pengelompokan Data Relawan Aktif

Mari kita buat komponen daftar relawan berkelompok menggunakan SectionList NativeWind:

```tsx
// components/DaftarRelawanKelompok.tsx
import React from 'react';
import { SectionList, View, Text } from 'react-native';

// 1. DATA DATABASE DUA TINGKAT
const DATA_RELAWAN = [
  {
    title: '🩸 Golongan Darah O',
    data: [
      { id: '1', nama: 'Ahmad Syarif', wilayah: 'Bandar Lampung' },
      { id: '2', nama: 'Budi Santoso', wilayah: 'Metro' }
    ]
  },
  {
    title: '🩸 Golongan Darah A',
    data: [
      { id: '3', nama: 'Siti Rahma', wilayah: 'Lampung Selatan' },
      { id: '4', nama: 'Dedi Kurniawan', wilayah: 'Pringsewu' }
    ]
  }
];

export default function DaftarRelawanKelompok() {
  
  // 2. COMPONENT BARIS ANGGOTA (ITEM RENDERER)
  const renderBarisAnggota = ({ item }: { item: typeof DATA_RELAWAN[0]['data'][0] }) => (
    <View className="bg-white px-6 py-4 border-b border-slate-100 flex-row justify-between items-center">
      <Text className="font-semibold text-slate-800 text-xs">{item.nama}</Text>
      <Text className="text-[10px] text-slate-400 font-bold">{item.wilayah}</Text>
    </View>
  );

  // 3. COMPONENT HEADER KELOMPOK (SECTION HEADER RENDERER)
  const renderHeaderKelompok = ({ section: { title } }: { section: typeof DATA_RELAWAN[0] }) => (
    <View className="bg-slate-100 px-6 py-2.5">
      <Text className="text-[10px] font-black text-slate-500 uppercase tracking-widest">{title}</Text>
    </View>
  );

  return (
    <View className="flex-1 bg-slate-50">
      
      <!-- 4. EKSEKUSI SECTIONLIST -->
      <SectionList
        sections={DATA_RELAWAN}
        renderItem={renderBarisAnggota}
        renderSectionHeader={renderHeaderKelompok}
        keyExtractor={(item) => item.id}
        stickySectionHeadersEnabled={true} // Membuat judul kelompok menempel di atas saat di-scroll
      />

    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Amati parameter properti `stickySectionHeadersEnabled={true}` pada SectionList di atas.
2. Jelaskan efek visual dari properti `stickySectionHeadersEnabled={true}` tersebut bagi kenyamanan mata pengguna saat men-scroll kategori data relawan yang sangat panjang.
3. Mengapa kita wajib menamakan properti isi anggota kelompok dengan nama kunci `data` (huruf kecil) pada struktur array input SectionList? Jelaskan dari sisi kebutuhan parser library.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kegunaan Sticky Section Headers
*   **Efek Balon Menempel**: Saat kita men-scroll baris data ke bawah, judul kelompok (misalnya *"Golongan Darah O"*) akan **menempel diam di bagian paling atas layar**, bertindak sebagai pembatas visual. 
*   Judul tersebut baru akan terdorong keluar layar jika ada kelompok baru (seperti *"Golongan Darah A"*) yang datang dari bawah menggantikan posisinya. Ini menjaga ingatan petugas agar tetap tahu kelompok kategori mana yang sedang mereka baca di tengah-tengah ratusan baris daftar nama relawan.

### Jawaban 3: Aturan Kunci Data pada SectionList
*   **Ketetapan API**: Engine internal `SectionList` dirancang secara kaku untuk memproses pemetaan sub-looping data. 
*   Pustaka React Native **menuntut secara mutlak** bahwa array baris anggota harus disimpan di dalam kunci bernama **`data`**. Jika Anda merubah nama kuncinya menjadi nama lain (seperti `anggota` atau `list`), parser SectionList tidak akan bisa mendeteksi lokasi array tersebut, memicu pesan error visual di layar HP Anda.
