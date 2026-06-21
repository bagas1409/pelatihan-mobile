# Hari 19: Navigasi Samping (Drawer Navigation)
## Menyusun Menu Geser Laci Samping Untuk Panel Admin Khusus Petugas UDD PMI

Selamat datang di Hari 19! Hari ini kita akan mempelajari model navigasi ketiga: **Drawer Navigation** (Navigasi Samping / Menu Laci).

Pada portal dinas mobile, tidak semua menu muat ditaruh di tab bawah (tab bawah idealnya maksimal hanya berisi 3 s.d 5 menu utama). Untuk menu pengaturan akun, riwayat audit database, log keluar, dan peta logistik, kita disarankan meletakkannya di dalam laci samping tersembunyi yang akan meluncur keluar jika pengguna mengusap (*Swipe*) jempol mereka dari ujung kiri layar ke kanan.

---

## 1. Pemasangan Library Drawer

Navigasi Drawer membutuhkan beberapa pustaka pendukung untuk mendeteksi gerakan gesekan jari (*Gesture Handler*):

Jalankan perintah penginstalan berikut di terminal proyek Anda:
```bash
npx expo install @react-navigation/drawer react-native-gesture-handler react-native-reanimated
```

Pastikan Anda menambahkan baris import gesture handler di baris **paling atas** file `app/_layout.tsx` (atau entry point):
```typescript
import 'react-native-gesture-handler';
```

---

## 2. Studi Kasus PMI: Membuat Laci Menu Dinas UDD

Sama seperti Tabs, kita bisa menggunakan folder grouping khusus bernama **`app/(drawer)/`** untuk mengelompokkan halaman laci samping.

### Berkas Layout: `app/(drawer)/_layout.tsx`
```tsx
// app/(drawer)/_layout.tsx
import React from 'react';
import { Drawer } from 'expo-router/drawer';
import { Text } from 'react-native';

export default function LayoutLaciSamping() {
  return (
    <Drawer
      screenOptions={{
        drawerActiveTintColor: '#ef4444', // Warna menu aktif merah PMI
        drawerInactiveTintColor: '#475569',
        headerStyle: {
          backgroundColor: '#ffffff',
        },
        headerTintColor: '#1e293b',
      }}
    >
      <!-- MENU 1: BERANDA -->
      <Drawer.Screen
        name="index"
        options={{
          drawerLabel: 'Dashboard Utama',
          title: 'UDD PMI Lampung',
          drawerIcon: ({ color }) => (
            <Text style={{ color }} className="text-sm">📊</Text>
          ),
        }}
      />

      <!-- MENU 2: PETA SEBARAN -->
      <Drawer.Screen
        name="peta"
        options={{
          drawerLabel: 'Peta Distribusi',
          title: 'Lokasi Rumah Sakit Mitra',
          drawerIcon: ({ color }) => (
            <Text style={{ color }} className="text-sm">📍</Text>
          ),
        }}
      />

      <!-- MENU 3: PENGATURAN -->
      <Drawer.Screen
        name="pengaturan"
        options={{
          drawerLabel: 'Pengaturan Akun',
          title: 'Setting Aplikasi',
          drawerIcon: ({ color }) => (
            <Text style={{ color }} className="text-sm">⚙️</Text>
          ),
        }}
      />
    </Drawer>
  );
}
```

Cobalah buka emulator/HP Anda. Geser jari Anda dari pojok kiri layar ke arah tengah kanan. Laci menu navigasi akan meluncur keluar dengan transisi animasi yang sangat mulus!

---

## 3. Latihan Soal Mandiri
1. Sebutkan nama 3 library tambahan yang wajib kita pasang untuk menyalakan fitur navigasi Drawer di proyek Expo.
2. Apa fungsi dari baris impor `'react-native-gesture-handler'` di baris paling atas entry point aplikasi?
3. Sebutkan kelebihan penempatan menu administratif (seperti "Pengaturan Akun" atau "Log Out") di dalam navigasi Drawer dibandingkan ditaruh di navigasi Tab Bawah.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Library Pendukung Drawer
*   Tiga library wajib: **`@react-navigation/drawer`**, **`react-native-gesture-handler`**, dan **`react-native-reanimated`**.

### Jawaban 2: Kegunaan Gesture Handler
*   **Sensor Gerakan Jari**: Library `react-native-gesture-handler` mengambil alih sensor sentuhan sistem operasi HP asli. 
*   Dia bertugas mengenali dengan cepat apakah usapan jari pengguna merupakan gerakan gulir ke bawah (*Scroll*) atau gerakan seret mendatar ke kanan (*Pan/Swipe*) untuk membuka pintu laci samping secara presisi tanpa hambatan bug lag.

### Jawaban 3: Mengurangi Beban Visual Layar (Clutter Reduction)
*   **Visual Ergonomic**: Tab bawah dirancang untuk menu yang diklik petugas **puluhan kali dalam sehari** (seperti input donor dan check stok). 
*   Menu seperti "Pengaturan Akun" atau "Log Keluar" hanya diakses sekali-kali (misalnya seminggu sekali). Menyembunyikan menu sekunder tersebut di dalam laci Drawer menjaga layar utama tetap steril, fokus pada fitur operasional penting, dan mereduksi kebingungan visual petugas saat bekerja cepat.
