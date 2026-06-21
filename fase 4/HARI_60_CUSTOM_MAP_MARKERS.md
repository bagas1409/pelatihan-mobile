# Hari 60: Marker Kustom Peta & Interaktivitas
## Memasang Titik Pin (Marker) Lokasi Rumah Sakit Mitra CITO Dan Merancang Pop-up Detail Call-to-Action

Selamat datang di Hari 60! Melanjutkan keterampilan menggambar kanvas bumi di Hari 59, peta kosong belumlah berguna untuk tujuan operasional dinas. Hari ini kita akan menancapkan pin penanda peta (**Marker**) di berbagai letak bujur lokasi rumah sakit mitra yang mengantre stok darah CITO.

Lebih jauh, kita akan mendesain interaksi pop-up (**Callout**) yang muncul ketika petugas menyentuh pin marker tersebut untuk menampilkan informasi sisa stok dan tombol navigasi jalan.

---

## 1. Komponen `<Marker>` dan `<Callout>`

Pustaka `react-native-maps` menyediakan anak komponen untuk ditaruh di dalam `<MapView>`:
*   **`<Marker>`**: Jarum pentol fisik penanda titik koordinat (bisa diubah visualnya menjadi ikon botol darah).
*   **`<Callout>`**: Jendela balon informasi (tooltip) yang akan menyembul melayang keluar saat Marker tersebut diklik oleh petugas.

---

## 2. Studi Kasus PMI: Jaringan Rantai Distribusi CITO Kota

Mari kita pasang beberapa titik rumah sakit mitra yang meminta stok CITO ke dalam halaman peta kita di **`app/(tabs)/peta-rs.tsx`**:

```tsx
// app/(tabs)/peta-rs.tsx
import React from 'react';
import { View, Text, StyleSheet, TouchableOpacity, Alert } from 'react-native';
import MapView, { Marker, Callout, PROVIDER_GOOGLE } from 'react-native-maps';

export default function PetaRumahSakit() {
  const titikPusat = {
    latitude: -5.4052,
    longitude: 105.2668,
    latitudeDelta: 0.08,
    longitudeDelta: 0.08,
  };

  // Data Simulasi Rumah Sakit (Di dunia nyata, data ini diunduh dari API useApi)
  const rumahSakitMitra = [
    { id: '1', nama: 'RSU Abdul Moeloek', lat: -5.3995, lng: 105.2588, kebutuhan: 'O Positif', urgensi: 'TINGGI' },
    { id: '2', nama: 'RS Bumi Waras', lat: -5.4215, lng: 105.2650, kebutuhan: 'A Positif', urgensi: 'RENDAH' },
  ];

  return (
    <View className="flex-1 bg-white">
      <MapView
        provider={PROVIDER_GOOGLE}
        style={StyleSheet.absoluteFillObject} // Peta mengisi 100% layar HP
        initialRegion={titikPusat}
      >
        
        {/* MERENDER DAFTAR TITIK RUMAH SAKIT */}
        {rumahSakitMitra.map((rs) => (
          <Marker
            key={rs.id}
            coordinate={{ latitude: rs.lat, longitude: rs.lng }}
            pinColor={rs.urgensi === 'TINGGI' ? 'red' : 'blue'} // Visual jarum merah jika CITO
          >
            {/* JENDELA POP-UP BALON INFO SAAT PIN DIKLIK */}
            <Callout tooltip>
              <View className="bg-white p-4 rounded-xl border border-slate-200 shadow-md w-48 items-center space-y-2">
                <Text className="font-bold text-slate-800 text-xs text-center">{rs.nama}</Text>
                
                <View className="bg-red-50 py-1 px-2 rounded-md w-full items-center">
                  <Text className="text-[9px] font-black text-red-500">BUTUH: {rs.kebutuhan}</Text>
                </View>

                {/* Tombol Interaksi Balon Callout */}
                <TouchableOpacity 
                  onPress={() => Alert.alert('Navigasi', `Membuka rute jalan ke ${rs.nama}...`)}
                  className="bg-slate-900 w-full py-2 rounded-lg items-center"
                >
                  <Text className="text-white font-bold text-[10px]">Pilih Rute & Kirim</Text>
                </TouchableOpacity>
              </View>
            </Callout>
          </Marker>
        ))}

      </MapView>

      {/* Header Mengambang Absolut di Atas Peta */}
      <View className="absolute top-12 left-6 right-6 bg-white/90 p-4 rounded-3xl border border-slate-100 shadow-sm">
        <Text className="font-black text-slate-800 text-sm">Posko Antrean CITO</Text>
        <Text className="text-xs text-slate-500">Sentuh jarum merah untuk detail.</Text>
      </View>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Amati kode perulangan `<Marker>` di atas. Mengapa kita merubah properti `pinColor` menjadi `'red'` atau `'blue'` secara dinamis berdasarkan nilai `rs.urgensi`?
2. Buka kode tag `<Callout tooltip>`. Jelaskan kegunaan parameter boolean `tooltip` pada komponen Callout bagi kebebasan Anda merancang desain CSS custom kotak putih balon infonya.
3. Apakah komponen map ini menggunakan style NativeWind atau StyleSheet klasik untuk mengatur dimensinya memenuhi layar? Sebutkan nama kelas/helper yang digunakan.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Visual Scanning UX
*   **Pemindaian Mata Instan**: Peta dengan 50 titik jarum akan membingungkan mata relawan distribusi. 
*   Memberikan kode warna khusus (Merah untuk CITO darurat tinggi, Biru untuk reguler) memungkinkan mata petugas memindai dan memutuskan titik mana yang harus diprioritaskan untuk dikirim kantong darah pertamakali dalam hitungan detik (*Cognitive Load Reduction*).

### Jawaban 2: Melompati Frame Default Sistem
*   **Custom Callout Design**: Secara default OS HP akan menggambar Callout menggunakan kotak sistem default Android/iOS yang jelek dan kaku. 
*   Menyematkan properti boolean `tooltip` (atau `tooltip={true}`) memerintahkan mesin Google Maps untuk membuang kotak bawaan sistem tersebut, sehingga bingkai, sudut melengkung `rounded-xl`, dan bayangan panah sepenuhnya digambar menggunakan kelas styling CSS komponen View React buatan kita sendiri.

### Jawaban 3: Absolute Fill Helper
*   **StyleSheet Helper**: Menggunakan bantuan API klasik `StyleSheet.absoluteFillObject` bawaan dari modul `react-native`. 
*   Perintah sakti ini secara otomatis merentangkan kanvas map agar 100% tinggi dan lebarnya menabrak pojok layar HP tanpa perhitungan manual `w-full h-full`.
```
[FASE 4: HARI 60 SUKSES]
```
