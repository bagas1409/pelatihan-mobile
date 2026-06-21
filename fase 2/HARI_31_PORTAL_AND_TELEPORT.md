# Hari 31: Komponen Portal & Teleportasi UI
## Memahami Cara Melempar Komponen Dialog Konfirmasi Keluar Tembus Batas Layout Menggunakan Gorhom Portal

Selamat datang di Hari 31! Hari ini kita akan mempelajari teknik peletakan komponen tingkat lanjut: **Portal (Teleport)**.

Saat merakit web di Fase 4, kita mempelajari cara melempar elemen modal keluar menggunakan komponen `<Teleport>` bawaan Vue. Di dunia React Native, kita seringkali menghadapi masalah di mana dialog konfirmasi yang kita buat di dalam komponen kartu kecil (`Card`) visualnya terpotong atau tertutup di bawah batas overflow komponen induknya yang memiliki properti `borderRadius` atau `overflow: 'hidden'`. 

Hari ini kita akan memasang library **Gorhom Portal** untuk melempar dialog konfirmasi tersebut menembus hierarki layout dan meletakkannya di posisi teratas layar HP.

---

## 1. Instalasi Library Portal

Jalankan perintah berikut di terminal proyek Anda untuk memasang pustaka portal:
```bash
npm install @gorhom/portal
```

---

## 2. Studi Kasus PMI: Jendela Darurat CITO Tembus Pandang

### Langkah 1: Pasang Portal Provider di Layout Global (`src/app/_layout.tsx (atau app/_layout.tsx)`)

Agar portal bisa bekerja melempar elemen, kita wajib menyelimuti halaman root kita menggunakan **`PortalProvider`**:

```tsx
// src/app/_layout.tsx (atau app/_layout.tsx)
import React from 'react';
import { Stack } from 'expo-router';
// Impor Provider dari gorhom portal
import { PortalProvider } from '@gorhom/portal';

export default function LayoutPortalGlobal() {
  return (
    <PortalProvider>
      <Stack>
        <Stack.Screen name="index" options={{ headerShown: false }} />
      </Stack>
    </PortalProvider>
  );
}
```

---

### Langkah 2: Lempar Elemen Menggunakan Komponen `<Portal>`

Kini, dari dalam sub-komponen kartu kecil mana saja, kita bisa membungkus modal dialog menggunakan tag **`<Portal>`** agar dilemparkan langsung ke root layar teratas:

```tsx
// components/KartuCitoKecil.tsx
import React, { useState } from 'react';
import { View, Text, TouchableOpacity } from 'react-native';
// Impor Portal
import { Portal } from '@gorhom/portal';

export default function KartuCitoKecil() {
  const [showModal, setShowModal] = useState(false);

  return (
    // Wadah kartu kecil yang terpotong overflow-hidden
    <View className="bg-slate-100 p-4 rounded-3xl overflow-hidden border border-slate-200">
      <Text className="text-xs font-bold text-slate-800">Posko UDD Kedaton</Text>

      <TouchableOpacity 
        onPress={() => setShowModal(true)}
        className="bg-red-500 py-2 px-4 rounded-xl mt-3 items-center"
      >
        <Text className="text-white font-bold text-[10px]">TAYANGKAN ALERT</Text>
      </TouchableOpacity>

      <!-- 
        EKSEKUSI PORTAL (TELEPORTASI UI)
        Dialog di bawah ini akan melompat keluar dari KartuCitoKecil yang sempit
        dan merender dirinya langsung di root layar teratas, aman dari bug potong border!
      -->
      {showModal && (
        <Portal>
          <View className="absolute inset-0 bg-slate-900/60 justify-center items-center p-6 z-50">
            <View className="bg-white p-6 rounded-3xl border border-slate-100 max-w-sm w-full items-center space-y-4">
              <Text className="text-red-500 font-extrabold text-sm">🚨 ALERT DARURAT</Text>
              <Text className="text-xs text-slate-500 text-center">
                Permintaan stok darah CITO golongan O masuk dari RSU Abdul Moeloek!
              </Text>
              
              <TouchableOpacity 
                onPress={() => setShowModal(false)}
                className="w-full bg-slate-800 py-3 rounded-2xl items-center"
              >
                <Text className="text-white font-bold text-xs">Pahami Peringatan</Text>
              </TouchableOpacity>
            </View>
          </View>
        </Portal>
      )}

    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Masalah visual apa yang dipecahkan oleh penggunaan library **Portal** di React Native saat merakit dialog modal di dalam kontainer yang berukuran kecil?
2. Buka kode `src/app/_layout.tsx (atau app/_layout.tsx)` di atas. Sebutkan nama komponen provider yang wajib diselimutkan pada halaman root agar portal bisa bekerja memindahkan elemen visual.
3. Apakah elemen yang ditaruh di dalam tag `<Portal>` tetap bisa membaca variabel state local `showModal` yang berada di luar tag tersebut? Jelaskan hubungannya dengan hierarki memori data React.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Mengatasi Bug Potong Visual (Overflow/Z-index issues)
*   **Masalah Overflow**: Pada perangkat mobile, jika komponen induk memiliki gaya `overflow: 'hidden'` dan radius sudut `borderRadius: 30`, maka seluruh komponen anak di dalamnya yang posisinya meluber keluar secara fisik **akan terpotong hilang**. 
*   Portal memotong jalur hierarki pohon render visual tersebut, memindahkan lokasi rendering fisik modal ke root teratas aplikasi, membebaskan modal dari batasan CSS pembatas induknya tanpa merusak struktur kodenya.

### Jawaban 2: PortalProvider
*   Komponen tersebut bernama **`PortalProvider`**.

### Jawaban 3: Kelestarian State React
*   **Ya, Tetap Bisa Membaca**: Meskipun secara fisik elemen JSX modal tersebut digambar oleh engine di luar kontainer kartu, di level arsitektur memori React virtual DOM, modal tersebut **tetap terdaftar sebagai anak kandung komponen `KartuCitoKecil`**. 
*   Oleh karena itu, modal tetap bisa membaca state reaktif, merubah state `setShowModal(false)`, dan mengonsumsi data props yang dikirim dari atasnya secara normal tanpa putus komunikasi data.
```
[FASE 2: HARI 31 SUKSES]
```
