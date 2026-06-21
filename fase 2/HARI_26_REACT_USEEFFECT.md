# Hari 26: Siklus Hidup - React `useEffect`
## Memahami Cara Kerja Pengambilan Data Awal Menggunakan Hook useEffect & Kunci Array Dependensi []

Selamat datang di Hari 26! Hari ini kita akan mempelajari siklus hidup komponen React melalui hook paling mendasar: **`useEffect`**.

Sama halnya dengan Vue 3 yang memiliki siklus hidup seperti `onMounted` yang kita pelajari di Hari 58, di React kita mengontrol siklus penayangan komponen menggunakan hook `useEffect`. Kita harus menguasai hook ini agar bisa memerintahkan aplikasi HP mengambil data daftar golongan darah dari server backend tepat pada detik pertama layar dipasang (*Mounted*) di monitor HP petugas.

---

## 1. Tiga Variasi Siklus Hidup `useEffect`

Cara kerja `useEffect` dikendalikan sepenuhnya oleh parameter kedua yaitu **Array Dependensi (`[]`)**:

### A. Lifecycle Mounted (Hanya Sekali Saat Layar Dibuka)
Sangat cocok untuk mengambil data dari server API. Kita memasukkan array kosong `[]` sebagai parameter kedua.
```typescript
useEffect(() => {
  console.log("Halaman dibuka pertama kali. Tembak API...");
}, []); // Array kosong menjamin fungsi hanya dieksekusi sekali.
```

### B. Lifecycle Update (Berjalan Setiap Ada Perubahan Variabel)
Berjalan di awal, dan berjalan kembali setiap kali isi variabel reaktif yang didaftarkan mengalami perubahan nilai.
```typescript
useEffect(() => {
  console.log("Variabel filter berubah! Tembak ulang API...");
}, [filterGolongan]); // Berputar otomatis jika filterGolongan berubah.
```

### C. Lifecycle Unmount (Pembersihan Siklus saat Layar Ditutup)
Menggunakan blok `return () => { ... }` untuk mematikan sensor/timer saat petugas keluar dari halaman.
```typescript
useEffect(() => {
  return () => {
    console.log("Petugas menutup halaman. Bersihkan memori.");
  };
}, []);
```

---

## 2. Studi Kasus PMI: Mengambil Stok Darah Awal Saat Halaman Dibuka

Mari kita rakit contoh fungsional pemanggilan data awal simulasi di dalam berkas halaman **`app/stok/index.tsx`**:

```tsx
// app/stok/index.tsx
import React, { useState, useEffect } from 'react';
import { View, Text, ActivityIndicator } from 'react-native';

export default function DaftarStokDinas() {
  const [loading, setLoading] = useState(true);
  const [stok, setStok] = useState<number>(0);

  // LIFECYCLE MOUNTED: Ambil data stok awal dari server
  useEffect(() => {
    console.log("[Lifecycle] Halaman stok dipasang. Memulai fetching...");
    
    // Simulasi jeda waktu download internet 1.5 detik
    const ambilDataAPI = setTimeout(() => {
      setStok(85); // Set data stok hasil download
      setLoading(false); // Matikan layar loading
      console.log("[Lifecycle] Fetching sukses. Data terpasang.");
    }, 1500);

    // CLEANUP: Batalkan timer jika petugas menutup halaman sebelum 1.5 detik
    return () => {
      console.log("[Lifecycle] Halaman ditutup. Membersihkan antrean timer...");
      clearTimeout(ambilDataAPI);
    };
  }, []); // Array kosong [] menjamin ini bertindak sebagai onMounted!

  return (
    <View className="flex-1 bg-slate-50 justify-center items-center p-6">
      <View className="bg-white p-8 rounded-3xl border border-slate-100 shadow-sm items-center w-full max-w-sm">
        <Text className="text-xs font-bold text-slate-400 uppercase tracking-widest mb-4">Monitor Logistik</Text>

        {loading ? (
          <View className="space-y-2 items-center">
            <ActivityIndicator size="large" color="#ef4444" />
            <Text className="text-xs text-slate-400">Menghubungkan ke database server...</Text>
          </View>
        ) : (
          <View className="items-center space-y-2">
            <Text className="text-3xl font-black text-red-500">{stok} Bag</Text>
            <Text className="text-xs text-slate-600 font-semibold">Golongan O Positif Aktif</Text>
          </View>
        )}
      </View>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Amati parameter kedua `[]` pada penulisan `useEffect` di atas.
2. Jelaskan apa yang terjadi jika kita tidak sengaja **lupa menuliskan parameter kedua array kosong `[]`** tersebut (hanya menulis `useEffect(() => { ... })`). Apa efek buruknya bagi kinerja processor HP petugas?
3. Sebutkan padanan nama React Hook `useEffect` dengan fungsi array kosong `[]` jika dibandingkan dengan nama Lifecycle Hook pada Vue 3.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Resiko Loop Tanpa Batas (Infinite Render Loop)
*   **Kecelakaan Render Berulang**: Jika array dependensi parameter kedua dihilangkan, `useEffect` akan dieksekusi **pada setiap siklus render halaman** tanpa henti.
*   **Alur Loop Rusak**: Ketika `useEffect` berjalan, dia memicu `setLoading(false)` dan `setStok(85)`. Perubahan state tersebut memicu React menggambar ulang layar. 
*   Karena layar digambar ulang, `useEffect` yang tanpa filter array kosong tadi akan berjalan kembali $\rightarrow$ merubah state kembali $\rightarrow$ menggambar ulang kembali, menciptakan lingkaran setan loop tanpa batas (*Infinite loop*). Hal ini akan langsung membuat processor CPU HP bekerja 100%, panas, baterai habis dalam hitungan menit, dan aplikasi hang membeku total.

### Jawaban 3: Padanan Lifecycle Hook Vue vs React
*   React `useEffect(() => { ... }, [])` setara dengan **`onMounted(() => { ... })`** di Vue 3.
*   Sedangkan fungsi return pembersihnya `return () => { ... }` setara dengan **`onUnmounted(() => { ... })`** di Vue 3.
