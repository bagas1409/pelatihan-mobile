# Hari 72: Sistem File Lokal & Fitur Bagikan (expo-file-system & expo-sharing)
## Mengunduh Arsip Laporan PDF Transaksi CITO Dan Membagikannya Ke WhatsApp Melalui Fitur Bawaan Share HP

Selamat datang di Hari 72! Di aplikasi mobile, terkadang petugas lapangan perlu menyimpan bukti surat jalan atau laporan transaksi donor harian dalam format dokumen statis (misalnya gambar Kuitansi / file PDF) yang bisa diakses kapanpun saat sedang santai tanpa sinyal internet, dan di-forward ke aplikasi obrolan (WhatsApp/Telegram).

Kita akan memanfaatkan modul **`expo-file-system`** untuk mengunduh dan meraba map disk keras penyimpanan lokal HP, lalu menggunakan **`expo-sharing`** untuk melempar file tersebut keluar ke aplikasi tetangga.

---

## 1. Pemasangan Dua Pustaka Kembar Aksi File

Jalankan perintah penginstalan ini di terminal VS Code Anda:
```bash
npx expo install expo-file-system expo-sharing
```

---

## 2. Studi Kasus PMI: Mengunduh Bukti Surat Jalan PDF

Mari kita buat sebuah komponen kartu laci dokumen untuk transaksi distribusi kantong darah CITO. Jika tombol ditekan, aplikasi akan mengunduh dari server, menyimpannya ke memori, lalu membagikannya:

```tsx
// components/KartuUnduhSuratJalan.tsx
import React, { useState } from 'react';
import { View, Text, TouchableOpacity, ActivityIndicator, Alert } from 'react-native';
import * as FileSystem from 'expo-file-system';
import * as Sharing from 'expo-sharing';

export default function KartuUnduhSuratJalan() {
  const [sedangUnduh, setSedangUnduh] = useState(false);

  // Fungsi pengunduh dan pelempar Share
  const aksiUnduhDanBagikan = async () => {
    setSedangUnduh(true);
    try {
      // Dummy URL PDF Statis (Bisa diganti endpoint backend PDF Anda)
      const URL_PDF_SERVER = 'https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf';
      
      // 1. BUAT JALUR ALAMAT FILE LOKAL TUJUAN (Di laci dokumen internal aplikasi)
      // FileSystem.documentDirectory adalah letak laci brankas tertutup aman khusus aplikasi ini
      const NAMA_FILE = 'Surat_Jalan_CITO_001.pdf';
      const fileLokalUri = FileSystem.documentDirectory + NAMA_FILE;

      console.log("[FileSystem] Memulai proses penyedotan file dari server internet...");

      // 2. SEDOT FILE MENGGUNAKAN METODE downloadAsync
      const hasilUnduhan = await FileSystem.downloadAsync(
        URL_PDF_SERVER,
        fileLokalUri
      );

      console.log("[FileSystem] File terunduh utuh di folder:", hasilUnduhan.uri);

      // 3. LEMPAR FILE KE APLIKASI LAIN (WHATSAPP, GMAIL, DLL)
      // Cek dulu apakah HP ini mendukung fitur Share file biner?
      const bisaDiShare = await Sharing.isAvailableAsync();
      
      if (bisaDiShare) {
        await Sharing.shareAsync(hasilUnduhan.uri, {
          dialogTitle: 'Bagikan Bukti Surat Jalan CITO', // Judul khusus menu Share Android
          mimeType: 'application/pdf',
        });
      } else {
        Alert.alert('Gagal Membagikan', 'Perangkat ini tidak mendukung fitur Sharing file.');
      }

    } catch (e) {
      console.error("[FileSystem Error]:", e);
      Alert.alert('Unduh Gagal', 'Sinyal koneksi putus atau memori HP kepenuhan.');
    } finally {
      setSedangUnduh(false);
    }
  };

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4 flex-row items-center justify-between">
      <View className="space-y-1">
        <Text className="font-extrabold text-slate-800 text-sm">Surat Jalan #C-001</Text>
        <Text className="text-[10px] text-slate-400">Arsip Pengiriman Gol. O Positif</Text>
      </View>

      <TouchableOpacity 
        onPress={aksiUnduhDanBagikan}
        disabled={sedangUnduh}
        className="bg-red-50 p-4 rounded-full border border-red-100"
      >
        {sedangUnduh ? (
          <ActivityIndicator size="small" color="#ef4444" />
        ) : (
          <Text className="text-xl">⬇️</Text>
        )}
      </TouchableOpacity>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi kegunaan spesifik dari konstanta akar bawaan `FileSystem.documentDirectory` pada modul expo di atas? Bisakah letak laci ini diakses dengan mudah oleh aplikasi lain?
2. Buka kode blok pengunduhan asinkron parameter kedua fungsi metode `FileSystem.downloadAsync(...)`. Mengapa kita wajib menyerahkan jalur titik kumpul `fileLokalUri` yang berisi susunan gabungan panjang nama string di sana?
3. Sebutkan kelebihan pemanggilan perantara fungsi perisai `Sharing.isAvailableAsync()` secara langsung tepat di awal baris sebelum kita iseng melempar paksa eksekusi `.shareAsync`!

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Laci Brankas Sandbox Sistem Tertutup
*   **Privasi Ketat Dokumen**: String URL alamat path `documentDirectory` mengacu langsung pada rute partisi rahasia isolasi memori harddisk HP. 
*   Secara default *Sandbox OS*, file yang jatuh mendarat ditaruh di dalam laci aplikasi ini **HANYA bisa dibaca oleh aplikasi DonorKu ini saja**! File sensitif data rekam darah relawan aman tidak akan bisa dilihat bocor oleh file manager galeri publik atau diculik dicuri dibaca oleh virus aplikasi pencari celah (*Malware*).

### Jawaban 2: Identitas Pendaratan Target Unduhan Kargo Biner (Destinasi File)
*   **Memberikan Titik Pendaratan**: Metode `downloadAsync` bertugas menarik gerbong arus jutaan byte biner pecahan dari satelit internet awan server, dia bingung harus ditumpahkan kemana arus data tersebut. 
*   Penyuplai variabel penggabung alamat direktori root disatukan bersama nama akhiran ekstensi filenya (`'Surat_Jalan_CITO_001.pdf'`) menjadi landasan peta agar si mesin SDK berani menulis mematri cetak merakit ulang arus pecahan byte tersebut menjadi lembaran satu wujud utuh file arsip PDF mandiri mendarat di laci disk ruang memori HP.

### Jawaban 3: Penyelamat Fatal Error Tiba-Tiba Aplikasi Crash Lempar
*   **Sensor Toleransi Lintas OS Device**: Tidak semua device hardware sistem mesin mendukung fitur dialog pop-up menu "Share Ke...". Emulator Apple TV / Smart TV / HP Android edisi usang seringkali ketiadaan paket UI Native Share. 
*   Apabila perisai metode `isAvailableAsync()` tidak dipasang mendahului, eksekusi pemaksaan pelemparan pemanggilan langsung ke layar `shareAsync` akan ditanggapi OS dengan pelemparan crash lempar paksa force-close layar mati. Penambahan pengaman if-else menangkap damai pengecualian tersebut lewat dialog pop-up teguran santai (*Graceful Degradation*).
```
[FASE 4: HARI 72 SUKSES]
```
