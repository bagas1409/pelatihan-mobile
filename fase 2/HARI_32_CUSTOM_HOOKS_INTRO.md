# Hari 32: Pengenalan Custom Hooks Dasar
## Memilah Logika Penghitungan Masa Kadaluarsa Kantong Darah Dari Tampilan UI Menggunakan Custom Hooks

Selamat datang di Hari 32! Hari ini kita akan mempelajari cara merancang **Custom Hooks** dasar di React Native.

Di Fase 3 (Hari 49) kita belajar bahwa Custom Composables digunakan untuk membagi logika fungsi yang sama di web. Di dunia React, kita menyebut konsep ini sebagai **Custom Hooks** (fungsi kustom yang memanfaatkan React Hooks internal seperti `useState` atau `useEffect`). 

Kita akan mempraktikkan cara memisahkan rumus matematika perhitungan sisa hari kadaluarsa kantong darah keluar dari file visual UI agar file visual kita bersih dan mudah dirawat.

---

## 1. Apa itu Custom Hooks?

*   **Fungsi Khusus**: Custom Hooks adalah fungsi JavaScript biasa yang diawali dengan kata **`use`** (misal: `useSisaHari`). 
*   **Keunggulan**: Memungkinkan kita membungkus logika state reaktif ke dalam fungsi tersendiri dan membagikannya ke banyak komponen visual yang berbeda secara instan tanpa menulis ulang kodenya.

---

## 2. Studi Kasus PMI: Custom Hook Penghitung Kadaluarsa Darah

### Langkah 1: Buat Berkas Custom Hook (`hooks/useSisaHari.ts`)

```typescript
// hooks/useSisaHari.ts
import { useState, useEffect } from 'react';

// Input menerima tanggal expired bertipe string (contoh: "2026-06-25")
export function useSisaHari(tanggalKadaluarsa: string) {
  const [sisaHari, setSisaHari] = useState<number>(0);
  const [statusAman, setStatusAman] = useState<boolean>(true);

  useEffect(() => {
    const hitungSelisih = () => {
      const tglSkrg = new Date();
      const tglExp = new Date(tanggalKadaluarsa);
      
      // Rumus selisih hari: Milidetik -> Hari
      const selisihWaktu = tglExp.getTime() - tglSkrg.getTime();
      const selisihHari = Math.ceil(selisihWaktu / (1000 * 3600 * 24));

      setSisaHari(selisihHari);
      setStatusAman(selisihHari > 5); // Darah kritis jika sisa hari di bawah 5 hari!
    };

    hitungSelisih();
  }, [tanggalKadaluarsa]);

  // Kembalikan objek state yang reaktif agar bisa digunakan oleh UI
  return { sisaHari, statusAman };
}
```

---

### Langkah 2: Konsumsi Custom Hook di Komponen UI (`components/StatusDarah.tsx`)

```tsx
// components/StatusDarah.tsx
import React from 'react';
import { View, Text } from 'react-native';
// Impor custom hook buatan kita
import { useSisaHari } from '../hooks/useSisaHari';

export default function StatusDarah() {
  // Panggil custom hook, kirim tanggal expired logistik
  const { sisaHari, statusAman } = useSisaHari('2026-06-30');

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-100 items-center space-y-3">
      <Text className="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Sisa Umur Sel Darah</Text>
      
      <Text className="text-xl font-black text-slate-800">
        {sisaHari > 0 ? `${sisaHari} Hari Lagi` : 'Kadaluarsa!'}
      </Text>

      {/* Gunakan variabel boolean statusAman dari custom hook untuk mewarnai UI */}
      <View className={`px-3 py-1 rounded-full border ${statusAman ? 'bg-emerald-50 border-emerald-100' : 'bg-red-50 border-red-100'}`}>
        <Text className={`text-[9px] font-bold uppercase ${statusAman ? 'text-emerald-500' : 'text-red-500'}`}>
          {statusAman ? 'Layak Transfusi' : 'Kritis / Expired'}
        </Text>
      </View>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Sebutkan aturan penamaan penulisan awal nama fungsi (*Naming Convention*) agar suatu fungsi JavaScript dikenali sebagai Custom Hooks oleh compiler React.
2. Buka kode `hooks/useSisaHari.ts` di atas. Jelaskan kegunaan nilai pengembalian `return { sisaHari, statusAman };` bagi komponen visual yang memanggilnya.
3. Apa kelebihan pemisahan file logika bisnis (Business Logic) menggunakan Custom Hooks dari segi kemudahan pengujian program (*Unit Testing*)?

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Aturan Kata "use"
*   **Wajib Awalan use**: Nama fungsi wajib diawali dengan kata **`use`** dengan huruf kecil (contoh: `useSisaHari`, `useApi`, `useForm`). 
*   Penamaan ini merupakan standar deteksi sistem React agar compiler linter tahu bahwa fungsi tersebut diperbolehkan memanggil React Hooks internal (seperti `useState` / `useEffect`) di dalam badannya. Jika Anda menamakannya `hitungSisaHari` tanpa awalan use, compiler React akan memunculkan error penulisan.

### Jawaban 2: Konsumsi Properti Reaktif
*   **Pembagian State**: Baris `return` mengirimkan variabel state `sisaHari` dan `statusAman` keluar. 
*   Komponen visual mana saja yang memanggil hook tersebut kini bisa langsung memanfaatkan nilai reaktif tersebut untuk merubah warna background kartu tanpa perlu memahami rumus matematika kalkulasi milidetik di dalamnya.

### Jawaban 3: Pengujian Terisolasi (Separation of Concerns)
*   **Tes Tanpa UI**: Kita bisa menguji keakuratan rumus perhitungan sisa hari kadaluarsa darah cukup dengan mengetes file `useSisaHari.ts` saja menggunakan tools penguji (seperti Jest) secara terisolasi tanpa perlu repot menggambar/merender komponen visual layout Android/iOS di layar simulator, mempercepat deteksi bug logika.
```
[FASE 2: HARI 32 SUKSES]
```
