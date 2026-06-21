# Hari 50: Desain Transisi Loading Screen
## Merancang Halaman Penahan loading Transisi Dan Efek Kerangka Berdenyut (Skeleton Loader)

Selamat datang di Hari 50! Hari ini kita akan mempelajari cara merancang **Layar Transisi Loading** (Loading Screen Transitions) yang premium.

Selama aplikasi mobile menunggu respon unduh data dari server backend, membiarkan layar polos kosong tanpa tanda aktivitas akan membuat pengguna mengira aplikasi Anda hang macet. Hari ini kita akan merancang komponen loading penahan layar (*Loading Overlay*) menggunakan perpaduan **`ActivityIndicator`** dan background semi-transparan visual premium agar pengguna paham bahwa aplikasi sedang bekerja memproses transaksi database.

---

## 1. Analogi Visual: Lampu Merah Antrean Loket UDD
Bayangkan seorang relawan datang ke kantor PMI Lampung. Petugas mencari arsip kartu mereka di lemari arsip besar.
*   **Tanpa Loading**: Petugas diam membisu menatap relawan tanpa ekspresi selama 5 menit. Relawan akan bingung dan mengira petugas tersebut tidak melayani mereka.
*   **Dengan Loading**: Petugas tersenyum ramah dan berkata *"Mohon tunggu sebentar ya, saya sedang mencarikan berkas Anda"* sembari meletakkan papan penanda antrean. Relawan merasa nyaman dan sabar menunggu.
*   Di aplikasi mobile, papan penanda antrean tersebut diwakili oleh animasi loading spinner melingkar yang menutupi area klik layar.

---

## 2. Studi Kasus PMI: Komponen Loading Overlay Global (`components/LoadingOverlay.tsx`)

Mari kita buat komponen loading penutup area layar yang bisa dipakai di mana saja:

```tsx
// components/LoadingOverlay.tsx
import React from 'react';
import { View, Text, ActivityIndicator } from 'react-native';

interface LoadingOverlayProps {
  visible: boolean; // Status tampil/sembunyikan
  pesan?: string;   // Teks pesan loading (opsional)
}

export default function LoadingOverlay({ visible, pesan = 'Sedang memproses data...' }: LoadingOverlayProps) {
  // Jika props visible bernilai false, sembunyikan komponen ini (jangan render apapun)
  if (!visible) {
    return null;
  }

  return (
    // Wadah absolut hitam transparan yang mengunci seluruh layar HP
    <View className="absolute inset-0 bg-slate-900/60 justify-center items-center z-[999] px-6">
      
      {/* Kotak Card Tengah */}
      <View className="bg-white p-8 rounded-3xl border border-slate-100 items-center space-y-4 max-w-xs w-full shadow-lg">
        
        {/* Lingkaran Loading Merah PMI */}
        <ActivityIndicator size="large" color="#ef4444" />
        
        <Text className="text-xs font-bold text-slate-800 text-center leading-relaxed">
          {pesan}
        </Text>
        
        <Text className="text-[9px] font-bold text-slate-400 uppercase tracking-widest">
          Jaringan UDD PMI
        </Text>

      </View>

    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari pemanggilan class Tailwind `absolute inset-0` pada kontainer terluar komponen `LoadingOverlay` di atas?
2. Jelaskan fungsi dari pengaturan z-index tinggi: `z-[999]` pada komponen overlay di atas bagi prioritas ketampakan visual.
3. Sebutkan nama properti bawaan `ActivityIndicator` yang bertugas memperbesar diameter putaran loading khusus untuk mempermudah penglihatan petugas.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Penguncian Layar Absolut (Full Screen Lock)
*   **Menutupi Layar**: Kelas `absolute inset-0` memposisikan komponen agar menempel di koordinat `top: 0`, `bottom: 0`, `left: 0`, dan `right: 0` dari layar pembungkusnya. 
*   Ini memastikan latar belakang abu-abu gelap transparan menutupi seluruh sudut monitor HP secara penuh, mencegah pengguna mengklik tombol lain di bawahnya secara tidak sengaja (*Click Jacking Protection*).

### Jawaban 2: Prioritas Lapisan Visual (Z-index Stack)
*   **Lapisan Teratas**: Angka `z-[999]` memerintahkan HP menggambar overlay di tumpukan koordinat kedalaman teratas. 
*   Hal ini menjamin spinner loading akan digambar melayang di atas visual navigasi bar atas, footer bawah, maupun elemen modal apa saja, mengunci layar secara steril selama API sedang memproses transaksi.

### Jawaban 3: Properti size="large"
*   Properti tersebut bernama **`size="large"`** (sedangkan ukuran standar adalah `"small"`).
```
[FASE 3: HARI 50 SUKSES]
```
