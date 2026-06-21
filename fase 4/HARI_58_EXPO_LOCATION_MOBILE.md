# Hari 58: Membaca GPS Handphone (expo-location)
## Mengambil Titik Koordinat Latitude Dan Longitude Real-Time Petugas Untuk Pelacakan Distribusi Logistik

Selamat datang di Hari 58! Setelah berhasil mendapatkan izin OS dari pengguna di Hari 57, hari ini kita akan mempraktikkan proses penarikan data koordinat satelit GPS HP menggunakan modul **`expo-location`**.

Pada skenario distribusi kantong darah CITO, admin pusat PMI Lampung perlu mengetahui posisi terkini (*Live Tracking*) dari mobil ambulans relawan. Kita akan membaca data bujur lintang GPS dari antena satelit fisik HP dan menyajikannya di layar.

---

## 1. Pemahaman Dasar Sistem GPS Mobile

*   **Akurasi vs Baterai**: Antena GPS handphone menyedot daya baterai paling besar dibandingkan komponen lainnya. Membaca koordinat berulang kali dengan presisi tinggi akan membuat HP cepat panas. 
*   **Koordinat Spesifik**: Hasil dari pembacaan GPS berupa objek yang menyimpan `latitude` (Garis Lintang), `longitude` (Garis Bujur), serta `accuracy` (radius penyimpangan dalam satuan meter).

---

## 2. Studi Kasus PMI: Komponen Pelacak Titik Lokasi (`components/PelacakAmbulans.tsx`)

Mari kita buat komponen yang membaca titik koordinat petugas setelah izin diberikan:

```tsx
// components/PelacakAmbulans.tsx
import React, { useState } from 'react';
import { View, Text, TouchableOpacity, ActivityIndicator } from 'react-native';
import * as Location from 'expo-location';

export default function PelacakAmbulans() {
  const [koordinat, setKoordinat] = useState<{ lat: number; lng: number } | null>(null);
  const [loading, setLoading] = useState(false);
  const [errorMsg, setErrorMsg] = useState('');

  const bacaGpsSekarang = async () => {
    setLoading(true);
    setErrorMsg('');

    try {
      // 1. CEK IZIN OS TERLEBIH DAHULU! (Hari 57)
      const { status } = await Location.requestForegroundPermissionsAsync();
      
      if (status !== 'granted') {
        setErrorMsg('Izin akses GPS ditolak sistem.');
        setLoading(false);
        return; // Hentikan fungsi jika izin ditolak
      }

      console.log("[GPS] Mengontak satelit, membaca posisi fisik...");
      
      // 2. AMBIL KOORDINAT FISIK
      // PENTING: Gunakan accuracy: Location.Accuracy.Balanced untuk menghemat baterai
      const lokasi = await Location.getCurrentPositionAsync({
        accuracy: Location.Accuracy.Balanced,
      });

      // 3. SIMPAN HASIL KE STATE
      setKoordinat({
        lat: lokasi.coords.latitude,
        lng: lokasi.coords.longitude
      });
      console.log("[GPS] Sukses terkunci:", lokasi.coords.latitude, lokasi.coords.longitude);

    } catch (e: any) {
      setErrorMsg('Gagal menemukan sinyal satelit.');
      console.error(e);
    } finally {
      setLoading(false);
    }
  };

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4 items-center">
      <Text className="font-extrabold text-slate-800 text-sm">📡 Titik Koordinat Armada</Text>

      {/* Area Tampilan Koordinat */}
      <View className="w-full bg-slate-50 p-4 rounded-2xl border border-slate-100 items-center space-y-2 h-24 justify-center">
        {loading ? (
          <ActivityIndicator color="#ef4444" />
        ) : errorMsg ? (
          <Text className="text-red-500 text-xs font-bold">{errorMsg}</Text>
        ) : koordinat ? (
          <>
            <Text className="text-[10px] font-bold text-slate-400 uppercase tracking-widest">LOKASI TERKUNCI</Text>
            <Text className="text-sm font-black text-slate-800">
              Lat: {koordinat.lat.toFixed(5)}
            </Text>
            <Text className="text-sm font-black text-slate-800">
              Lng: {koordinat.lng.toFixed(5)}
            </Text>
          </>
        ) : (
          <Text className="text-xs text-slate-400">Koordinat belum ditarik.</Text>
        )}
      </View>

      <TouchableOpacity 
        onPress={bacaGpsSekarang}
        disabled={loading}
        className="w-full bg-red-500 py-3 rounded-2xl items-center disabled:bg-slate-200"
      >
        <Text className="text-white font-extrabold text-xs uppercase">Baca Sinyal GPS</Text>
      </TouchableOpacity>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari properti `accuracy: Location.Accuracy.Balanced` pada parameter metode pembacaan posisi lokasi di atas?
2. Buka blok fungsi `bacaGpsSekarang`. Mengapa kita wajib menjalankan fungsi `requestForegroundPermissionsAsync` terlebih dahulu tepat sebelum mengeksekusi metode pencarian koordinat?
3. Sebutkan nama 2 properti garis bumi utama yang dikembalikan oleh objek `lokasi.coords` yang berguna untuk menggambar posisi titik di Google Maps nantinya.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Kompromi Performa dan Baterai
*   **Penghematan Baterai**: Opsi `Accuracy.Balanced` memerintahkan OS untuk memadukan sinyal menara seluler, Wi-Fi terdekat, dan satelit standar untuk mendapatkan lokasi sejauh 100 meter, yang cukup untuk melacak kota/kecamatan, dan **memakan daya baterai sangat kecil**. 
*   Jika kita memakai `Accuracy.Highest`, HP dipaksa menyalakan satelit hardware fisik penuh secara konstan untuk mengukur sampai radius 1 meter, menyebabkan aplikasi melambat dan memakan kuota baterai besar.

### Jawaban 2: Syarat Izin Mutlak dari OS
*   **Pengecekan Berlapis (Double Gate Check)**: Sistem operasi Android/iOS dapat mencabut izin GPS di latar belakang kapan saja jika user mengubah setting di HP-nya. 
*   Memanggil metode permintaan izin di baris awal fungsi memastikan bahwa variabel izin terbaru diperiksa ulang. Jika izin dicabut dan kita paksa menembak fungsi `getCurrentPositionAsync`, sistem OS akan langsung membuat **aplikasi kita Crash Force Close karena pelanggaran privasi hardware**.

### Jawaban 3: Latitude dan Longitude
*   Properti tersebut bernama **`latitude`** (Garis Lintang - Utara/Selatan) dan **`longitude`** (Garis Bujur - Timur/Barat).
```
[FASE 4: HARI 58 SUKSES]
```
