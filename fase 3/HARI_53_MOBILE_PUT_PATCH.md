# Hari 53: Integrasi PUT/PATCH API - Perubahan Status
## Memperbarui Data Parsial Stok Darah Menggunakan Axios PATCH Dan Mengelola Feedback Loading Indikator

Selamat datang di Hari 53! Hari ini kita akan mempelajari cara mengirim data pembaruan parsial menggunakan metode **Axios PATCH** atau **PUT**.

Di aplikasi DonorKu PMI Lampung, ketika petugas ingin menyetujui kehadiran relawan donor di lapangan, atau ingin mengubah status kesiapan stok darah secara cepat, kita tidak perlu mengirimkan seluruh data objek relawan yang utuh. Kita cukup mengirimkan parameter status yang berubah saja (misal: *mengubah status 'Pending' menjadi 'Selesai'*) menggunakan metode `PATCH` agar menghemat lalu lintas data server backend.

---

## 1. Perbedaan Utama: PUT vs PATCH

*   **`PUT`**: Digunakan untuk **Mengganti secara total** satu baris data database dengan objek baru. Jika kita lupa mengirimkan kolom `nama`, server backend yang kaku bisa tidak sengaja mengosongkan kolom nama tersebut.
*   **`PATCH`**: Digunakan untuk **Modifikasi sebagian (Parsial)**. Server hanya merubah kolom yang disuplai di dalam body request, sedangkan kolom lain tetap utuh aman terjaga di database.

---

## 2. Studi Kasus PMI: Konfirmasi Kehadiran Relawan (Status Toggle)

Mari kita buat komponen baris relawan dengan tombol konfirmasi kehadiran cepat reaktif di berkas **`components/BarisKonfirmasiRelawan.tsx`**:

```tsx
// components/BarisKonfirmasiRelawan.tsx
import React, { useState } from 'react';
import { View, Text, TouchableOpacity, ActivityIndicator, Alert } from 'react-native';
import api from '../utils/api';

interface RelawanProps {
  id: string;
  nama: string;
  statusHadirInitial: boolean;
}

export default function BarisKonfirmasiRelawan({ id, nama, statusHadirInitial }: RelawanProps) {
  const [hadir, setHadir] = useState<boolean>(statusHadirInitial);
  const [loading, setLoading] = useState<boolean>(false);

  // FUNGSI UTAMA TRIGGER AXIOS PATCH
  const ubahStatusKehadiran = async () => {
    setLoading(true);
    const targetStatus = !hadir; // Balik status boolean saat ini
    
    try {
      console.log(`[AxiosPATCH] Merubah status relawan ${id} menjadi:`, targetStatus);
      
      // Tembak API PATCH ke endpoint spesifik relawan
      const respon = await api.patch(`/relawan/${id}`, {
        statusKehadiran: targetStatus // Hanya kirim satu properti parsial!
      });

      if (respon.status === 200) {
        setHadir(targetStatus); // Sukses -> Update status visual di HP!
        Alert.alert('✓ Berhasil', `Status kehadiran ${nama} sukses diperbarui.`);
      }
    } catch (error: any) {
      console.error("[AxiosPATCH Error]:", error);
      Alert.alert('🚨 Gagal Update', error.message || 'Server menolak perubahan.');
    } finally {
      setLoading(false);
    }
  };

  return (
    <View className="bg-white p-5 rounded-3xl border border-slate-100 flex-row justify-between items-center mb-3 shadow-sm">
      <View className="space-y-1">
        <Text className="font-extrabold text-slate-800 text-xs">{nama}</Text>
        <Text className="text-[9px] text-slate-400">ID Anggota: {id}</Text>
      </View>

      {/* Tombol Toggle Aksi */}
      <TouchableOpacity 
        onPress={ubahStatusKehadiran}
        disabled={loading}
        className={`px-4 py-2.5 rounded-2xl items-center justify-center flex-row space-x-1.5 ${
          hadir 
            ? 'bg-emerald-50 border border-emerald-100' // Visual hijau jika hadir
            : 'bg-red-500 shadow-sm' // Visual merah jika belum hadir
        }`}
      >
        {loading && <ActivityIndicator size="small" color={hadir ? '#10b981' : '#ffffff'} />}
        
        <Text className={`text-[10px] font-extrabold uppercase tracking-wide ${
          hadir ? 'text-emerald-500' : 'text-white'
        }`}>
          {loading ? 'Proses...' : hadir ? '✓ HADIR' : 'ABSEN'}
        </Text>
      </TouchableOpacity>

    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari perintah `api.patch(...)` pada kode di atas? Mengapa metode PATCH lebih disukai untuk kasus edit satu kolom status dibandingkan menggunakan metode PUT?
2. Buka kode `ubahStatusKehadiran` di atas. Jelaskan kegunaan parameter `disabled={loading}` pada komponen `TouchableOpacity` tombol aksi.
3. Sebutkan nama status respon kode angka HTTP sukses umum yang dikembalikan server backend untuk perubahan data PATCH.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Efisiensi Data Parsial (PATCH vs PUT)
*   **Hemat Bandwidth**: Metode `PATCH` hanya mengirimkan payload minimal (contoh: `{ statusKehadiran: true }` berukuran kurang dari 50 byte). 
*   Jika menggunakan `PUT`, kita terpaksa menyertakan data utuh (nama, NIK, alamat, email) yang memboroskan kuota internet HP petugas serta membebani server backend untuk memproses query pencocokan kolom yang tidak berubah.

### Jawaban 2: Pencegahan Request Ganda (Debouncing Click)
*   **Penyelamat Database**: Parameter `disabled={loading}` menonaktifkan kemampuan klik tombol selama Axios sedang menunggu respon dari server API. 
*   Ini mencegah petugas menekan tombol secara liar bertubi-tubi saat koneksi lambat, yang bisa melahirkan antrean request beruntun yang merusak konsistensi data database di server.

### Jawaban 3: HTTP Status Code 200
*   Kode status tersebut adalah **`200 OK`**.
```
[FASE 3: HARI 53 SUKSES]
```
