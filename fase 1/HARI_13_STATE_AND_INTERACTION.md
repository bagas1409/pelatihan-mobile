# Hari 13: Manajemen State Lokal & Interaksi Reaktif
## Menerapkan React useState Untuk Memanipulasi Angka Counter Distribusi Kantong Darah

Selamat datang di Hari 13! Hari ini kita akan mempelajari konsep **State Lokal** menggunakan React Hook **`useState`** di React Native.

Di Hari 12 kita mempelajari bahwa Props bersifat mati (Read-Only). Untuk membuat halaman interaktif yang datanya bisa bertambah, berkurang, atau merespon ketukan jari petugas secara dinamis, kita harus menggunakan **State**. State adalah memori penyimpanan lokal reaktif yang khusus dimiliki oleh komponen tersebut. Merubah nilai variabel state akan menyuruh React menggambar ulang (*Re-render*) bagian tampilan layar yang terpengaruh.

---

## 1. Konsep useState Hook di React

Sintaks dasar pembuatan state di React:
```typescript
const [namaVariabel, setNamaVariabel] = useState(nilaiAwal);
```
*   `namaVariabel`: Penampung nilai data saat ini.
*   `setNamaVariabel`: Satu-satunya fungsi yang diizinkan untuk mengubah nilai variabel tersebut agar memicu render ulang layar.

---

## 2. Studi Kasus PMI: Penghitung Kantong Darah Keluar (Counter Bag)

Mari kita buat halaman pencatatan logistik sederhana di mana petugas bisa menambah atau mengurangi jumlah kantong darah golongan O yang siap dimuat ke dalam mobil box kurir:

```tsx
// components/CounterKantongDarah.tsx
import React, { useState } from 'react';
import { View, Text, TouchableOpacity } from 'react-native';

export default function CounterKantongDarah() {
  // 1. INISIALISASI STATE (Jumlah awal: 0 kantong)
  const [jumlah, setJumlah] = useState<number>(0);

  const tambahKantong = () => {
    setJumlah(jumlah + 1); // Tambah 1
  };

  const kurangiKantong = () => {
    if (jumlah > 0) {
      setJumlah(jumlah - 1); // Kurangi 1 (batasi agar tidak negatif)
    }
  };

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm max-w-sm mx-auto mt-8 items-center space-y-6">
      
      <View class="items-center space-y-1">
        <Text className="text-sm font-bold text-slate-800">📦 Persiapan Box Distribusi</Text>
        <Text className="text-xs text-slate-400">Golongan Darah O- (Urgent)</Text>
      </View>

      <!-- TAMPILAN ANGKA REAKTIF (Otomatis berubah saat state berubah) -->
      <View className="bg-red-50 w-24 h-24 rounded-full items-center justify-center border border-red-100">
        <Text className="text-3xl font-black text-red-500">{jumlah}</Text>
        <Text className="text-[9px] font-bold text-red-400 uppercase">Bag</Text>
      </View>

      <!-- TOMBOL KENDALI COUNTER -->
      <View className="flex-row space-x-4">
        
        <!-- Tombol Kurang (-) -->
        <TouchableOpacity 
          onPress={kurangiKantong}
          className="w-12 h-12 bg-slate-100 rounded-2xl items-center justify-center active:bg-slate-200"
        >
          <Text className="font-extrabold text-slate-700 text-lg">-</Text>
        </TouchableOpacity>

        <!-- Tombol Tambah (+) -->
        <TouchableOpacity 
          onPress={tambahKantong}
          className="w-12 h-12 bg-red-500 rounded-2xl items-center justify-center active:bg-red-600"
        >
          <Text className="font-extrabold text-white text-lg">+</Text>
        </TouchableOpacity>

      </View>

    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari pemanggilan fungsi `setJumlah(jumlah - 1)` di atas dan mengapa kita dilarang keras menuliskan modifikasi nilai langsung seperti `jumlah = jumlah - 1`?
2. Buka kode `kurangiKantong` di atas. Jelaskan fungsi dari pemeriksaan logika `if (jumlah > 0)` sebelum mengurangi nilai state.
3. Sebutkan kelebihan visual dari penggunaan state reaktif dibandingkan kita memanipulasi teks tulisan secara manual menggunakan perintah DOM JavaScript seperti `document.getElementById('angka').innerText = ...`.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Larangan Mutasi State Langsung
*   **Fungsi Set State**: Fungsi `setJumlah` memberitahu React bahwa data telah berubah dan menjadwalkan proses pembaruan layar (*Re-render*).
*   **Akibat modifikasi langsung**: Menulis `jumlah = jumlah - 1` hanya merubah nilai variabel di memori RAM internal, namun **React tidak tahu bahwa perubahan tersebut terjadi**. Akibatnya, React tidak akan menggambar ulang layar HP, menyebabkan tampilan angka di layar HP petugas tetap macet diam tidak berubah meskipun variabel aslinya sudah berkurang.

### Jawaban 2: Validasi Batas Bawah (Guard Clause)
*   **Pencegahan Data Korup**: Logika `if (jumlah > 0)` mengamankan database agar tidak mencatat data mustahil. 
*   Di dunia nyata, jumlah kantong fisik logistik darah PMI tidak mungkin bernilai negatif (misal `-3` kantong). Pemeriksaan ini menjamin tombol minus tidak akan mengurangi angka jika nilai counter sudah menyentuh angka `0`.

### Jawaban 3: Deklaratif vs Imperatif
*   **Sistem Deklaratif**: React menggunakan sistem deklaratif. Kita cukup menyambungkan `{jumlah}` ke teks JSX. Saat variabel berubah, React yang bertanggung jawab menyinkronkan seluruh tampilan visual di layar HP. 
*   Ini jauh lebih bersih, aman dari bug, dan hemat baris kode dibandingkan sistem imperatif web jadul (`document.getElementById`) yang mengharuskan kita mencari ID elemen secara manual yang rawan salah ketik di lingkungan mobile.
```
[FASE 1: HARI 13 SUKSES]
```
