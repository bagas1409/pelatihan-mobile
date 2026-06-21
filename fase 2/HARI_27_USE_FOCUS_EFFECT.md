# Hari 27: Siklus Navigasi Aktif (useFocusEffect)
## Memicu Refresh Data API Secara Otomatis Setiap Kali Halaman Selesai Dipindahkan & Aktif Dilihat Pengguna

Selamat datang di Hari 27! Hari ini kita akan mempelajari hook navigasi tingkat lanjut yang sangat penting di dunia mobile development: **`useFocusEffect`**.

Di Hari 26 kita belajar menggunakan `useEffect` dengan array `[]` (seperti `onMounted`). Namun, di dalam aplikasi mobile dengan sistem navigasi Stack/Tabs, ketika petugas berpindah halaman (misalnya dari halaman Daftar Relawan pergi ke halaman Detail, lalu menekan tombol back kembali ke halaman Daftar), halaman Daftar **tidak pernah di-Unmount (tidak dihancurkan)**. Halaman tersebut hanya tersembunyi di bawah tumpukan. 
Akibatnya, **`useEffect` tidak akan berjalan kembali** saat petugas kembali ke halaman Daftar, menyebabkan data stok darah yang tertera di layar tidak ter-update. Solusinya adalah menggunakan `useFocusEffect`.

---

## 1. Perbedaan: useEffect vs useFocusEffect

*   **`useEffect` (React Core)**: Hanya berjalan sekali saat komponen secara fisik dibuat di memori RAM pertama kali (*Mounted*).
*   **`useFocusEffect` (Expo Router / Navigation)**: Berjalan **setiap kali halaman tersebut aktif tampil di depan mata pengguna** (*Focused*), termasuk ketika halaman tersebut muncul kembali setelah ditimpa halaman lain.

---

## 2. Studi Kasus PMI: Refresh Otomatis Daftar Pengiriman CITO

Kita harus memastikan daftar antrean pengiriman CITO di halaman beranda selalu segar setiap kali petugas kembali dari halaman detail konfirmasi.

```tsx
// app/(tabs)/cito.tsx
import React, { useState, useCallback } from 'react';
import { View, Text, FlatList } from 'react-native';
// Impor useFocusEffect dari expo-router
import { useFocusEffect } from 'expo-router';

export default function DaftarCitoAktif() {
  const [dataCito, setDataCito] = useState<string[]>([]);

  // 1. EKSEKUSI USEFOCUSEFFECT
  // Wajib dibungkus useCallback agar fungsi tidak memicu render loop tak berguna
  useFocusEffect(
    useCallback(() => {
      console.log("[useFocusEffect] Halaman CITO aktif difokuskan! Refreshing data API...");
      
      // Simulasi mengambil data terbaru dari server
      const dataTerbaru = ['Kirim RSU Abdul Moeloek - Urgent', 'Kirim RS Urip Sumoharjo - Standby'];
      setDataCito(dataTerbaru);

      // CLEANUP CALLBACK (Dijalankan saat halaman kehilangan fokus / ditinggalkan petugas)
      return () => {
        console.log("[useFocusEffect] Halaman CITO ditinggalkan. Hibernasi aktif.");
      };
    }, [])
  );

  return (
    <View className="flex-1 bg-slate-50 p-6">
      <Text className="text-base font-black text-slate-800 mb-6">🚨 Antrean Distribusi CITO</Text>

      <FlatList
        data={dataCito}
        renderItem={({ item }) => (
          <View className="bg-white p-5 rounded-3xl border border-slate-100 mb-4 shadow-sm">
            <Text className="text-xs font-bold text-slate-700">{item}</Text>
          </View>
        )}
        keyExtractor={(item, index) => index.toString()}
      />
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Buka kode `useFocusEffect` di atas. Mengapa kita wajib membungkus callback di dalam fungsi **`useCallback(() => { ... }, [])`** bawaan React?
2. Jelaskan perbedaan perilaku refresh data antara menekan tombol back dari halaman detail ke halaman utama jika kita menggunakan `useEffect` biasa dibandingkan menggunakan `useFocusEffect` di perangkat mobile.
3. Sebutkan nama modul library pembawa hook `useFocusEffect` pada Expo Router.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Mencegah Loop Fungsi useCallback
*   **Pengamanan Memory Address**: React mendeteksi perubahan fungsi pada setiap render ulang. 
*   Jika kita tidak membungkus callback dengan `useCallback`, sistem navigasi akan menganggap ada fungsi refresh baru yang diciptakan pada setiap milidetik render, menyebabkan `useFocusEffect` dieksekusi terus menerus secara tidak sengaja, memicu **Infinite Rendering Loop** yang menghabiskan daya baterai HP. `useCallback` menjamin referensi fungsi tetap sama selama array dependensi `[]`-nya kosong.

### Jawaban 2: Perilaku Refresh Lintas Halaman
*   **Jika memakai `useEffect`**: Data daftar stok darah **tetap bernilai lama (stale data)** dan tidak ter-refresh saat petugas menekan tombol kembali dari halaman detail. Petugas harus menutup aplikasi secara paksa dan membukanya kembali dari awal agar data termuat baru.
*   **Jika memakai `useFocusEffect`**: Detik pertama halaman daftar muncul kembali di depan layar pasca tombol back ditekan, data stok darah **otomatis ter-refresh memanggil API terbaru** secara instan tanpa perlu menutup aplikasi terlebih dahulu, menjamin akurasi info medis.

### Jawaban 3: Paket Pengimpor
*   Kita bisa mengimpornya langsung dari pustaka **`expo-router`** (yang secara internal mengekspor helper dari `@react-navigation/native`).
```
[FASE 2: HARI 27 SUKSES]
```
