# Hari 34: Deteksi Status Internet (NetInfo)
## Menampilkan Banner Peringatan Merah Saat Handphone Petugas Kehilangan Koneksi Sinyal Internet Offline

Selamat datang di Hari 34! Hari ini kita akan mempelajari cara mendeteksi **Status Koneksi Internet** handphone secara real-time menggunakan pustaka **`@react-native-community/netinfo`**.

Aplikasi mobile dirancang untuk dibawa bepergian ke luar kantor (berbeda dengan web desktop yang biasanya diam di meja). Petugas PMI Lampung sering kali harus menyusuri daerah pelosok desa untuk mengumpulkan darah donor keliling, di mana sinyal operator seluler sering terputus (*Offline*). Jika internet mati dan petugas mencoba menekan tombol kirim data, aplikasi akan hang memutar loading tanpa batas. Kita harus mendeteksi matinya internet secara instan dan menampilkan banner peringatan merah di atas layar agar petugas tahu status sinyal mereka.

---

## 1. Instalasi Library NetInfo

Jalankan perintah penginstalan berikut di terminal proyek Expo Anda:
```bash
npx expo install @react-native-community/netinfo
```

---

## 2. Studi Kasus PMI: Banner Peringatan Koneksi Terputus (Offline Banner)

Mari kita rancang komponen banner peringatan melayang yang akan menyala otomatis jika sinyal HP mati di file **`components/BannerInternetMati.tsx`**:

```tsx
// components/BannerInternetMati.tsx
import React, { useState, useEffect } from 'react';
import { View, Text } from 'react-native';
// Impor modul NetInfo
import NetInfo from '@react-native-community/netinfo';

export default function BannerInternetMati() {
  const [online, setOnline] = useState<boolean | null>(true);

  useEffect(() => {
    // 1. DAFTARKAN DETEKTOR SINYAL REAL-TIME
    const unsubscribe = NetInfo.addEventListener((state) => {
      console.log("[Sinyal] Tipe Koneksi:", state.type);
      console.log("[Sinyal] Apakah Konek Internet?:", state.isConnected);
      setOnline(state.isConnected); // Update state dengan status aslinya (true/false)
    });

    // 2. CLEANUP: Cabut detektor jika komponen unmount
    return () => {
      console.log("[Sinyal] Detektor dicabut.");
      unsubscribe();
    };
  }, []);

  // Jika sinyal normal (online), jangan gambar banner di layar (sembunyikan)
  if (online === true || online === null) {
    return null;
  }

  return (
    // Banner Merah Melayang di Atas Layar HP
    <View className="absolute top-0 left-0 right-0 bg-red-500 py-3 px-6 z-50 items-center justify-center flex-row space-x-2 shadow-md">
      <Text className="text-white text-xs">⚠️</Text>
      <Text className="text-white font-extrabold text-[10px] uppercase tracking-wider">
        Koneksi Internet Terputus. Memasuki Mode Offline.
      </Text>
    </View>
  );
}
```

Tanamkan komponen `<BannerInternetMati />` ini di dalam file layout global `src/app/_layout.tsx (atau app/_layout.tsx)` Anda agar banner pengaman ini aktif menjaga seluruh halaman aplikasi secara serentak.

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari perintah callback `unsubscribe()` pada return `useEffect` di atas?
2. Buka kode `BannerInternetMati.tsx`. Mengapa kita memposisikan banner menggunakan kelas utility `absolute top-0 left-0 right-0 z-50`?
3. Sebutkan nama parameter di dalam objek status NetInfo yang berfungsi mendeteksi tipe media fisik yang digunakan HP untuk terhubung ke internet (seperti Wi-Fi atau Seluler/4G).

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Penghentian Detektor Sinyal OS
*   **Mencabut Listener**: Fungsi `NetInfo.addEventListener` mendaftarkan callback ke pemantau sistem jaringan native Android/iOS. 
*   Memanggil `unsubscribe()` di akhir siklus hidup memastikan sistem operasi HP **berhenti mengirimkan sinyal laporan status jaringan** ke aplikasi kita saat halaman ditutup, memotong konsumsi baterai dan RAM latar belakang yang tidak perlu.

### Jawaban 2: Posisi Melayang Teratas (Floating Bar)
*   **Aman Terlihat**: Kelas `absolute top-0` memposisikan banner di bagian paling atas layar, menimpa elemen visual apa saja yang ada di bawahnya. 
*   Penambahan `z-50` menjamin banner akan digambar di atas koordinat sumbu kedalaman teratas, mencegah banner tertutup di belakang foto banner atau visual map, memastikan petugas langsung menyadari matinya sinyal seketika.

### Jawaban 3: Parameter state.type
*   Parameter tersebut bernama **`state.type`**. 
*   Dia bisa bernilai `'wifi'` jika terhubung ke modem kantor PMI, bernilai `'cellular'` jika terhubung ke paket data 4G/5G operator seluler di jalan, atau bernilai `'none'` jika tidak ada jaringan aktif.
```
[FASE 2: HARI 34 SUKSES]
```
