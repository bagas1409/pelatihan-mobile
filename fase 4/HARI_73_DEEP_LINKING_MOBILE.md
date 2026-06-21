# Hari 73: Integrasi Deep Linking
## Menghubungkan Tautan Eksternal URL WhatsApp/SMS Agar Membuka Rute Spesifik Di Dalam Aplikasi Mobile Anda

Selamat datang di Hari 73! Hari ini kita akan masuk ke dalam sihir interkoneksi antarmuka modern yang bernama **Deep Linking**.

Skenario: Markas besar PMI menemukan relawan dengan darah AB yang cocok, lalu mengirimkan tautan link via SMS ke nomor HP sang relawan: *"Mohon segera verifikasi persetujuan CITO Anda dengan menekan link ini: donorku://cito/verifikasi/88"*.
Jika fitur Deep Linking belum dipasang, link tersebut hanyalah tulisan mati. Setelah dipasang, saat link tersebut ditekan di dalam SMS/WhatsApp, handphone akan langsung otomatis melompat masuk mengebrak membuka layar khusus halaman aplikasi DonorKu Anda tanpa perlu membuka browser!

---

## 1. Skema Pengaturan Dasar (URI Scheme) di Expo

Langkah pertama yang paling wajib adalah menetapkan nama unik pemanggil dewa (*URI Scheme*) di dalam file pengaturan konfigurasi jantung aplikasi **`app.json`**. Nama scheme inilah yang akan menjadi "domain" khusus pemanggil aplikasi Anda.

Buka file `app.json`, pastikan di bawah objek `expo` terdapat atribut ini:
```json
{
  "expo": {
    "name": "DonorKu PMI",
    "slug": "donorku-pmi",
    "scheme": "donorku", 
    ...
  }
}
```
*Dengan skema `donorku`, OS HP kini akan mengintai setiap link klik yang berawalan `donorku://...` dan mengarahkannya untuk memicu kebangkitan aplikasi Anda.*

---

## 2. Keajaiban Otomatis File-Based Routing (Expo Router)

Berbeda dengan React Native Navigator klasik (di mana Anda harus menulis panjang kode regex detektor), pustaka canggih Expo Router secara alamiah magis **otomatis menterjemahkan jalur URL Deep Link menabrak ke jalur file folder halaman Anda!**

1.  Relawan di WhatsApp mengklik: `donorku://stok/detail/100`
2.  Expo Router mencegat aplikasi bangun dari tidur saku.
3.  Aplikasi otomatis mencari rute file: `app/stok/detail/[id].tsx` dengan nilai variabel parameter `id` terisi nilai tangkapan URL string `'100'`.

---

## 3. Studi Kasus PMI: Komponen Pendeteksi Parameter URL Dari Link Cepat Luar (`app/jalur-cepat/[kode].tsx`)

Mari buat halaman konfirmasi CITO darurat rahasia yang tersembunyi yang ditujukan spesial untuk langsung dibuka diserang mendadak dari link klik WhatsApp relawan luar!

```tsx
// app/jalur-cepat/[kode].tsx
import React, { useEffect, useState } from 'react';
import { View, Text, TouchableOpacity, ActivityIndicator } from 'react-native';
// Mengimpor penarik parameter dari URL Address bar
import { useLocalSearchParams, router } from 'expo-router';

export default function LayarJalurCepatDariLinkLuar() {
  // 1. TANGKAP DATA PARAMETER DARI DEEP LINK
  // Jika link-nya adalah donorku://jalur-cepat/A-POS-DARURAT
  // Maka properti kunci 'kode' ini akan secara instan bernilai "A-POS-DARURAT"
  const { kode } = useLocalSearchParams<{ kode: string }>();

  const [memproses, setMemproses] = useState(false);

  const konfirmasiSetujuOtomatis = () => {
    setMemproses(true);
    setTimeout(() => {
      setMemproses(false);
      // Pindahkan ke dashboard setelah selesai!
      router.replace('/(tabs)');
    }, 1500);
  };

  return (
    <View className="flex-1 bg-red-50 justify-center items-center p-6 space-y-6">
      
      <View className="w-20 h-20 bg-red-100 rounded-full items-center justify-center border-4 border-white shadow-lg">
        <Text className="text-3xl">🚀</Text>
      </View>

      <View className="items-center space-y-2">
        <Text className="font-black text-slate-800 text-lg">Panggilan Akselerasi Cepat!</Text>
        <Text className="text-xs text-slate-500 text-center leading-relaxed">
          Anda mengakses layar ini dari tautan luar eksternal sistem dengan rujukan persetujuan sandi unik:
        </Text>
        
        {/* TAMPILKAN SANDI RAHASIA TANGKAPAN DARI LINK WHATSAPP! */}
        <View className="bg-slate-900 px-4 py-2 rounded-xl mt-2">
          <Text className="text-white font-mono font-bold text-xs">{kode}</Text>
        </View>
      </View>

      <TouchableOpacity 
        onPress={konfirmasiSetujuOtomatis}
        className="w-full max-w-xs bg-red-500 py-3.5 rounded-2xl items-center flex-row justify-center space-x-2"
      >
        {memproses && <ActivityIndicator color="#ffffff" size="small" />}
        <Text className="text-white font-extrabold text-xs uppercase tracking-widest">
          {memproses ? 'Menyambungkan...' : 'Setujui Panggilan'}
        </Text>
      </TouchableOpacity>

    </View>
  );
}
```

---

## 4. Latihan Soal Mandiri
1. Buka file konfigurasi jantung `app.json`. Apa efek magis penulisan nilai kata pendek `"scheme": "donorku"` di atribut deklarasi berkas setting aplikasi tersebut bagi kernel sistem OS handphone petugas?
2. Apabila petugas relawan mendapat kiriman tautan link SMS berbunyi URL alamat klik `donorku://jalur-cepat/XYZ-999`, lantas mereka mengkliknya, sebutkan alur lengkap kejadian apa yang terjadi pada aplikasi DonorKu dari keadaan tidur hingga merender layar!
3. Sebutkan nama helper hook Expo Router yang sangat berguna bertugas bertindak membedah, menangkap, mencerna isi kalimat kata acak `"XYZ-999"` di ujung tautan link tersebut ke dalam state logika variabel react.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Identifikasi Domain Scheme Resmi Hak Paten Aplikasi
*   **Deklarasi Monopoli Pintu Rute OS**: Menyuntikkan atribut nama scheme memberi instruksi paten sakti kepada Android Registry/iOS Registry di dalam mesin HP bahwa *"Kapanpun sang user menyentuh mengklik tulisan alamat link ajaib apa saja di WA/Telegram yang berawalan kata kunci donorku://, HARAP LANGSUNG bangunkan paksa dan buka seret aplikasi saya!"*

### Jawaban 2: Rantai Aksi Penelusuran Layar Transisi Deep Link (Deep Routing Pipe)
*   **Eksekusi File Based Routing System**: Layar HP berpindah dari mematikan view SMS. Sistem OS membangunkan aplikasi DonorKu. 
*   Expo Router di balik layar menelan jalur URL masuk `jalur-cepat/XYZ-999`, ia melompat pintar memotong rute awal index dashboard dan langsung menyetir layar meluncur ke dalam pintu file `app/jalur-cepat/[kode].tsx` sembari mengantarkan paket parameter isi tulisan string `'XYZ-999'`.

### Jawaban 3: Pengekstrak Parameter Hook `useLocalSearchParams`
*   Hook serba guna bernama **`useLocalSearchParams()`**. Hook serbaguna ini memiliki fungsional ganda: bukan hanya sakti meraba membaca parameter dari navigasi intern `router.push()` (seperti di Hari 21), tetapi juga dapat menangkap muntahan string parameter murni tak terduga yang datang dari tautan klik luar platform HP!
```
[FASE 4: HARI 73 SUKSES]
```
