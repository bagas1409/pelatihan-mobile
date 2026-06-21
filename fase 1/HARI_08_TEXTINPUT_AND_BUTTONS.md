# Hari 8: Input Teks & Komponen Tombol Reaktif
## Menangani Masukan Formulir Dengan TextInput Serta Memahami Perbedaan TouchableOpacity Dengan Pressable

Selamat datang di Hari 8! Hari ini kita akan mempelajari cara menangani input ketikan user menggunakan komponen **`TextInput`** dan merakit tombol interaktif reaktif menggunakan **`TouchableOpacity`** dan **`Pressable`**.

Pada portal DonorKu PMI Lampung, petugas membutuhkan form untuk mengetik nama relawan, nomor KTP, serta tombol kirim yang memberikan umpan balik visual instan (seperti efek meredup saat ditekan) agar aplikasi terasa responsif dan hidup.

---

## 1. Perbedaan Tombol: TouchableOpacity vs Pressable

React Native tidak memiliki komponen `<button>` HTML. Kita menggunakan pembungkus area klik khusus:
*   **`TouchableOpacity`**: Tombol bawaan klasik. Saat ditekan, tingkat transparansi (*Opacity*) elemen anak di dalamnya akan meredup otomatis (keindahan feedback bawaan). Sangat praktis untuk tombol standar.
*   **`Pressable`**: Komponen tombol modern yang lebih canggih. Dia tidak memiliki efek visual bawaan, namun menyediakan parameter status interaktif (`pressed`) yang memungkinkan kita merancang animasi warna CSS Tailwind kustom saat ditekan (*Hover/Active states*).

---

## 2. Studi Kasus PMI: Form Input Cepat Pendaftaran Donor

Mari kita buat komponen form pendaftaran dengan validasi state reaktif sederhana menggunakan NativeWind:

```tsx
// components/FormPendaftaranCepat.tsx
import React, { useState } from 'react';
import { View, Text, TextInput, TouchableOpacity, Alert } from 'react-native';

export default function FormPendaftaranCepat() {
  // 1. STATE PENAMPUNG INPUTAN
  const [nama, setNama] = useState('');
  const [ktp, setKtp] = useState('');

  const handleKirim = () => {
    if (!nama || !ktp) {
      Alert.alert('🚨 Peringatan', 'Mohon lengkapi seluruh kolom formulir!');
      return;
    }
    Alert.alert('✓ Sukses', `Relawan bernama ${nama} berhasil didaftarkan!`);
    // Reset form setelah sukses
    setNama('');
    setKtp('');
  };

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4">
      <Text className="font-bold text-slate-800 text-sm">✍️ Pendaftaran Relawan Baru</Text>

      <!-- 2. INPUT NAMA -->
      <View className="space-y-1.5">
        <Text className="text-[10px] font-bold text-slate-500 uppercase">Nama Lengkap</Text>
        <TextInput
          value={nama}
          onChangeText={(teks) => setNama(teks)} // Set state nama secara reaktif
          placeholder="Ketik nama lengkap sesuai KTP..."
          className="w-full px-4 py-3 rounded-2xl bg-slate-50 border border-slate-200 text-xs outline-none focus:border-red-500"
          placeholderTextColor="#94a3b8" // Warna teks petunjuk placeholder
        />
      </View>

      <!-- 3. INPUT NIK KTP -->
      <View className="space-y-1.5">
        <Text className="text-[10px] font-bold text-slate-500 uppercase">Nomor NIK KTP</Text>
        <TextInput
          value={ktp}
          onChangeText={(teks) => setKtp(teks)}
          placeholder="16 Digit NIK KTP..."
          keyboardType="numeric" // Memaksa keyboard HP memunculkan angka saja!
          maxLength={16} // Batasi ketikan maksimal 16 huruf saja
          className="w-full px-4 py-3 rounded-2xl bg-slate-50 border border-slate-200 text-xs outline-none focus:border-red-500"
          placeholderTextColor="#94a3b8"
        />
      </View>

      <!-- 4. TOMBOL TOUCHABLEOPACITY -->
      <TouchableOpacity 
        onPress={handleKirim}
        activeOpacity={0.8} // Mengatur tingkat keredupan saat ditekan (0.0 sangat redup, 1.0 tidak redup)
        className="w-full bg-red-500 py-3.5 rounded-2xl items-center justify-center mt-2 shadow-sm"
      >
        <Text className="text-white font-extrabold text-xs tracking-wider uppercase">Daftarkan Sekarang</Text>
      </TouchableOpacity>

    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Amati parameter properti `keyboardType="numeric"` pada TextInput NIK KTP di atas.
2. Jelaskan fungsi dari parameter `keyboardType="numeric"` tersebut bagi kemudahan pengisian form (*User Experience*) di perangkat handphone asli.
3. Mengapa kita disarankan menggunakan `TouchableOpacity` daripada membuat area tombol menggunakan `<View>` yang dipasang event `onTouchStart` manual?

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kegunaan keyboardType
*   **Optimalisasi Keyboard Fisik**: Saat kursor masuk ke input NIK KTP, sistem operasi HP akan **otomatis membuka panel keyboard numerik khusus angka saja (Numeric Keypad)**, bukan keyboard huruf alfabet biasa. 
*   Ini meningkatkan kenyamanan petugas karena mereka tidak perlu repot menekan tombol `?123` untuk beralih ke mode angka secara manual, serta meminimalisir kesalahan ketik huruf abjad pada kolom nomor KTP.

### Jawaban 3: Otoritas Event Klik & Aksesibilitas HP
*   `TouchableOpacity` dan `Pressable` memiliki kemampuan menangani sistem penundaan sentuhan (*Gesture Responder System*) bawaan Android/iOS. 
*   Mereka mampu membedakan apakah pengguna benar-benar berniat mengklik tombol (*Tap*), atau hanya tidak sengaja menyentuh tombol saat berniat melakukan scroll layar ke bawah. Menggunakan `View` biasa dengan `onTouchStart` akan memicu eksekusi tombol secara tidak sengaja ketika pengguna hanya berniat men-scroll halaman, merusak kenyamanan navigasi aplikasi.
