# Hari 66: Halaman Berhalaman Sisi Client (Pagination)
## Memotong Aliran Data Array Ratusan Baris Menjadi Sepotong Hal-Halaman Terisolir Memanfaatkan Teknik Fungsi Slice() Array JS

Selamat datang di Hari 66! Menyambung materi di Hari 65, kita telah berhasil meringankan beban render visual di layar (RAM). Namun, satu beban masih tertinggal: **Beban unduh Data Payoad Jaringan!**.

Bayangkan API database merespon `/stok` dengan membawa teks panjang string JSON array berjumlah **5.000 anggota** ke sebuah HP ber-sinyal lemah di pelosok hutan. JSON berukuran jumbo (mencapai ratusan Kilobytes) tersebut akan butuh waktu belasan detik mendownload, dan langsung membekukan memori parsial React Native. 
Sebagai dasar sebelum kita menginjak teknik *Infinite Scroll*, hari ini kita akan memilah dan mengkalkulasi pemotongan array data halaman (*Pagination Array Chunks*).

---

## 1. Dua Jenis Konsep Pagination Logis

Dalam ekosistem aplikasi, pagination dibagi 2 jalur pertanggungjawaban:
*   **Server-Side Pagination**: (Opsi Teraman & Teringan). Server backend hanya mengirim array 10 potong baris kepada Mobile App, membatasi bocor kuota. (Contoh URL API: `/relawan?halaman=2&limit=10`).
*   **Client-Side Pagination**: Server mengirim 200 data utuh sekaligus ke HP. HP bertanggung jawab memotong-motongnya secara mandiri ke dalam blok antarmuka 20 tampilan tersembunyi dengan merakit fungsi JavaScript metode `.slice()`.

Hari ini kita menyentuh *Client-Side Pagination* logika JavaScript dasarnya terlebih dahulu.

---

## 2. Studi Kasus PMI: Memotong Halaman Modul Daftar Divisi Relawan KSR

Mari kita bangun layout penampil data relawan dengan dua buah tombol Prev - Next (Sebelumnya / Selanjutnya) di sisi client:

```tsx
// components/LogikaPaginasiKlien.tsx
import React, { useState } from 'react';
import { View, Text, FlatList, TouchableOpacity } from 'react-native';

export default function LogikaPaginasiKlien() {
  // DATA SUMBER DUMMY (Anggap ini array besar 50 baris yang di-download tumpah sekali dari API server)
  const masterDataArrayBesar = Array.from({ length: 50 }, (_, i) => ({
    id: String(i),
    nama: `Anggota PMR Unit ${i + 1}`
  }));

  // STATE LOGIKA PEMOTONG
  const JUMLAH_ITEM_PER_HALAMAN = 7; 
  const [halamanAktif, setHalamanAktif] = useState<number>(1);

  // RUMUS INTI PEMOTONG ARRAY JAVASCRIPT:
  // Jika di hal 1 -> indexStart = 0, indexEnd = 7. (Menarik index baris ke 0 s.d 6)
  // Jika di hal 2 -> indexStart = 7, indexEnd = 14. (Menarik index baris ke 7 s.d 13)
  const indexAwal = (halamanAktif - 1) * JUMLAH_ITEM_PER_HALAMAN;
  const indexAkhir = indexAwal + JUMLAH_ITEM_PER_HALAMAN;
  
  // Array yang digambar di FlatList HANYALAH data sepotong hasil tebasan fungsi slice() ini!
  const arrayTebasanTampil = masterDataArrayBesar.slice(indexAwal, indexAkhir);

  // Kalkulasi max pagination (50 data dibagi 7 = mentok total 8 halaman)
  const totalHalamanMentok = Math.ceil(masterDataArrayBesar.length / JUMLAH_ITEM_PER_HALAMAN);

  // Fungsi Action Tombol Panah Bawah
  const geserMaju = () => {
    if (halamanAktif < totalHalamanMentok) setHalamanAktif(halamanAktif + 1);
  };
  const geserMundur = () => {
    if (halamanAktif > 1) setHalamanAktif(halamanAktif - 1);
  };

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm flex-1 mb-4 space-y-4">
      <Text className="font-extrabold text-slate-800 text-sm">📄 Direktori Pendaftaran (Sisi Client)</Text>

      {/* RENDER HANYA 7 POTONG BARIS KE LAYER LAYAR FLATLIST */}
      <View className="flex-1 min-h-[280px]">
        {arrayTebasanTampil.map((item) => (
          <View key={item.id} className="py-3 border-b border-slate-100 flex-row justify-between">
            <Text className="text-xs font-bold text-slate-700">{item.nama}</Text>
            <Text className="text-[10px] text-slate-400">#IDX_{item.id}</Text>
          </View>
        ))}
      </View>

      {/* PANEL TOMBOL KONTROL PREV - NEXT */}
      <View className="flex-row justify-between items-center bg-slate-50 p-2 rounded-2xl border border-slate-100">
        
        <TouchableOpacity 
          onPress={geserMundur} disabled={halamanAktif === 1}
          className={`px-4 py-2 rounded-xl ${halamanAktif === 1 ? 'opacity-30' : 'bg-red-100 active:bg-red-200'}`}
        >
          <Text className="font-bold text-xs text-red-600">⬅️ Mundur</Text>
        </TouchableOpacity>

        <Text className="text-[10px] font-black text-slate-500 uppercase tracking-widest">
          Hal {halamanAktif} / {totalHalamanMentok}
        </Text>

        <TouchableOpacity 
          onPress={geserMaju} disabled={halamanAktif === totalHalamanMentok}
          className={`px-4 py-2 rounded-xl ${halamanAktif === totalHalamanMentok ? 'opacity-30' : 'bg-red-100 active:bg-red-200'}`}
        >
          <Text className="font-bold text-xs text-red-600">Maju ➡️</Text>
        </TouchableOpacity>

      </View>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa peran fungsi rumus pembantu array JavaScript `.slice(indexAwal, indexAkhir)` pada logika pagination di sisi komputasi Client mobile di atas? Mengapa memotong array sumber yang berukuran 50 baris menjadi array kecil berukuran 7 baris sangat berdampak besar pada peredaman lag komponen loop `.map()`?
2. Buka blok penulisan variabel pembantu fungsi `Math.ceil()`. Jelaskan perhitungan dasar mengapa 50 dibagi 7 menghasilkan angka mentok `8`, bukan `7.14`.
3. Dari segi performa pemborosan koneksi data seluler petugas (*Network Quota Usage*), sebutkan kelemahan utama dari metode Client-Side Pagination dibandingkan dengan arsitektur membagi halaman Server-Side Pagination yang dikerjakan langsung oleh server database Express.js.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Filter Render Tampilan
*   **Pengurangan Loop Rendering**: Mesin loop visual DOM (`.map`) di dalam React sangatlah rewel (lamban) saat harus menggambar 50 pasang tumpukan View, Text, dan properti Flexbox secara mentah dari ujung ke ujung. 
*   Memasukkan array yang sudah dipenggal (dipotong pisau JavaScript Slice) memaksa mesin render UI mengalokasikan tenaga processor-nya untuk fokus menggambar hanya 7 kotak card ke monitor. 43 kotak data sisa tersembunyi dengan aman terpendam di RAM murni, meredam bottleneck frame-rate saat perpindahan layar.

### Jawaban 2: Pembulatan Atas ke Langit-langit Plafon (Ceiling)
*   Fungsi **`Math.ceil(50 / 7)`** bertugas melakukan tindakan matematik *Pembulatan Atas / Ceiling*. 
*   Meskipun nilai aslinya `7.14`, sistem wajib menciptakan 1 slot halaman penampung wadah utuh ke-8 demi meletakkan sisa baris anggota (yang koma tidak genap 1 buah baris sisa) di dalamnya. Jika dibulatkan menggunakan `round()` normal, halaman ke-8 tidak tercipta, data relawan sisa hilang (*Missing Data Bug*).

### Jawaban 3: Pemborosan Lebar Pita Unduh Awal (Initial Loading Spike)
*   **Ledakan Unduhan Palsu**: Client-side pagination di mobile sangat memboroskan kuota data pulsa. 
*   Bila petugas hanya sekadar iseng melihat Relawan urutan halaman pertama (1 s.d 10), HP *toh* secara bodoh tetap diwajibkan men-download sisa relawan ke-11 sampai ke-5.000 memakan 3 Megabyte memori data internet dalam satu tarikan JSON API saat detakan layer pertama mount terbuka! Jauh lebih efisien menembak URL Backend dengan limit di Server-Side sehingga paket unduhan mendarat tidak lebih dari 5 Kilobyte data kecil setiap kalinya.
```
[FASE 4: HARI 66 SUKSES]
```
