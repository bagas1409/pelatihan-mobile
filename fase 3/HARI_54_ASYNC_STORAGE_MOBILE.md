# Hari 54: Menyimpan Data Non-Sensitif (AsyncStorage)
## Memanfaatkan AsyncStorage Untuk Menyimpan Prefrensi Pencarian Golongan Darah Petugas Secara Lokal

Selamat datang di Hari 54! Hari ini kita akan mempelajari cara menyimpan data lokal non-sensitif menggunakan **`AsyncStorage`**.

Di Hari 43 kita belajar mengunci token JWT secara aman di KeyStore terenkripsi hardware HP menggunakan `SecureStore`. Namun, tidak semua data di aplikasi bersifat rahasia (sensitif). Contoh data non-sensitif adalah: preferensi filter pencarian golongan darah petugas (A, B, O, AB), setelan ukuran font dashboard, atau status apakah user sudah pernah melihat petunjuk pengoperasian awal (*First time tutorial onboarding*). 

Menyimpan data non-sensitif ini di `SecureStore` akan membebani chip enkripsi HP secara tidak perlu. Solusi yang tepat adalah menggunakan **`AsyncStorage`**.

---

## 1. Apa itu AsyncStorage?

*   **Penyimpanan Teks Sederhana**: `AsyncStorage` adalah database bertipe kunci-nilai (*Key-Value Pair*) asinkron yang tidak terenkripsi, sangat mirip dengan `localStorage` pada browser web.
*   Data disimpan dalam format teks polos di harddisk internal HP, menjadikannya sangat cepat dibaca dan ditulis untuk berkas berukuran besar sekalipun.

---

## 2. Instalasi Library AsyncStorage

Jalankan perintah penginstalan berikut di terminal proyek Anda:
```bash
npx expo install @react-native-async-storage/async-storage
```

---

## 3. Studi Kasus PMI: Merekam Preferensi Filter Pencarian Petugas

Mari kita buat helper perekam preferensi filter pencarian stok darah di berkas **`utils/preferences.ts`**:

```typescript
// utils/preferences.ts
import AsyncStorage from '@react-native-async-storage/async-storage';

const KEY_FILTER = 'donorku_pref_filter';

export const prefHelper = {
  // 1. SIMPAN FILTER PILIHAN PETUGAS
  simpanFilterTerpilih: async (golongan: string): Promise<void> => {
    try {
      await AsyncStorage.setItem(KEY_FILTER, golongan);
      console.log("[AsyncStorage] Preferensi filter disimpan:", golongan);
    } catch (e) {
      console.error("[AsyncStorage Save Error]:", e);
    }
  },

  // 2. AMBIL FILTER PILIHAN TERAKHIR
  ambilFilterTerakhir: async (): Promise<string | null> => {
    try {
      const value = await AsyncStorage.getItem(KEY_FILTER);
      return value; // Mengembalikan string golongan (misal: "O") atau null jika belum di-set
    } catch (e) {
      console.error("[AsyncStorage Read Error]:", e);
      return null;
    }
  }
};
```

---

## 4. Latihan Soal Mandiri
1. Apa perbedaan utama dari segi kegunaan/peruntukan antara **`SecureStore`** dengan **`AsyncStorage`** di perangkat mobile?
2. Buka kode `prefHelper.simpanFilterTerpilih` di atas. Mengapa data yang disimpan di dalam `AsyncStorage` wajib berupa tipe data `string`? Bagaimana cara kita menyimpan objek JavaScript yang kompleks (seperti JSON profil)?
3. Sebutkan nama perintah di terminal proyek Expo untuk memasang library `AsyncStorage` secara resmi.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Perbedaan Keamanan & Karakteristik Data
*   **`SecureStore`**: Diperuntukkan bagi **Data Rahasia/Sensitif** berukuran mikro (token JWT, password, PIN bank) karena datanya dienkripsi di chip hardware. Kapasitas terbatas (2KB).
*   **`AsyncStorage`**: Diperuntukkan bagi **Data Umum/Non-Sensitif** (preferensi filter, layout dark/light mode, cache artikel berita). Kapasitas penyimpanan besar (tidak dibatasi secara ketat, tergantung sisa ruang harddisk HP), namun datanya disimpan polos tanpa enkripsi.

### Jawaban 2: Konsep Serialisasi JSON (JSON Stringify)
*   **Wajib String**: Mesin `AsyncStorage` hanya menerima isian tipe data string. 
*   **Penyimpanan Objek**: Jika ingin menyimpan objek JSON yang kompleks, kita wajib menggunakan metode **`JSON.stringify(objek)`** untuk mengubahnya menjadi string teks terlebih dahulu sebelum disimpan, dan memanggil **`JSON.parse(string)`** untuk menerjemahkannya kembali menjadi objek JavaScript saat dibaca.

### Jawaban 3: Perintah Instalasi Resmi
*   Perintahnya adalah **`npx expo install @react-native-async-storage/async-storage`**.
```
[FASE 3: HARI 54 SUKSES]
```
