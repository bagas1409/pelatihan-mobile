# Hari 69: Caching Respon API Lokal (Offline First)
## Menyimpan Sementara Data Hasil Response Server API Menggunakan AsyncStorage Untuk Percepatan Tampilan Saat Internet Lelet

Selamat datang di Hari 69! Hari ini kita akan merajut integrasi tingkat lanjut pemanfaatan memori `AsyncStorage` yang kita pelajari di Hari 54: **Data Caching API (Tembolok Data)**.

Aplikasi PMI Lampung yang sering dibawa kurir ke pedesaan pelosok (Blank Spot Signal) membutuhkan kemampuan mode luring statis (*Offline First*). Jika kurir membuka halaman Daftar Harga Komponen Darah, namun HP sedang kehilangan sinyal, aplikasi tidak boleh melempar layar blank putih atau crash. Kita harus memanggil memori penyimpanan sementara (Cache Storage) dari hasil download kunjungan sebelumnya.

---

## 1. Arsitektur Cache-First Network Logis

Konsep algoritma "Tembolok Menyelamatkan" (Stale-While-Revalidate pattern):
1.  **Langkah 1**: Saat HP membuka layar List Darah, cek `AsyncStorage` HP lokal. Jika ada rekam jejak array data darah kemarin, langsung lemparkan tampilkan ke layar dalam 0 detik! (Pengguna merasa UI super kilat).
2.  **Langkah 2**: Diam-diam HP tetap mencoba menembak Axios HTTP Request ke server API sungguhan di latar belakang.
3.  **Langkah 3**: Jika API sukses membalas membawa update angka stok terbaru, tindih (*Overwrite*) gambar data di layar UI, dan catat update terbaru tersebut menimpa cache AsyncStorage untuk ditabung esok hari.

---

## 2. Studi Kasus PMI: Tembolok Daftar Komponen Darah (`hooks/useApiCache.ts`)

Mari kembangkan custom hook kita sebelumnya menjadi **`useApiCache`** di direktori hooks:

```typescript
// hooks/useApiCache.ts
import { useState, useEffect, useCallback } from 'react';
import AsyncStorage from '@react-native-async-storage/async-storage';
import api from '../utils/api';

export function useApiCache<T>(pathUrl: string, cacheKey: string) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState<boolean>(true);

  const unduhDataDenganCache = useCallback(async () => {
    // 1. [MODE KILAT] CEK DATA TABUNGAN OFFLINE DULU!
    try {
      const dataLokalMentah = await AsyncStorage.getItem(cacheKey);
      if (dataLokalMentah) {
        // Terjemahkan string JSON kembali ke format Objek Javascript
        setData(JSON.parse(dataLokalMentah) as T);
        setLoading(false); // Matikan loading secara super kilat karena data sudah muncul!
        console.log(`[Cache] Memuat tabungan data offline dari kunci: ${cacheKey}`);
      }
    } catch (e) { console.error("Error baca cache:", e); }

    // 2. [MODE INTERNET] TETAP TARIK UPDATE API DIAM-DIAM DIBELAKANG
    try {
      console.log(`[Cache] Memvalidasi update API rute ${pathUrl} ke server...`);
      const respon = await api.get<T>(pathUrl);
      
      const dataBaru = respon.data;
      
      // Update UI Layar
      setData(dataBaru);
      
      // Update Tabungan File Offline AsyncStorage! (Wajib Stringify JSON)
      await AsyncStorage.setItem(cacheKey, JSON.stringify(dataBaru));
      console.log(`[Cache] Sukses Update. Data tabungan ditindih versi terbaru.`);
      
    } catch (error) {
      console.error("[Cache Network Error]: Menggunakan data lokal jika ada.");
    } finally {
      setLoading(false);
    }
  }, [pathUrl, cacheKey]);

  useEffect(() => {
    unduhDataDenganCache();
  }, [unduhDataDenganCache]);

  return { data, loading, segarkanManual: unduhDataDenganCache };
}
```

Cara memakainya di layar Komponen: `const { data } = useApiCache('/komponen-darah', 'CACHE_DARAH_LIST');`

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari parameter argumen `cacheKey` (contoh valuenya `'CACHE_DARAH_LIST'`) yang wajib dilempar saat inisialisasi custom hooks `useApiCache` di atas?
2. Buka baris blok simpan memori `AsyncStorage.setItem`. Jelaskan keharusan membungkus objek array ke fungsi `JSON.stringify(dataBaru)` sebelum ditabungkan ke memori fisik HP.
3. Sebutkan pengalaman pengguna (*User Experience*) terbaik yang dirasakan oleh petugas akibat pembacaan file tembolok data offline terlebih dahulu sebelum sistem menarik API secara utuh dari server internet.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Identitas Brankas Memori
*   **Pembeda Tabungan Database**: Modul `AsyncStorage` menyimpan ratusan memori data di satu file database lokal yang sama (Key-Value map). 
*   Menetapkan `cacheKey` seperti `'CACHE_DARAH_LIST'` bertindak sebagai nama unik stiker identitas label folder laci penyimpanan khusus untuk data layar tersebut agar isiannya tidak tumpang tindih bocor ditabrak data array cache milik menu Profil Relawan (`'CACHE_PROFIL_USER'`).

### Jawaban 2: Proses Serialisasi Biner Text (Stringify Payload)
*   **Perbatasan Data Objek vs Text**: Modul disk file native HP Android/iOS (AsyncStorage) sangat primitif, modul ini secara fisik HANYA bisa menyimpan data untaian string karakter huruf polos (*Plain text strings*). 
*   Komponen tersebut akan error membisu saat disuruh merekam struktur array kompleks. Fungsi `JSON.stringify` bertugas merubah wujud sihir objek kurung kurawal tersebut menjadi rentengan urutan kalimat huruf rapi agar bisa disalin masuk secara aman ke memori C++ native disk device.

### Jawaban 3: Sensasi Zero Loading (Kecepatan Waktu Tampil Layar Instan)
*   **Percepatan Render UI Layar**: Layar akan terasa sangat gesit mulus terbuka dalam waktu kilat 0,05 detik di HP karena langsung menggambar memori list teks lokal, memberikan sensasi seolah-olah list darah tersebut sudah tersimpan keras statis di aplikasi. Serta menyelamatkan muka error layar kosong nge-blank (*White screen bug*) jika tiba-tiba kurir masuk zona seluler pegunungan yang tidak ada jaringan sama sekali.
```
[FASE 4: HARI 69 SUKSES]
```
