# Hari 59: Menggambar Peta Interaktif (react-native-maps)
## Menampilkan Koordinat Ke Dalam Antarmuka Google Maps Interaktif Di Aplikasi Mobile Petugas

Selamat datang di Hari 59! Melanjutkan materi Hari 58, angka-angka desimal GPS (Latitude & Longitude) tidaklah ramah bagi mata petugas awam. Hari ini kita akan menyuntikkan kanvas peta satelit bumi sungguhan ke dalam aplikasi menggunakan pustaka **`react-native-maps`**.

Berbeda dengan integrasi web di mana kita harus memuat script iframe berukuran besar, React Native memiliki dukungan native ke modul mesin pembaca peta bawaan handphone: **Google Maps** (untuk perangkat Android) dan **Apple Maps** (untuk perangkat iOS). Hal ini menjamin transisi geser dan perbesaran cubitan jari (*Pinch Zoom*) peta berjalan secara sangat mulus dengan laju layar 60 FPS asli.

---

## 1. Instalasi Library Peta

Jalankan instruksi pemasangan pustaka pembuat peta resmi yang didukung Expo ini di terminal Anda:
```bash
npx expo install react-native-maps
```

---

## 2. Studi Kasus PMI: Menampilkan Peta Lokasi Kantor Pusat UDD Lampung

Mari buat komponen peta interaktif yang titik awal kameranya diarahkan langsung ke kota Bandar Lampung di berkas **`components/PetaDistribusiDinas.tsx`**:

```tsx
// components/PetaDistribusiDinas.tsx
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';
// Impor komponen MapView khusus penyedia tampilan kanvas bumi
import MapView, { PROVIDER_GOOGLE } from 'react-native-maps';

export default function PetaDistribusiDinas() {
  
  // Titik Fokus Kamera Awal: Markas Pusat PMI Lampung
  const posisiAwal = {
    latitude: -5.4052, // Garis Lintang
    longitude: 105.2668, // Garis Bujur
    latitudeDelta: 0.05, // Skala perbesaran vertical (Makin kecil makin zoom)
    longitudeDelta: 0.05, // Skala perbesaran horizontal
  };

  return (
    <View className="flex-1 bg-white p-4 rounded-3xl overflow-hidden space-y-3">
      <Text className="font-bold text-slate-800 text-sm">🗺️ Peta Pantauan Logistik Lampung</Text>
      <Text className="text-[10px] text-slate-500">Cubitan dua jari untuk memperbesar area peta.</Text>

      {/* WADAH PETA WAJIB MEMILIKI DIMENSI TINGGI YANG JELAS! */}
      <View className="w-full h-80 rounded-2xl overflow-hidden border border-slate-200">
        <MapView
          // Paksa iOS untuk juga menggunakan engine mesin Google Maps (jika diinstal), 
          // atau hapus baris ini agar iOS otomatis menggunakan Apple Maps
          provider={PROVIDER_GOOGLE} 
          
          style={styles.petaFisik}
          initialRegion={posisiAwal} // Tembak kamera satelit ke objek posisiAwal
          showsUserLocation={true} // Tampilkan titik biru lokasi HP petugas saat ini
          showsMyLocationButton={true} // Tombol kembali membidik titik saya
        />
      </View>
    </View>
  );
}

// MapView membutuhkan StyleSheet lama karena terkadang bermasalah saat digabung class Tailwind murni
const styles = StyleSheet.create({
  petaFisik: {
    width: '100%',
    height: '100%',
  },
});
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari nilai koordinat spesifik `latitudeDelta` dan `longitudeDelta` pada objek `posisiAwal` di atas? Apa efek visualnya di layar jika kita mengubah nilainya dari `0.05` menjadi `2.0`?
2. Jelaskan fungsi dari properti boolean `showsUserLocation={true}` pada komponen `<MapView>`. Fitur apa dari Hari 58 yang wajib dijalankan terlebih dahulu agar properti ini berfungsi?
3. Buka kode properti `provider={PROVIDER_GOOGLE}`. Apa keuntungan teknis menggunakan mesin Google Maps secara serentak di Android maupun iPhone (iOS)?

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Konfigurasi Tingkat Pembesaran Kamera (Zoom Level Delta)
*   **Rentang Ruang Pandang (Field of View)**: Delta mengatur berapa derajat luas bentang alam yang ingin dirender layar. 
*   Nilai `0.05` sangat sempit, sehingga kamera akan menukik sangat dekat menampilkan blok-blok gedung jalanan kota secara detail. 
*   Jika diubah menjadi `2.0`, rentang pandang menjadi sangat luas, sehingga layar HP akan menampilkan keseluruhan peta pulau Sumatera dan sekitarnya dari luar angkasa.

### Jawaban 2: Merender Ikon Titik Biru Radar Pengguna
*   **Sensor Posisi Asli**: Properti ini memerintahkan mesin peta Google untuk otomatis membaca titik GPS HP saat ini dan menggambar sebuah **bulatan radar berwarna biru** di letak titik tersebut. 
*   **Syarat Mutlak**: Fitur ini tidak akan berfungsi dan tidak akan muncul jika petugas belum memberikan Izin Lokasi OS (*Foreground Location Permission*) yang kita bahas di Hari 57.

### Jawaban 3: Konsistensi Visual Lintas OS
*   **Menyatukan Desain**: Jika tidak diatur, HP Android akan memuat Google Maps, dan HP iPhone akan memuat Apple Maps bawaan yang memiliki warna dan letak bangunan desain yang sedikit berbeda. 
*   Memaksa penggunaan `PROVIDER_GOOGLE` menjamin relawan maupun petugas yang memakai merk HP berbeda akan sama-sama melihat tata letak antarmuka peta Google yang konsisten, memudahkan sinkronisasi pelaporan lapangan.
```
[FASE 4: HARI 59 SUKSES]
```
