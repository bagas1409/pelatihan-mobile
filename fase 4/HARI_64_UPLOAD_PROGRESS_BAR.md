# Hari 64: Penanganan Indikator Progress Upload
## Menampilkan Animasi Batang Persentase Visual Saat Mentransfer File Berukuran Besar Menggunakan Properti onUploadProgress Axios

Selamat datang di Hari 64! Hari ini kita akan menyempurnakan fitur Upload Data yang baru kita buat di Hari 63 dengan menyisipkan interaksi **Progress Bar (Bilah Indikator Kemajuan)**.

Saat petugas mengirimkan banyak foto bukti logistik CITO beresolusi tinggi di daerah pelosok yang koneksinya lemah, proses upload bisa memakan waktu 5 hingga 10 detik. Menampilkan UI loading melingkar biasa tidaklah cukup, petugas sering kali membatalkan layar karena menyangka aplikasinya hang macet (*Frozen State*). Kita akan menampilkan hitungan persentase kemajuan aliran paket biner (0% s.d 100%) agar user sabar dan tahu bahwa aplikasi aktif bergerak bekerja mengirim data di belakang layar.

---

## 1. Menangkap Aliran Progress di Axios

Perpustakaan Axios memiliki properti parameter ketiga bawaan yang sangat canggih yaitu **`onUploadProgress`**. Kita bisa menyisipkan sebuah fungsi callback di sini. Axios akan mengeksekusi fungsi ini berkali-kali setiap kali sepenggal paket memori byte gambar selesai dikirim menembus sinyal Wi-Fi HP ke server internet.

---

## 2. Studi Kasus PMI: Bar Persentase Pengiriman Berkas KTP Relawan

Mari kita modifikasi file upload di Hari 63 agar memiliki bar animasi persentase:

```tsx
// app/upload-progress.tsx
import React, { useState } from 'react';
import { View, Text, TouchableOpacity, Alert } from 'react-native';
import api from '../utils/api';
// Simulasi file
import { uriFotoPilihan } from '../komponen/DummyPickerStore'; 

export default function LayarUploadProgress() {
  const [isUploading, setIsUploading] = useState(false);
  const [persentaseUpload, setPersentaseUpload] = useState<number>(0); // State melacak angka 0 - 100

  const jalankanUploadBesar = async () => {
    setIsUploading(true);
    setPersentaseUpload(0);

    try {
      const form = new FormData();
      form.append('file_ktp', {
        uri: uriFotoPilihan,
        type: 'image/jpeg',
        name: 'ktp_hd.jpg'
      } as any);

      console.log("[Progress] Membuka keran transfer biner...");

      // Tembak API dan sisipkan sensor pelacak progress!
      const respon = await api.post('/relawan/upload-ktp', form, {
        headers: { 'Content-Type': 'multipart/form-data' },
        
        // 1. SENSOR DETEKTOR AXIOS EVENT
        onUploadProgress: (progressEvent) => {
          // Ambil total byte (ukuran file asli) dan hitung berapa byte yang sukses dikirim
          const totalKeseluruhan = progressEvent.total || 1;
          const yangTerkirim = progressEvent.loaded;
          
          // Kalkulasi matematika persen
          const nilaiPersen = Math.round((yangTerkirim * 100) / totalKeseluruhan);
          
          // 2. UPDATE STATE VISUAL (Memicu layar merender ulang warna bar!)
          setPersentaseUpload(nilaiPersen);
        }
      });

      if (respon.status === 201 || respon.status === 200) {
        Alert.alert('✓ Berhasil', 'Pengiriman rampung 100%.');
      }

    } catch (e: any) {
      Alert.alert('Gagal', 'Sinyal terputus di tengah jalan.');
    } finally {
      setIsUploading(false);
      // Reset bar kembali jadi nol setelah satu detik (Opsional visual polish)
      setTimeout(() => setPersentaseUpload(0), 1000); 
    }
  };

  return (
    <View className="flex-1 bg-white justify-center items-center p-6 space-y-6">
      <Text className="font-extrabold text-slate-800 text-sm">Kirim Dokumen Besar</Text>

      {/* 
        3. ELEMEN KOTAK PROGRESS BAR DINAMIS
        Hanya tampil saat proses isUploading = true
      */}
      {isUploading && (
        <View className="w-full space-y-2">
          {/* Label Persentase Teks */}
          <View className="flex-row justify-between w-full px-1">
            <Text className="text-[10px] font-bold text-slate-500 uppercase tracking-widest">Koneksi Berjalan</Text>
            <Text className="text-[10px] font-black text-red-500">{persentaseUpload}%</Text>
          </View>
          
          {/* Batang Kosong Trek Latar Belakang */}
          <View className="w-full h-4 bg-slate-100 rounded-full overflow-hidden">
            {/* Batang Isi Berwarna Merah Yang Mengisi Berdasarkan Width Persen! */}
            <View 
              className="h-full bg-red-500 rounded-full" 
              style={{ width: `${persentaseUpload}%` }} // CSS Dinamis merentangkan lebar div!
            />
          </View>
        </View>
      )}

      {/* Tombol Pemantik */}
      <TouchableOpacity 
        onPress={jalankanUploadBesar}
        disabled={isUploading}
        className="w-full max-w-xs bg-slate-900 py-3.5 rounded-2xl items-center disabled:bg-slate-300"
      >
        <Text className="text-white font-bold text-xs uppercase">
          {isUploading ? 'Menransfer...' : 'Mulai Proses Upload'}
        </Text>
      </TouchableOpacity>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari pemanggilan rumus logika hitungan `Math.round((yangTerkirim * 100) / totalKeseluruhan)` di dalam fungsi callback `onUploadProgress` di atas?
2. Buka kode visual progress bar bagian atribut properti rendering `style={{ width: \`\${persentaseUpload}%\` }}`. Bagaimana kode pendek ini memberikan ilusi optik warna batang bar yang perlahan-lahan merentang mengisi selongsong kontainer layaknya air mengisi pipa?
3. Sebutkan properti bawaan `progressEvent` Axios yang mencatat informasi mengenai *"Berapa banyak jumlah memori bytes yang sudah sukses berpindah server"*?

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Konversi Biner ke Angka Persen
*   **Perhitungan Proporsi**: Objek `progressEvent` tidak mengembalikan angka "50%". Ia merespon dengan pecahan mesin seperti *"150000 bytes terkirim dari total 300000 bytes"*. 
*   Kita harus membagi pecahan angka tersebut dan mengalikannya seratus, lalu dibulatkan membuang koma desimal menggunakan fungsi `Math.round()` agar nilai reaktif yang dikembalikan murni serangkaian bilangan bulat cantik dari nilai `0` hingga max `100`.

### Jawaban 2: Pengendalian Dimensi Dinamis Inline CSS
*   **Render Reaktif Lebar Area**: Div background merah PMI kita tidak memiliki lebar baku. Saat state bernilai `10`, React menggambar properti CSS menjadi `width: 10%` dari kontainer asalnya. Sesaat setelah nilai naik, div digambar ulang sedikit lebih memanjang, terus begitu hingga mencapai ujung `100%`.

### Jawaban 3: Properti loaded
*   Properti objek tersebut bernama **`progressEvent.loaded`**.
```
[FASE 4: HARI 64 SUKSES]
```
