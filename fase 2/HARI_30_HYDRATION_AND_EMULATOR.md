# Hari 30: Optimasi Kecepatan Emulator & Hydration Mismatch
## Mengatasi Hambatan Rendering Awal Serta Mengoptimalkan Kecepatan Bundling Metro Di Komputer Lokal

Selamat datang di Hari 30! Hari ini kita akan mempelajari teknik penanganan **Hydration Mismatch** (Ketidakselarasan Rendering Awal) dan tips **Mengoptimalkan Kecepatan Bundler Metro** di laptop lokal Anda.

Saat kita mendeploy aplikasi web dengan Server-Side Rendering (SSR) di Fase 3, kita mengenal error Hydration. Di Expo Router modern yang mendukung pengodean universal (Web + Mobile), kita juga beresiko menemui error yang sama jika kode program kita mencoba mengakses variabel lingkungan native HP secara langsung di detik pertama server merender layout. Hari ini kita akan belajar menjinakkan error tersebut serta mempercepat proses bundling di laptop agar hemat waktu.

---

## 1. Apa itu Hydration Mismatch di React Native?

*   **Penyebab**: Terjadi jika hasil rendering visual di sisi Server (SSR) berbeda bentuknya dengan hasil rendering di sisi Client (Browser/Mobile). 
*   **Contoh Fatal**: Kode program Anda mencoba membaca dimensi lebar layar HP atau status waktu jam real-time:
    ```typescript
    // ERROR: Server merender jam statis, sedangkan HP/Client merender jam dinamis saat ini!
    <Text>{new Date().toLocaleTimeString()}</Text> 
    ```
*   **Solusi Keamanan**: Gunakan state penampung boolean `isMounted` untuk menjamin kode dinamis tersebut hanya dieksekusi setelah komponen selesai dipasang di sisi Client.

---

## 2. Studi Kasus PMI: Jendela Render Dinamis Aman dari Hydration

Mari kita perbaiki komponen detail jam piket petugas PMI agar bebas dari ancaman error Hydration:

```tsx
// components/JamPiketDinas.tsx
import React, { useState, useEffect } from 'react';
import { View, Text } from 'react-native';

export default function JamPiketDinas() {
  const [pukul, setPukul] = useState<string>('');
  const [mounted, setMounted] = useState(false);

  // useEffect menjamin kode di dalamnya HANYA dieksekusi di sisi CLIENT (HP)
  useEffect(() => {
    setMounted(true);
    setPukul(new Date().toLocaleTimeString());

    const timer = setInterval(() => {
      setPukul(new Date().toLocaleTimeString());
    }, 1000);

    return () => clearInterval(timer);
  }, []);

  // Jika server mencoba merender awal, berikan visual placeholder kosong yang statis
  if (!mounted) {
    return <Text className="text-slate-400 text-xs">Menghitung jam...</Text>;
  }

  return (
    <View className="bg-slate-50 p-4 rounded-2xl border border-slate-100 items-center">
      <Text className="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Pukul Operasional</Text>
      <Text className="text-lg font-black text-slate-800 mt-1">{pukul}</Text>
    </View>
  );
}
```

---

## 3. Tips Mempercepat Bundling Metro di Laptop Lokal

Jika Metro Bundler Anda terasa lambat saat memproses perubahan kode program (*Saving code takes long time*), terapkan optimasi berikut:

1.  **Gunakan Port Bersih**: Tutup aplikasi lain yang menduduki port default `8081`.
2.  **Bersihkan Cache Metro**: Jika emulator macet di angka 0% bundling, matikan terminal dan nyalakan ulang dengan bendera pembersih cache:
    ```bash
    npx expo start --clear
    ```
3.  **Matikan Log yang Terlalu Tebal**: Hindari menulis `console.log()` di dalam fungsi perulangan render `FlatList` karena akan memperlambat transfer data via kabel data/Wi-Fi ke emulator.

---

## 4. Latihan Soal Mandiri
1. Buka kode `JamPiketDinas` di atas. Jelaskan mengapa kita menggunakan state `mounted` dan apa akibatnya bagi rendering server jika kita langsung merender variabel `new Date()` tanpa dibungkus `useEffect`.
2. Apa guna dari perintah `npx expo start --clear` di terminal komputer lokal Anda?
3. Mengapa penulisan perintah `console.log()` di dalam fungsi perulangan render FlatList dilarang keras saat kita menguji aplikasi menggunakan emulator/HP fisik?

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Menghindari Bentrokan Server vs Client
*   **Penyelarasan Siklus**: Saat server merender komponen `JamPiketDinas` di awan, server membaca waktu jam server (misal pukul `12.00.00`). 
*   Di milidetik berikutnya, ketika script mendarat di HP petugas, HP membaca waktu lokal HP (misal pukul `12.00.02`). Bentrokan perbedaan 2 detik ini akan membuat React melempar error merah *Hydration Mismatch* karena pohon DOM yang digambar server tidak selaras dengan client. 
*   Membungkusnya dengan state `mounted` menjamin server dan client sama-sama merender visual teks kosong di detik pertama, lalu client menyuntikkan waktu dinamisnya setelah koneksi sinkronisasi selesai (*Hydrated*).

### Jawaban 2: Reset Cache Metro
*   **Fungsi `--clear`**: Menghapus seluruh berkas tembolok rakitan lama (*Cache*) yang disimpan oleh bundler Metro di dalam folder `.expo` lokal. 
*   Ini berguna untuk mengatasi bug aneh di mana perubahan kode program terbaru kita tidak mau ter-update di layar HP karena Metro salah membaca cache kompilasi yang rusak.

### Jawaban 3: Mencegah Hambatan Komunikasi Wi-Fi (Terminal Bottleneck)
*   **Beban Terminal**: Menulis log di dalam Flat List (misal meloop 1.000 kali) memaksa HP mengirimkan 1.000 paket data string log kembali ke terminal laptop via jaringan Wi-Fi lokal. 
*   Banjir paket data log ini akan langsung menyumbat jalur komunikasi LAN Wi-Fi, menyebabkan proses respon tombol di HP terasa melambat (*UI Thread Lagging*) dan terminal VS Code Anda menjadi lamban merespon input keyboard.
```
[FASE 2: HARI 30 SUKSES]
```
