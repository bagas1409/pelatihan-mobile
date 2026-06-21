# Hari 24: Layar Pop-up Melayang (Modal Screen)
## Merancang Halaman Formulir Filter Cepat Stok Darah Menggunakan Presentation Modal Expo Router

Selamat datang di Hari 24! Hari ini kita akan mempelajari cara merancang **Modal Screen** (Layar Pop-up Melayang) menggunakan fitur presentation modal di Expo Router.

Di antarmuka mobile premium, ada tipe halaman yang tidak dibuka dengan menumpuk ke samping (seperti detail rute biasa), melainkan **meluncur naik dari bawah ke atas** menutupi sebagian layar (layaknya tumpukan kartu yang ditarik ke atas). Ini sangat cocok untuk membuat halaman formulir filter pencarian cepat stok darah atau jendela detail mini relawan.

---

## 1. Konsep Presentation Mode "modal"

Pada Stack Navigator, kita bisa menyetel rute tertentu agar bertindak sebagai modal dengan mengubah nilai properti **`presentation: 'modal'`** di dalam opsi rutenya.
*   **Android**: Layar meluncur dari bawah dengan efek meredup tipis di belakangnya.
*   **iOS**: Layar utama di belakang akan menciut mundur secara elegan membentuk visual kartu 3D tumpuk (*Card Stack Effect*), memberikan keindahan UX yang sangat tinggi.

---

## 2. Studi Kasus PMI: Jendela Filter Cepat Golongan Darah

### Langkah 1: Daftarkan Rute Modal di Layout Global (`app/_layout.tsx`)

Buka file layout utama global Anda dan tambahkan rute group khusus modal:

```tsx
// app/_layout.tsx
import React from 'react';
import { Stack } from 'expo-router';

export default function LayoutDinas() {
  return (
    <Stack>
      <!-- Halaman Utama Dashboard -->
      <Stack.Screen name="index" options={{ title: 'Beranda' }} />
      
      <!-- 
        DAFTARKAN LOKASI MODAL SCREEN
        presentation: 'modal' -> Merubah transisi geser samping menjadi meluncur naik dari bawah
      -->
      <Stack.Screen 
        name="filter-stok" 
        options={{ 
          presentation: 'modal', 
          title: 'Saring Stok Darah',
          headerStyle: { backgroundColor: '#f8fafc' },
          headerTintColor: '#0f172a'
        }} 
      />
    </Stack>
  );
}
```

---

### Langkah 2: Buat Halaman Modal (`app/filter-stok.tsx`)

Tulis kode isi formulir penyaringan di file **`app/filter-stok.tsx`**:

```tsx
// app/filter-stok.tsx
import React from 'react';
import { View, Text, TouchableOpacity } from 'react-native';
import { router } from 'expo-router';

export default function HalamanFilterStok() {
  
  const terapkanFilter = (golongan: string) => {
    // 1. Kirim parameter filter kembali ke index halaman asal
    router.replace({
      pathname: '/',
      params: { filterGolongan: golongan }
    });
  };

  return (
    <View className="flex-1 bg-white p-6 space-y-6 justify-center">
      <Text className="text-center font-bold text-slate-800 text-sm">Pilih Golongan Darah Yang Dicari</Text>

      <View className="grid grid-cols-2 gap-4">
        <!-- Pilihan A, B, AB, O -->
        {['A', 'B', 'AB', 'O'].map((gol) => (
          <TouchableOpacity
            key={gol}
            onPress={() => terapkanFilter(gol)}
            className="bg-red-50 hover:bg-red-100 p-6 rounded-3xl border border-red-100 items-center justify-center"
          >
            <Text className="text-red-500 font-extrabold text-lg">Golongan {gol}</Text>
          </TouchableOpacity>
        ))}
      </View>

      <!-- Tombol Batal/Tutup Modal -->
      <TouchableOpacity 
        onPress={() => router.back()} // Menutup modal, meluncur kembali ke bawah
        className="w-full bg-slate-100 py-3.5 rounded-2xl items-center mt-6"
      >
        <Text className="text-slate-600 font-bold text-xs">Batalkan Pencarian</Text>
      </TouchableOpacity>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa perbedaan arah arah gerak transisi visual (*Screen Transition*) saat kita membuka halaman biasa vs membuka halaman berstatus `presentation: 'modal'`?
2. Buka kode `app/filter-stok.tsx` di atas. Bagaimana cara program menutup halaman modal tersebut secara terprogram agar meluncur kembali hilang ke bawah?
3. Sebutkan kelebihan visual dari modal screen pada sistem operasi iOS Apple (iPhone).

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Perbedaan Arah Transisi Layar
*   **Halaman Biasa**: Layar baru meluncur masuk secara mendatar dari **Kiri ke Kanan** (iOS) atau membesar naik dari tengah (Android).
*   **Halaman Modal**: Layar baru meluncur tegak lurus dari **Bawah ke Atas** menutupi layar utama.

### Jawaban 2: Penutupan Modal (Dismiss Modal)
*   Menggunakan fungsi **`router.back()`** (atau `router.dismiss()`). Perintah ini memberi sinyal ke navigator untuk membakar layar modal teratas dan menurunkannya kembali hilang ke arah bawah.

### Jawaban 3: Visual Premium iOS Card Stack
*   Pada iOS (iPhone), modal tidak sekedar menutupi layar, melainkan layar utama di belakang akan **berubah melengkung mengecil di latar belakang (Scale Down)** dengan bayangan hitam halus di tepinya. 
*   Ini menegaskan hierarki spasial bahwa modal tersebut hanyalah lembaran menu sementara (*Temporary Overlay Card*) yang menumpang di atas dashboard utama, meningkatkan kemewahan interaksi visual aplikasi mobile PMI.
```
[FASE 2: HARI 24 SUKSES]
```
