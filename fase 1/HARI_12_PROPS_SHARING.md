# Hari 12: Konsep Props Lintas Komponen
## Membuat Komponen UI Kartu Riwayat Donor Yang Reusable Dengan Pengamanan Tipe Data TypeScript Props

Selamat datang di Hari 12! Hari ini kita akan mempelajari cara berbagi data dari komponen induk (*Parent*) ke komponen anak (*Child*) menggunakan **Props** dengan pengamanan tipe data **TypeScript**.

Saat merancang aplikasi DonorKu PMI, kita seringkali menggambar komponen visual yang sama berulang kali (seperti kartu nama relawan). Dibandingkan menulis ulang struktur HTML-nya 10 kali di file berbeda, kita akan memisahkan struktur kartu tersebut ke dalam file komponen mandiri, lalu menyuplai data isian dinamisnya lewat properti parameter (Props) agar hemat kode (*Reusable Component*).

---

## 1. Apa itu Props di React Native?

*   **Props (Properties)**: Parameter input yang dikirimkan oleh komponen pembungkus luar ke dalam komponen anak. Sifat data Props adalah **Read-Only (Hanya Bisa Dibaca)**, komponen anak dilarang keras merubah isi nilai data props secara sepihak demi kestabilan alur data satu arah (*Uni-directional Data Flow*).
*   **TypeScript Interface**: Kita wajib mendeklarasikan jenis tipe data properti yang kita izinkan (misal: properti `nama` wajib bertipe `string`, properti `jumlah` wajib bertipe `number`) untuk menghindari error salah input tipe data saat menulis kode.

---

## 2. Studi Kasus PMI: Komponen Kartu Riwayat Donor Mandiri

### Langkah 1: Buat Komponen Anak (`components/KartuRiwayat.tsx`)

```tsx
// components/KartuRiwayat.tsx
import React from 'react';
import { View, Text } from 'react-native';

// 1. DEKLARASIKAN TIPE DATA PROPS MENGGUNAKAN TYPESCRIPT
interface KartuRiwayatProps {
  namaRs: string;      // Wajib String
  golonganDarah: string; // Wajib String
  jumlahBag: number;   // Wajib Angka (Number)
  tanggalKirim: string; // Wajib String
}

// 2. TERIMA PROPS DI PARAMETER FUNGSI
export default function KartuRiwayat({ namaRs, golonganDarah, jumlahBag, tanggalKirim }: KartuRiwayatProps) {
  return (
    <View className="bg-white p-5 rounded-3xl border border-slate-100 flex-row justify-between items-center shadow-sm mb-4">
      <View className="space-y-1">
        <Text className="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Tujuan Rumah Sakit</Text>
        <Text className="font-extrabold text-slate-800 text-sm">{namaRs}</Text>
        <Text className="text-[9px] text-slate-400">Dikirim pada: {tanggalKirim}</Text>
      </View>

      <View className="items-end space-y-1">
        <!-- Badge Bulat Golongan Darah -->
        <View className="w-10 h-10 rounded-full bg-red-100 items-center justify-center border border-red-200">
          <Text className="font-black text-red-600 text-xs">{golonganDarah}</Text>
        </View>
        <Text className="text-[10px] font-bold text-slate-500">{jumlahBag} Kantong</Text>
      </View>
    </View>
  );
}
```

---

### Langkah 2: Panggil Komponen di Halaman Induk (`app/riwayat/index.tsx`)

Komponen induk tinggal memanggil komponen di atas berkali-kali dengan menyuplai nilai data yang berbeda:

```tsx
// app/riwayat/index.tsx
import React from 'react';
import { ScrollView, Text } from 'react-native';
import KartuRiwayat from '../../components/KartuRiwayat';

export default function HalamanRiwayat() {
  return (
    <ScrollView className="flex-1 bg-slate-50 p-6">
      <Text className="text-base font-black text-slate-800 mb-6">📦 Riwayat Pengiriman Logistik CITO</Text>

      <!-- Menggunakan kembali komponen KartuRiwayat dengan props berbeda -->
      <KartuRiwayat 
        namaRs="RSU Abdul Moeloek" 
        golonganDarah="O" 
        jumlahBag={5} 
        tanggalKirim="22 Juni 2026" 
      />

      <KartuRiwayat 
        namaRs="RS Urip Sumoharjo" 
        golonganDarah="AB" 
        jumlahBag={2} 
        tanggalKirim="23 Juni 2026" 
      />
    </ScrollView>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Amati deklarasi properti: `jumlahBag: number` pada berkas `KartuRiwayat.tsx` di atas.
2. Apa yang akan dilakukan oleh editor VS Code jika pada halaman induk kita tidak sengaja menyuplai nilai berupa teks string: `jumlahBag="Lima"`? Hubungkan dengan keuntungan menggunakan TypeScript.
3. Mengapa di dalam arsitektur React Native komponen anak dilarang keras mengubah secara langsung nilai properti yang diterimanya dari komponen induk?

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Proteksi compiler TypeScript
*   **Peringatan Error Instan**: VS Code akan langsung memunculkan **garis bawah bergelombang merah** pada baris properti `jumlahBag` dan menampilkan pesan error compiler: `Type 'string' is not assignable to type 'number'`.
*   **Keuntungannya**: Kesalahan ketik atau salah kirim data tipe data dari API bisa dideteksi langsung saat kita menulis kode (*Compile-time Error*), mencegah terjadinya crash bug tak terduga saat aplikasi sudah diinstal di HP petugas lapangan.

### Jawaban 3: Aliran Data Satu Arah (Uni-directional Data Flow)
*   **Kestabilan State**: Jika komponen anak diizinkan merubah nilai props secara sepihak, komponen induk tidak akan menyadari perubahan tersebut, mengakibatkan ketidakselarasan data (*State Desynchronization*) di layar lain. 
*   Jika komponen anak butuh memicu perubahan data di atasnya, cara yang benar adalah komponen induk mengirimkan fungsi callback (Event Emits / Callback Function) via props agar komponen induk yang memproses perubahannya secara mandiri di pusat state.
