# Hari 61: Mengakses Sensor Kamera HP (expo-camera)
## Menghubungkan Layar Antarmuka Ke Feed Lensa Kamera Belakang Petugas Untuk Pengambilan Foto Bukti Transaksi

Selamat datang di Hari 61! Hari ini kita akan membuka kunci akses fitur sensor hardware yang paling menonjol pada perangkat mobile modern: **Lensa Kamera**.

Setiap serah-terima kantong darah antara kurir mobil distribusi PMI dan rumah sakit mitra memerlukan bukti fisik dokumentasi yang nyata untuk menghindari kecurangan. Aplikasi DonorKu dinas harus mampu membuka feed siaran langsung (*Live Feed Viewfinder*) dari kamera belakang HP dan menangkap (*Capture*) gambar tersebut ke memori lokal. Kita akan merakit modul ini menggunakan pustaka **`expo-camera`**.

---

## 1. Instalasi Modul Kamera

Jalankan instruksi berikut untuk memasang paket kamera Expo ke proyek Anda:
```bash
npx expo install expo-camera
```

---

## 2. Studi Kasus PMI: Jendela Kamera Pengambilan Bukti Transaksi Darah

Kita harus merancang halaman yang memuat komponen `<CameraView>`, serta menangani izin kamera sistem terlebih dahulu (Materi Hari 57). Mari kita buat di file **`app/kamera-transaksi.tsx`**:

```tsx
// app/kamera-transaksi.tsx
import React, { useState, useRef } from 'react';
import { View, Text, TouchableOpacity, StyleSheet, Alert } from 'react-native';
// Impor CameraView & useCameraPermissions
import { CameraView, useCameraPermissions } from 'expo-camera';

export default function LayarKameraTransaksi() {
  const [permission, requestPermission] = useCameraPermissions();
  
  // Ref untuk mencengkeram komponen kamera agar kita bisa memanggil fungsi jepret
  const alatKameraRef = useRef<CameraView>(null);
  
  // State untuk melacak apakah petugas menghadap lensa depan atau belakang
  const [facingDepan, setFacingDepan] = useState(false);

  // 1. TAMPILAN JIKA BELUM MEMILIKI IZIN OS
  if (!permission) { return <View />; } // Sedang memuat sensor
  if (!permission.granted) {
    return (
      <View className="flex-1 bg-white justify-center items-center p-6 space-y-4">
        <Text className="text-center text-xs font-bold text-slate-800">
          Kami membutuhkan izin kamera untuk memotret bukti surat jalan darah CITO.
        </Text>
        <TouchableOpacity onPress={requestPermission} className="bg-red-500 py-3 px-6 rounded-2xl">
          <Text className="text-white font-bold text-xs uppercase">Buka Kunci Lensa</Text>
        </TouchableOpacity>
      </View>
    );
  }

  // 2. FUNGSI PENJEPRETAN GAMBAR (SHUTTER)
  const jepretFoto = async () => {
    // Pastikan ref kamera sudah tersambung
    if (alatKameraRef.current) {
      console.log("[Camera] Mengambil gambar kualitas tinggi...");
      
      // Ambil objek file gambar
      const hasilFoto = await alatKameraRef.current.takePictureAsync({
        quality: 0.7, // Kompres ukuran file (0.0 s.d 1.0) untuk hemat bandwidth!
        base64: true, // Sertakan encoding data mentah biner
      });

      console.log("[Camera] Berhasil dijepret! URI Lokal:", hasilFoto?.uri);
      Alert.alert('📸 Foto Tersimpan', 'Siap dilampirkan ke database transaksi pusat.');
    }
  };

  // 3. FUNGSI PEMUTAR ARAH LENSA
  const putarArahKamera = () => {
    setFacingDepan((posisiAwal) => !posisiAwal);
  };

  return (
    <View className="flex-1 bg-black">
      {/* 
        KOMPONEN UTAMA VIEWFINDER KAMERA
        Mengisi seluruh kontainer
      */}
      <CameraView 
        ref={alatKameraRef}
        style={StyleSheet.absoluteFillObject} 
        facing={facingDepan ? 'front' : 'back'} // Parameter penentu lensa
      >
        
        {/* PANEL TOMBOL TRANSPARAN DI BAGIAN BAWAH LAYAR */}
        <View className="absolute bottom-10 left-0 right-0 flex-row justify-evenly items-center">
          
          <TouchableOpacity onPress={putarArahKamera} className="bg-white/20 p-4 rounded-full">
            <Text className="text-white text-xs font-bold">↻ PUTAR</Text>
          </TouchableOpacity>

          {/* Tombol Shutter Utama Besar */}
          <TouchableOpacity onPress={jepretFoto} className="bg-white w-20 h-20 rounded-full border-4 border-slate-300 shadow-xl" />

          <View className="w-16" /> {/* Spacer Kosong */}
        </View>

      </CameraView>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari pemanggilan hook `useCameraPermissions()` di awal halaman di atas? Mengapa alur UI wajib dihalangi jika properti `permission.granted` bernilai false?
2. Buka blok kode parameter konfigurasi jepretan foto `takePictureAsync`. Mengapa kita menurunkan kualitas (quality) foto menjadi `0.7` alih-alih `1.0` yang berkualitas maksimal?
3. Sebutkan nilai string properti `facing` pada komponen `<CameraView>` jika kita ingin secara paksa menggunakan lensa pemotretan selfie (lensa depan HP).

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Filter Gerbang Keamanan Hardware
*   **Aturan OS Ketat**: Kamera adalah perangkat privasi sensitif tertinggi. 
*   Memaksa merender `<CameraView>` secara fisik di layar tanpa status izin yang valid (`granted`) akan memicu Error Fatal OS Android/iOS dan aplikasi akan langsung Crash ditutup paksa. Kode `if (!permission.granted)` adalah dinding pelindung yang menjamin lensa fisik baru menyala jika tombol "Izinkan Kamera" di popup sistem sudah diklik pengguna.

### Jawaban 2: Optimasi Beban Payload Upload (Bandwidth Saver)
*   **Mencegah Macet Upload**: Foto kualitas asli `1.0` dari lensa 12-Megapixel HP masa kini bisa menghasilkan file berukuran 5MB sampai 10MB per gambar. 
*   Menurunkan quality ke `0.7` mengompresi gambar tersebut menjadi ukuran web-friendly (sekitar 800KB - 1.5MB) tanpa merusak kejelasan detail teks bukti transaksi secara mata telanjang. Pengurangan ukuran ekstrem ini menjamin foto **meluncur super cepat saat dikirim** (di-upload) ke server di tempat yang sinyal selulernya buruk.

### Jawaban 3: Parameter Kamera Depan
*   Nilai tersebut adalah string **`'front'`**.
```
[FASE 4: HARI 61 SUKSES]
```
