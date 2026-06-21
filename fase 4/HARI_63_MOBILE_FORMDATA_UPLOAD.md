# Hari 63: Upload Data Biner (FormData Multipart)
## Mengunggah File Foto Bukti KTP Relawan Ke Server Backend Menggunakan Format FormData Axios Post

Selamat datang di Hari 63! Di dua hari sebelumnya (Hari 61 & 62), kita sukses mendapatkan **URI lokal gambar** dari perangkat HP. Hari ini, kita akan mempelajari cara merakit URI lokal tersebut agar bisa dikirim menyeberangi jaringan internet menuju server API Express.js.

Format JSON (`{ "nama": "Budi" }`) tidak diciptakan untuk mengirim serpihan ribuan angka data mentah biner piksel dari sebuah file gambar seberat 1 Megabyte. Server akan menolaknya. Kita wajib membungkus foto tersebut menggunakan kotak spesial bernama **`FormData`** dengan tipe header khusus: `multipart/form-data`.

---

## 1. Bagaimana FormData Mobile Bekerja?

Pada browser web, kita menempelkan objek `File`. Pada lingkungan React Native, kita tidak memiliki kelas `File`. Sebagai gantinya, **kita merekayasa objek JS khusus** di dalam `FormData` yang mendeskripsikan: `uri` file lokal di HP, tipe mime (`type`), dan nama acaknya (`name`).

```javascript
// Struktur rekayasa biner khusus React Native
const gambarLampiran = {
  uri: "file:///data/user/0/host.exp.exponent/cache/123.jpg",
  type: "image/jpeg",
  name: "foto_bukti_123.jpg",
};
```

---

## 2. Studi Kasus PMI: Mengunggah KTP Relawan ke Server Express

Mari rakit kode fungsi uploadnya dan kita tempelkan pada sebuah tombol "Upload Ke Server":

```tsx
// app/upload-ktp.tsx
import React, { useState } from 'react';
import { View, Text, TouchableOpacity, Alert, ActivityIndicator } from 'react-native';
import api from '../utils/api';
// Diasumsikan uriFoto diperoleh dari Hari 61 (Kamera) atau Hari 62 (Galeri)
import { uriFotoPilihan } from '../komponen/DummyPickerStore'; 

export default function LayarUploadKtp() {
  const [loading, setLoading] = useState(false);

  // 1. FUNGSI EKSEKUTOR UPLOAD BINER MULTIPART
  const unggahBerkasSekarang = async () => {
    if (!uriFotoPilihan) {
      Alert.alert('Data Kosong', 'Harap jepret foto KTP relawan terlebih dahulu!');
      return;
    }

    setLoading(true);

    try {
      console.log("[Upload] Menyiapkan paket kargo FormData...");
      
      // 2. DEKLARASI WADAH MULTIPART FORM DATA
      const kargoForm = new FormData();
      
      // Masukkan data identitas teks biasa
      kargoForm.append('nik', '1871029393939393');
      kargoForm.append('nama', 'Budi Relawan');

      // 3. MERAKIT STRUKTUR KHUSUS UNTUK DATA FILE GAMBAR BINER
      // Nama unik diperlukan agar file tidak saling timpa di server
      const namaUnik = `ktp_${Date.now()}.jpg`; 
      
      const fileBiner = {
        uri: uriFotoPilihan,
        type: 'image/jpeg',
        name: namaUnik
      } as unknown as Blob; // PENTING: Gunakan type cast ke Blob untuk menipu TS Compiler

      kargoForm.append('file_ktp', fileBiner);

      console.log("[Upload] Menembak API...");

      // 4. KIRIM PAYLOAD POST DENGAN HEADER KHUSUS BINER
      const respon = await api.post('/relawan/upload-ktp', kargoForm, {
        headers: {
          'Content-Type': 'multipart/form-data', // Wajib diubah menjadi multipart!
        }
      });

      if (respon.status === 201 || respon.status === 200) {
        Alert.alert('✓ Upload Berhasil', 'Foto KTP relawan telah tersimpan aman di cloud server PMI.');
      }

    } catch (error: any) {
      console.error("[Upload Error]:", error);
      Alert.alert('🚨 Upload Gagal', error.message || 'Koneksi terputus saat mentransfer gambar.');
    } finally {
      setLoading(false);
    }
  };

  return (
    <View className="flex-1 bg-white justify-center items-center p-6 space-y-4">
      <Text className="font-extrabold text-slate-800 text-sm">Validasi Identitas Berkas</Text>
      
      <TouchableOpacity 
        onPress={unggahBerkasSekarang}
        disabled={loading}
        className="w-full max-w-xs bg-red-500 py-3.5 rounded-2xl items-center flex-row justify-center space-x-2"
      >
        {loading && <ActivityIndicator color="#ffffff" size="small" />}
        <Text className="text-white font-extrabold text-xs uppercase">
          {loading ? 'Mengunggah Berkas...' : 'Kirim Foto Ke Server'}
        </Text>
      </TouchableOpacity>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari pengubahan properti header `'Content-Type': 'multipart/form-data'` pada opsi Axios request `api.post` di atas? Apa yang terjadi di server Express.js backend jika header ini dibiarkan sebagai JSON?
2. Buka blok baris deklarasi rakitan objek `fileBiner` di atas. Sebutkan keharusan menyertakan properti penentu format (seperti `type: 'image/jpeg'`) agar backend Multer kita mengenali jenis paket kargo yang dikirimkan.
3. Sebutkan kegunaan metode `.append(kunci, nilai)` pada instansiasi objek kelas `FormData`.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Pembuka Pintu Kargo Middleware Backend
*   **Identifikasi Tipe Paket Biner**: Standar web mewajibkan file biner gambar dikirimkan dengan tag `multipart/form-data`. 
*   **Efek Fatal Kesalahan Tipe**: Jika header ini tidak dikonfigurasi, Axios akan menggunakan header default JSON. Saat paket mendarat di sisi API Backend, middleware pengurai gambar di backend Anda (seperti `Multer` di Node.js Express) **akan membisu dan otomatis menolak membaca body request tersebut**, menyebabkan backend melempar error gagal meng-upload file karena mengira file biner tersebut adalah string teks kacau biasa.

### Jawaban 2: Identifikasi Ekstensi File
*   Menyisipkan MIME type `image/jpeg` memberi sinyal kuat kepada server web backend mengenai cara mengekstrak algoritma kompresi file tersebut. Jika dikosongkan, banyak server cloud keamanan ketat yang otomatis memblokir file tersebut karena mencurigainya sebagai eksekusi malware yang berkedok gambar.

### Jawaban 3: Memasukkan Barang Ke Kargo
*   Fungsi **`.append()`** adalah alat pemasak. Ibarat sedang menyusun kotak kardus paket logistik fisik, dia bertugas menambahkan satu barang (key dan value) ke dalam daftar manifestasi kargo di dalam objek FormData.
```
[FASE 4: HARI 63 SUKSES]
```
