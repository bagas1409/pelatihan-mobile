# Hari 25: Sensor Tombol Back Android (BackHandler)
## Mencegah Petugas Tidak Sengaja Keluar Dari Aplikasi Saat Menekan Tombol Back Fisik Android

Selamat datang di Hari 25! Hari ini kita akan mempelajari cara menangani **Sensor Tombol Back Fisik** khusus untuk handphone Android menggunakan **`BackHandler`** API.

Berbeda dengan iPhone yang tidak memiliki tombol fisik penjelajah, handphone Android memiliki tombol navigasi segitiga di bagian bawah layar atau kontrol usapan geser pinggir (*Android Gesture Navigation*) yang berfungsi untuk kembali. Jika petugas UDD PMI sedang berada di halaman Beranda Utama dan menekan tombol back fisik tersebut secara tidak sengaja, sistem Android secara default akan **langsung menutup aplikasi keluar ke home screen HP**. Kita harus mencegat tindakan tersebut dan memunculkan dialog konfirmasi *"Apakah Anda yakin ingin keluar?"*.

---

## 1. Mengenal Event Listener `BackHandler`

React Native menyediakan modul **`BackHandler`** untuk mendengarkan event klik tombol back fisik Android:
*   **Cara Mencegat**: Kita mendaftarkan fungsi sensor menggunakan `BackHandler.addEventListener('hardwareBackPress', callback)`.
*   **Nilai Return Callback**:
    *   Jika fungsi kita mengembalikan nilai **`true`**: Sistem Android diperintahkan untuk **mengabaikan** klik tersebut (aplikasi tidak akan keluar/tutup). Kita bisa menyisipkan dialog konfirmasi kustom kita di sini.
    *   Jika mengembalikan nilai **`false`**: Sistem Android akan memproses penutupan rute default (aplikasi ditutup).

---

## 2. Studi Kasus PMI: Konfirmasi Keluar di Halaman Utama (`app/index.tsx`)

Mari kita sisipkan pencegat tombol back fisik Android ini menggunakan React hook `useEffect`:

```tsx
// app/index.tsx
import React, { useEffect } from 'react';
import { View, Text, BackHandler, Alert } from 'react-native';

export default function DashboardUtamaAndroid() {

  useEffect(() => {
    // 1. FUNGSI PENCEGAT TOMBOL BACK
    const handleTombolBackFisik = () => {
      // Tampilkan dialog pop-up konfirmasi resmi
      Alert.alert(
        '🚪 Log Out Aplikasi',
        'Apakah Anda yakin ingin keluar dari aplikasi DonorKu?',
        [
          { text: 'Batal', onPress: () => null, style: 'cancel' },
          { text: 'Ya, Keluar', onPress: () => BackHandler.exitApp() } // Matikan & matikan proses aplikasi
        ]
      );
      
      return true; // PENTING: Mengembalikan true agar tombol back default Android dimatikan!
    };

    // 2. DAFTARKAN LISTENER DI SISTEM OPERASI ANDROID
    const listenerBack = BackHandler.addEventListener(
      'hardwareBackPress',
      handleTombolBackFisik
    );

    // 3. MEMBERSIHKAN SENSOR SAAT PETUGAS PINDAH KE HALAMAN LAIN (UNMOUNT)
    return () => {
      console.log("Membunuh sensor back listener. RAM steril.");
      listenerBack.remove(); // Cabut detektor sensor dari RAM!
    };
  }, []);

  return (
    <View className="flex-1 bg-slate-50 justify-center items-center p-6 text-center">
      <Text className="text-sm font-bold text-slate-800">Dashboard Utama Petugas</Text>
      <Text className="text-xs text-slate-400 mt-1">
        Coba tekan tombol back fisik Android di bawah HP Anda untuk menguji sensor pencegat.
      </Text>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Amati baris pengembalian nilai: `return true;` di dalam fungsi `handleTombolBackFisik` di atas.
2. Jelaskan akibatnya jika kita lupa menuliskan `return true;` di dalam fungsi pencegat tersebut. Apakah dialog konfirmasi `Alert.alert` tetap muncul? Jelaskan perilakunya.
3. Mengapa kita wajib memanggil fungsi `.remove()` di bagian return cleanup `useEffect`? Jelaskan bahaya kebocoran memori (*Memory Leak*) di level sistem operasi HP.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pentingnya Return True pada BackHandler Listener
*   **Dialog Tetap Muncul Namun Aplikasi Tetap Tutup**: Jika Anda lupa menuliskan `return true;` (atau mengembalikan nilai `false/void`), dialog `Alert.alert` konfirmasi **akan tetap menyala berkedip di layar**. 
*   Namun, karena sistem Android tidak menerima pesan pembatalan rute, sistem Android secara simultan **tetap akan mengeksekusi penutupan aplikasi ke layar utama HP**, membuat dialog konfirmasi kita menjadi tidak berguna karena aplikasi sudah tertutup di belakang layar.

### Jawaban 3: Mencegah Memory Leak Sensor
*   **Bahaya Sensor Melayang**: Pendaftaran event listener `hardwareBackPress` terikat pada memori RAM global sistem operasi Android. 
*   Jika petugas berpindah dari halaman Beranda ke Halaman Edit, namun kita lupa memanggil `listenerBack.remove()`, sensor detektor tersebut **akan tetap aktif mengambang di memori RAM**. 
*   Saat petugas berada di Halaman Edit dan menekan tombol back (berniat kembali ke Beranda), sensor lama dari halaman Beranda akan salah menangkap event tersebut dan justru memicu dialog *"Apakah Anda yakin ingin keluar dari aplikasi?"*, mengacaukan navigasi rute dan membebani kinerja RAM handphone petugas.
```
[FASE 2: HARI 25 SUKSES]
```
