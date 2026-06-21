# Hari 20: Navigasi Terprogram (Programmatic Navigation)
## Menggunakan Router Helper push(), replace(), Serta back() Di Dalam Logic Fungsi JavaScript/TypeScript

Selamat datang di Hari 20! Hari ini kita akan mempelajari teknik **Navigasi Terprogram** (Programmatic Navigation) menggunakan helper `router` bawaan Expo Router.

Di Hari 16 s.d 18 kita berpindah halaman menggunakan tag visual `<Link>`. Namun di dunia nyata, perpindahan halaman seringkali dipicu oleh **logika program** setelah suatu proses aksi sukses diselesaikan (contoh: *setelah petugas berhasil menekan tombol simpan form donor, panggil API sukses, barulah arahkan layar kembali secara paksa ke beranda*). Kita tidak bisa memakai tag `<Link>` untuk kasus ini. Kita wajib menggunakan helper `router`.

---

## 1. Tiga Fungsi Utama Object `router`

*   **`router.push('/rute')`**: Mendorong halaman baru ke atas tumpukan stack (seperti mengklik link biasa). Pengguna bisa kembali ke halaman lama.
*   **`router.replace('/rute')`**: Mengganti halaman aktif saat ini secara total dan menghapusnya dari tumpukan. Pengguna **tidak bisa kembali** ke halaman lama saat menekan tombol back (sangat cocok untuk rute setelah Login berhasil agar user tidak bisa kembali ke form login).
*   **`router.back()`**: Membakar halaman aktif saat ini dan mundur satu langkah ke halaman di bawahnya (sama dengan menekan tombol back fisik).

---

## 2. Studi Kasus PMI: Alur Transaksi Otorisasi Pendaftaran

Mari kita buat simulasi logika submit form pendaftaran relawan donor yang memanfaatkan fungsi navigasi terprogram:

```tsx
// components/FormOtorisasiKirim.tsx
import React, { useState } from 'react';
import { View, Text, TouchableOpacity, ActivityIndicator } from 'react-native';
// Mengimpor helper router dari Expo Router
import { router } from 'expo-router';

export default function FormOtorisasiKirim() {
  const [loading, setLoading] = useState(false);

  const prosesKirimData = () => {
    setLoading(true);

    // Simulasi jeda waktu kirim API ke backend selama 2 detik
    setTimeout(() => {
      setLoading(false);

      // A. Arahkan secara paksa dan hapus riwayat tumpukan agar tidak bisa di-back
      // Mencegah petugas tidak sengaja mendaftar ulang relawan yang sama
      router.replace('/(tabs)/riwayat');

    }, 2000);
  };

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-200 space-y-4">
      <Text className="font-bold text-slate-800 text-sm">Konfirmasi Pengiriman Laporan</Text>
      <Text className="text-xs text-slate-400">
        Data akan dikirim ke server pusat PMI Lampung secara terenkripsi.
      </Text>

      <TouchableOpacity 
        onPress={prosesKirimData}
        disabled={loading}
        className="w-full bg-emerald-500 py-3.5 rounded-2xl items-center justify-center flex-row space-x-2 disabled:bg-slate-200"
      >
        <!-- Indikator lingkaran berputar bawaan mobile jika loading aktif -->
        {loading && <ActivityIndicator size="small" color="#ffffff" />}
        
        <Text className="text-white font-extrabold text-xs uppercase tracking-wider">
          {loading ? 'Kirim Data...' : 'Kirim Ke Server'}
        </Text>
      </TouchableOpacity>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa perbedaan mendasar antara `router.push('/dashboard')` dengan `router.replace('/dashboard')` dari segi tumpukan riwayat rute (*History Stack*)?
2. Buka kode `prosesKirimData` di atas. Mengapa kita menggunakan `router.replace` untuk mengalihkan halaman setelah kirim data sukses, bukannya menggunakan `router.push`?
3. Sebutkan nama komponen bawaan React Native yang bertugas menggambar animasi lingkaran berputar (*Loading Spinner*) pada kode tombol di atas.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Perbedaan Push vs Replace
*   **`push`**: Menumpuk halaman baru. Halaman lama tetap disimpan di memori bawah. Menekan tombol back akan mengembalikan user ke halaman asal.
*   **`replace`**: Memotong dan menggantikan halaman lama di tumpukan dengan halaman baru. Halaman lama dibuang permanen dari memori RAM. Menekan back tidak akan mengembalikan user ke halaman asal, melainkan akan keluar dari aplikasi jika tidak ada halaman lain di bawahnya.

### Jawaban 2: Keamanan Alur Form (Duplicate Submission Prevention)
*   **Mencegah Klik Ganda**: Setelah data berhasil terkirim, kita menggunakan `router.replace` agar jika petugas tidak sengaja menekan tombol back fisik Android di HP-nya, mereka tidak akan dikembalikan ke formulir penginputan kosong yang sama. 
*   Ini mencegah resiko petugas mengisi ulang data yang sama berulang-kali secara tidak sengaja, menjaga kebersihan data transaksi UDD PMI.

### Jawaban 3: Komponen Loading Spinner
*   Komponen tersebut bernama **`ActivityIndicator`**. Kita bisa menyetel ukurannya (`size="small"` atau `"large"`) dan warnanya (`color`) sesuai tema tombol.
```
[FASE 2: HARI 20 SUKSES]
```
