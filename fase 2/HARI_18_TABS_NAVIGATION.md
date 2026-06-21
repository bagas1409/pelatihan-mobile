# Hari 18: Navigasi Tab Bawah (Tabs Navigation)
## Menyusun Menu Navigasi Utama Aplikasi Berada Di Bagian Bawah Layar Menggunakan Tabs Layout Grouping

Selamat datang di Hari 18! Hari ini kita akan mempelajari model navigasi paling populer untuk tata letak halaman utama aplikasi mobile: **Tabs Navigation** (Navigasi Tab Bawah).

Sebagian besar aplikasi mobile menaruh menu-menu utama mereka (seperti Beranda, Riwayat Transaksi, dan Profil Pengguna) berjejer mendatar di bagian bawah layar agar mudah dijangkau oleh ibu jari pengguna. Kita akan merakit menu tab bawah ini menggunakan folder grup khusus di Expo Router.

---

## 1. Struktur Folder Tab Grouping `(tabs)`

Di Expo Router, kita bisa mengelompokkan halaman-halaman yang memiliki menu tab bawah di dalam folder bernama **`app/(tabs)/`**. Tanda kurung bulat `( )` menginstruksikan Expo Router agar folder tersebut bertindak sebagai pengelompokan navigasi internal tanpa mempengaruhi nama URL rute.

Struktur foldernya adalah sebagai berikut:
```
app/
├── (tabs)/
│   ├── _layout.tsx      <-- Konfigurasi visual menu tab bawah (ikon, label, warna)
│   ├── index.tsx        <-- Halaman tab 1 (Beranda)
│   ├── riwayat.tsx      <-- Halaman tab 2 (Riwayat Donor)
│   └── profil.tsx       <-- Halaman tab 3 (Profil Petugas)
└── _layout.tsx          <-- Layout utama root pembungkus Stack
```

---

## 2. Mengode Konfigurasi Tab di `app/(tabs)/_layout.tsx`

Buka file layout di dalam folder `(tabs)` dan susun tab navigasinya:

```tsx
// app/(tabs)/_layout.tsx
import React from 'react';
import { Tabs } from 'expo-router';
import { Text } from 'react-native';

export default function LayoutTabBawah() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: '#ef4444', // Warna ikon/teks saat menu aktif (Merah PMI)
        tabBarInactiveTintColor: '#94a3b8', // Warna saat menu tidak aktif (Abu-abu Slate)
        tabBarStyle: {
          borderTopWidth: 1,
          borderTopColor: '#e2e8f0',
          height: 60,
          paddingBottom: 8,
          paddingTop: 8,
        },
      }}
    >
      <!-- TAB 1: BERANDA -->
      <Tabs.Screen
        name="index"
        options={{
          title: 'Beranda',
          // Menampilkan ikon kustom berupa teks emoji sederhana
          tabBarIcon: ({ color }) => (
            <Text style={{ color }} className="text-lg">📊</Text>
          ),
        }}
      />

      <!-- TAB 2: RIWAYAT -->
      <Tabs.Screen
        name="riwayat"
        options={{
          title: 'Riwayat',
          tabBarIcon: ({ color }) => (
            <Text style={{ color }} className="text-lg">📦</Text>
          ),
        }}
      />

      <!-- TAB 3: PROFIL -->
      <Tabs.Screen
        name="profil"
        options={{
          title: 'Profil',
          tabBarIcon: ({ color }) => (
            <Text style={{ color }} className="text-lg">👤</Text>
          ),
        }}
      />
    </Tabs>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa kegunaan penulisan tanda kurung pada nama folder **`(tabs)`** di dalam struktur Expo Router? Jelaskan efeknya pada rute URL halaman `/index`.
2. Jelaskan fungsi dari properti `tabBarActiveTintColor` pada komponen `<Tabs>` di atas.
3. Sebutkan nama berkas file yang bertindak sebagai halaman utama pertama kali saat folder `(tabs)` diakses oleh sistem rute.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Konsep Route Groups (Folder Tanda Kurung)
*   **Tanpa Mengubah URL**: Tanda kurung `(tabs)` memberi tahu router untuk mengabaikan nama folder tersebut di rute URL browser/aplikasi. 
*   Sehingga untuk mengakses file `app/(tabs)/profil.tsx`, kita cukup menuliskan tautan rute **`/profil`** (bukan `/tabs/profil`), menghemat penulisan string URL dan menyederhanakan arsitektur navigasi.

### Jawaban 2: Indikator Menu Aktif
*   **`tabBarActiveTintColor`** mewarnai teks dan ikon menu yang sedang dibuka aktif oleh jari user. Pada kode di atas, jika petugas sedang membuka tab Beranda, ikon grafik `📊` dan teks "Beranda" akan berwarna merah menyala, menegaskan posisi menu aktif secara visual.

### Jawaban 3: File index.tsx
*   Halaman default awal yang dimuat adalah file **`index.tsx`** yang berada langsung di bawah direktori folder `(tabs)`.
