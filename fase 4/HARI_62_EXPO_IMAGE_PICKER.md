# Hari 62: Mengambil Gambar dari Galeri (expo-image-picker)
## Membuka Akses Galeri Foto HP Petugas Untuk Memilih Lampiran Berkas Transaksi yang Sudah Difoto Sebelumnya

Selamat datang di Hari 62! Jika di Hari 61 kita mengambil gambar langsung menggunakan kamera (*Real-time Shoot*), hari ini kita akan menyediakan opsi kedua: **Mengambil gambar dari album galeri HP** menggunakan modul **`expo-image-picker`**.

Terkadang kurir PMI sudah mengambil foto formulir persetujuan CITO menggunakan aplikasi kamera bawaan HP mereka saat sedang terburu-buru, lalu baru meng-uploadnya ke aplikasi saat mereka sedang di jalan. Kita harus menyediakan tombol "Pilih dari Galeri" yang aman.

---

## 1. Instalasi Image Picker

Jalankan instruksi berikut untuk memasang paket pemilih gambar Expo ke proyek Anda:
```bash
npx expo install expo-image-picker
```

---

## 2. Studi Kasus PMI: Komponen Pemilih Gambar Laporan CITO

Kita akan membuat komponen tombol yang jika diklik, akan memicu popup sistem untuk masuk ke galeri foto (*Media Library*) HP:

```tsx
// components/PemilihGaleriBukti.tsx
import React, { useState } from 'react';
import { View, Text, TouchableOpacity, Image, Alert } from 'react-native';
// Impor helper picker dari expo-image-picker
import * as ImagePicker from 'expo-image-picker';

export default function PemilihGaleriBukti() {
  const [uriGambar, setUriGambar] = useState<string | null>(null);

  const bukaGaleriHp = async () => {
    console.log("[Galeri] Meminta sistem OS membuka folder galeri...");
    
    try {
      // 1. PANGGIL SISTEM GALERI BAWAAN OS
      const hasilPilihan = await ImagePicker.launchImageLibraryAsync({
        mediaTypes: ImagePicker.MediaTypeOptions.Images, // HANYA perbolehkan tipe Gambar (tanpa video)
        allowsEditing: true, // Izinkan petugas memotong (Crop) bagian tidak penting
        quality: 0.6, // Kompres kualitas gambar jadi 60%
        aspect: [4, 3], // Kunci rasio cropping persegi panjang
      });

      // 2. TANGANI HASIL PEMILIHAN
      if (!hasilPilihan.canceled) {
        // Pengguna berhasil memilih 1 foto, catat alamat (URI) file lokalnya
        const alamatFile = hasilPilihan.assets[0].uri;
        setUriGambar(alamatFile);
        console.log("[Galeri] Gambar sukses terpilih. URI:", alamatFile);
      } else {
        console.log("[Galeri] Aksi dibatalkan. Petugas menutup popup galeri.");
      }

    } catch (error) {
      console.error("[Galeri Error]:", error);
      Alert.alert('Gagal Akses', 'Aplikasi tidak dapat menjangkau folder galeri Anda.');
    }
  };

  const hapusGambar = () => setUriGambar(null);

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-100 shadow-sm space-y-4 items-center">
      <Text className="font-extrabold text-slate-800 text-sm">📁 Lampiran Arsip CITO</Text>
      
      {/* 3. AREA TAMPILAN PREVIEW GAMBAR LOKAL */}
      <View className="w-full h-48 bg-slate-50 border-2 border-dashed border-slate-200 rounded-2xl justify-center items-center overflow-hidden">
        {uriGambar ? (
          <Image 
            source={{ uri: uriGambar }} 
            className="w-full h-full"
            resizeMode="cover"
          />
        ) : (
          <Text className="text-xs text-slate-400">Belum ada file gambar yang dilampirkan</Text>
        )}
      </View>

      <View className="flex-row space-x-3 w-full">
        {/* Tombol Hapus (Hanya muncul jika gambar sudah terpilih) */}
        {uriGambar && (
          <TouchableOpacity onPress={hapusGambar} className="flex-1 bg-slate-100 py-3.5 rounded-2xl items-center">
            <Text className="text-slate-600 font-bold text-xs uppercase">Batal</Text>
          </TouchableOpacity>
        )}

        {/* Tombol Utama Buka Galeri */}
        <TouchableOpacity onPress={bukaGaleriHp} className="flex-[2] bg-red-500 py-3.5 rounded-2xl items-center">
          <Text className="text-white font-bold text-xs uppercase">
            {uriGambar ? 'Ganti Foto Lain' : 'Cari di Galeri'}
          </Text>
        </TouchableOpacity>
      </View>

    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Buka blok kode pengaturan parameter pemilih gambar: `mediaTypes: ImagePicker.MediaTypeOptions.Images`. Apa kegunaan parameter pembatas tipe data ini bagi perlindungan ruang database server backend Anda?
2. Jelaskan fungsi dari fitur `allowsEditing: true` bagi efektivitas ukuran foto bukti struk yang seringkali mengandung area kosong besar (seperti meja/lantai) sebelum gambar tersebut di-upload.
3. Sebutkan properti boolean penanda aksi di dalam objek `hasilPilihan` yang bernilai `true` jika petugas tidak jadi memilih foto dan menekan tombol panah silang untuk keluar dari galeri.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Mencegah Upload Video Berat Secara Tidak Sengaja
*   **Filter Format File**: Pengaturan ini mengunci jendela galeri OS agar **hanya menampilkan file foto berformat JPG/PNG saja**. 
*   Petugas sama sekali tidak bisa mengklik dan melampirkan video MP4 berukuran 500MB dari HP mereka. Ini menyelamatkan server API backend PMI dari ledakan kehabisan harddisk akibat menerima upload payload video berat yang tidak sesuai peruntukan logistik dokumentasi struk transaksi.

### Jawaban 2: Cropping Interaktif UI (Membuang Ruang Mati)
*   **Fokus Visual Dokumentasi**: Menyalakan properti editing akan secara otomatis menyisipkan satu layar tambahan bawaan OS (*Cropping Screen*) sesaat setelah petugas memilih foto struk dari galeri mereka. 
*   Petugas bisa mencubit layar untuk memotong buang bagian background pinggir foto yang tidak berguna (seperti foto taplak meja/ujung aspal sepatu). Ini menghasilkan foto bersih (*Clean crop*) berdimensi kecil yang sangat profesional saat ditampilkan di dashboard pusat PMI.

### Jawaban 3: Properti canceled
*   Properti tersebut bernama **`canceled`** (atau `cancelled` di library versi terdahulu).
```
[FASE 4: HARI 62 SUKSES]
```
