# Hari 28: Mencegah Kebocoran Memori (Memory Leaks)
## Memahami Cara Mematikan Interval Dan Listener Secara Bersih Pada Fungsi Cleanup Return useEffect

Selamat datang di Hari 28! Hari ini kita akan mempelajari topik krusial bagi performa jangka panjang aplikasi mobile: **Memory Leaks** (Kebocoran Memori RAM).

Pada portal DonorKu PMI Lampung, kita memiliki fitur krusial seperti polling pengecekan panggilan darurat CITO setiap 10 detik. Jika petugas memicu fungsi interval waktu tersebut di halaman Beranda, lalu berpindah halaman tanpa mematikan mesin intervalnya, **proses timer tersebut akan tetap berjalan terus-menerus di latar belakang memori RAM**. 

Seiring berjalannya waktu, timer yang bocor ini akan memakan resource CPU HP, membuat aplikasi melambat lambat (*Lagging*), dan akhirnya memicu crash force close dari sistem Android/iOS. Kita akan belajar cara menjinakkannya.

---

## 1. Bagaimana Kebocoran Memori Terjadi di Mobile?

*   **Penyebab**: Menyalakan proses asinkron yang berjalan terus menerus (seperti `setInterval`, `setTimeout`, Geolocation Tracking, atau Event Listener) namun lupa membersihkannya ketika komponen visualnya dihancurkan/ditutup dari layar (*Unmounted*).
*   **Solusi**: Memanfaatkan **Fungsi Cleanup** (Blok `return () => { ... }`) di dalam hook `useEffect` untuk menyetop paksa seluruh proses asinkron tersebut tepat sebelum halaman ditutup.

---

## 2. Studi Kasus PMI: Polling Darurat CITO Aman Terkendali

Mari kita rancang polling berulang yang aman dari kebocoran memori di file **`components/MonitorCitoSafe.tsx`**:

```tsx
// components/MonitorCitoSafe.tsx
import React, { useState, useEffect } from 'react';
import { View, Text } from 'react-native';

export default function MonitorCitoSafe() {
  const [waktuTerakhir, setWaktuTerakhir] = useState<string>('Belum di-update');

  useEffect(() => {
    console.log("[SafeInterval] Sensor monitoring dinyalakan.");

    // 1. JALANKAN POLLING BERULANG SETIAP 5 DETIK
    const intervalCito = setInterval(() => {
      const jamSekarang = new Date().toLocaleTimeString();
      setWaktuTerakhir(jamSekarang);
      console.log("[SafeInterval] Memeriksa database CITO baru pada jam:", jamSekarang);
    }, 5000);

    // 2. FUNGSI CLEANUP: WAJIB DI-RETURN UNTUK DIBERSIHKAN SAAT UNMOUNT
    return () => {
      console.log("[SafeInterval] Komponen ditutup. MEMBUNUH INTERVAL POLLING!");
      
      // Matikan interval agar tidak terus berputar memakan baterai HP!
      clearInterval(intervalCito);
    };
  }, []); // Array kosong menjamin interval disetup hanya sekali di awal

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-100 shadow-sm items-center space-y-2">
      <Text className="text-[10px] font-bold text-red-500 uppercase tracking-widest">🚨 Radar Panggilan CITO</Text>
      <Text className="text-xs text-slate-500">Pengecekan server aktif setiap 5 detik.</Text>
      <Text className="text-xs font-semibold text-slate-800">Update Terakhir: {waktuTerakhir}</Text>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Amati blok kode pembersih `clearInterval(intervalCito)` di dalam return `useEffect` di atas.
2. Apa akibatnya jika kita lupa menuliskan fungsi `clearInterval` tersebut ketika petugas menutup halaman monitoring CITO dan berpindah ke halaman edit profil? Jelaskan status CPU HP petugas.
3. Sebutkan nama 3 fungsi asinkron/sensor bawaan mobile yang wajib dibersihkan menggunakan fungsi cleanup return `useEffect` selain `setInterval`.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Analisis Kebocoran Memori
*   **Akibat Kebocoran**: Proses `setInterval` akan terus-menerus berjalan di memori RAM latar belakang meskipun tampilan visualnya sudah tidak ada di layar HP. 
*   **Status CPU HP**: CPU HP akan terus terbebani untuk mengeksekusi panggilan fungsi log jam setiap 5 detik. 
*   Jika petugas membuka dan menutup halaman tersebut sebanyak 10 kali, maka akan tercipta **10 proses interval paralel yang bocor dan berjalan bersamaan**, menyebabkan suhu handphone petugas memanas cepat, konsumsi baterai boros, dan kinerja RAM melambat drastis.

### Jawaban 3: Proses Asinkron Wajib Cleanup
*   Tiga hal utama yang wajib di-cleanup:
    1.  **`clearTimeout(timerId)`**: Untuk membatalkan antrean eksekusi penundaan waktu.
    2.  **`Geolocation.clearWatch(watchId)`**: Untuk menyetop pelacakan koordinat GPS fisik HP yang memakan daya baterai sangat tinggi.
    3.  **`EventEventEmitter.remove()` / `BackHandler.remove()`**: Untuk mencabut detektor sensor input fisik HP dari RAM global.
```
[FASE 2: HARI 28 SUKSES]
```
