# Hari 22: Membaca & Mengirim Parameter Rute
## Mengirim Parameter Kueri (Search Params) Lintas Layar Menggunakan Object Href Dinamis

Selamat datang di Hari 22! Hari ini kita akan memperdalam kemampuan komunikasi data antar halaman dengan mengirimkan **Parameter Kueri** (Query / Search Parameters) di Expo Router.

Di Hari 21 kita mempelajari cara mengirim 1 variabel ID via rute path dinamis. Namun bagaimana jika kita butuh mengirimkan beberapa data sekaligus saat pindah halaman (seperti: *mengirim nama relawan, golongan darah, dan asal wilayah dari halaman daftar langsung ke halaman edit*) tanpa harus memukul API backend berulang kali? Kita bisa menyelundupkan data tersebut di dalam parameter kueri URL (contoh: `/edit?nama=budi&gol=O`).

---

## 1. Konsep Objek Href Dinamis

Expo Router mempermudah penulisan parameter kueri yang panjang menggunakan format **Objek Href**. Dibandingkan menulis string manual yang rawan salah format seperti `/edit?nama=${nama}&gol=${gol}`, kita bisa menyusunnya sebagai objek JavaScript yang terstruktur:

```typescript
router.push({
  pathname: "/edit",
  params: { 
    nama: "Budi Santoso", 
    gol: "O" 
  }
});
```

---

## 2. Studi Kasus PMI: Halaman Edit Relawan dengan Parameter Kueri

Mari kita buat halaman edit di `app/relawan/edit.tsx` yang bertugas menangkap dan memajang beberapa parameter kueri sekaligus.

### Berkas Halaman: `app/relawan/edit.tsx`
```tsx
// app/relawan/edit.tsx
import React from 'react';
import { View, Text } from 'react-native';
import { useLocalSearchParams } from 'expo-router';

export default function HalamanEditRelawan() {
  
  // 1. TANGKAP BEBERAPA PARAMETER SEKALIGUS DARI URL
  const { nama, gol, wilayah } = useLocalSearchParams<{ 
    nama: string; 
    gol: string; 
    wilayah: string; 
  }>();

  return (
    <View className="flex-1 bg-slate-50 p-6 justify-center">
      <View className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4">
        
        <Text className="font-extrabold text-slate-800 text-sm">✏️ Edit Informasi Anggota</Text>
        
        <View className="space-y-2 border-t pt-4 border-slate-100 text-xs">
          <!-- Tampilkan data kiriman lintas halaman -->
          <Text className="text-slate-500">Nama Terdaftar: <Text className="font-bold text-slate-800">{nama}</Text></Text>
          <Text className="text-slate-500">Golongan Darah: <Text className="font-bold text-red-500">{gol}</Text></Text>
          <Text className="text-slate-500">Wilayah Dinas: <Text className="font-bold text-slate-800">{wilayah}</Text></Text>
        </View>

      </View>
    </View>
  );
}
```

---

## 3. Cara Mengirim Data dari Halaman Asal

Di halaman daftar relawan, ketika petugas mengklik tombol "Edit", pemicu navigasi objek dijalankan:

```tsx
const navigasiKeEdit = () => {
  router.push({
    pathname: '/relawan/edit',
    params: {
      nama: 'Budi Santoso',
      gol: 'O',
      wilayah: 'Kota Metro'
    }
  });
};
```

---

## 4. Latihan Soal Mandiri
1. Amati parameter properti `params` pada fungsi `navigasiKeEdit` di atas.
2. Jelaskan perbedaan cara kerja transmisi data antara menggunakan **Path Parameters** (Hari 21, `/relawan/101`) dengan menggunakan **Query Parameters** (Hari 22, `/relawan/edit?nama=budi`) dari segi fleksibilitas jumlah data yang bisa dikirim.
3. Apa yang terjadi jika nilai parameter mengandung spasi (seperti nama `"Budi Santoso"`)? Bagaimana Expo Router mengamankan spasi tersebut saat dikirim melalui URL rute?

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Path Params vs Query Params
*   **Path Params (`/relawan/:id`)**: Kaku dan terikat hierarki rute folder fisik. Hanya cocok untuk mengirim 1 penunjuk identitas utama (seperti ID atau NIK).
*   **Query Params (`/relawan/edit?key=value`)**: Sangat fleksibel. Kita bisa menyisipkan puluhan properti data opsional tambahan secara bebas tanpa memerlukan konfigurasi folder fisik kurung siku baru, menjadikannya sarana bertukar data sementara yang sangat praktis di sisi client.

### Jawaban 2: URL Encoding Otomatis
*   **Aman Terenkripsi**: Expo Router di balik layar akan otomatis menjalankan fungsi **`encodeURIComponent()`** pada seluruh data parameter. 
*   String `"Budi Santoso"` akan diubah formatnya menjadi `"Budi%20Santoso"` agar aman saat mengalir di mesin rute browser/perangkat mobile tanpa memicu error pemisah spasi kosong, lalu diterjemahkan kembali (*Decode*) menjadi teks normal saat ditangkap oleh hook `useLocalSearchParams()`.
