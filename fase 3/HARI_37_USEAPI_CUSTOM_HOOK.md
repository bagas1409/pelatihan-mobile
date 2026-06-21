# Hari 37: Custom Fetcher Hook `useApi`
## Merancang Custom Hook useApi Untuk Meminimalisir Penulisan State Loading & Error Berulang Di Setiap Layar

Selamat datang di Hari 37! Hari ini kita akan merancang sebuah custom hook pembantu bernama **`useApi`**.

Setiap kali kita memanggil data dari API backend di aplikasi mobile, kita selalu terpaksa menuliskan minimal tiga buah state lokal secara berulang-ulang: state `data` (menyimpan hasil), state `loading` (boolean penanda loading), dan state `error` (menyimpan pesan kesalahan). Menulis ketiga state ini di 30 file halaman yang berbeda akan menghasilkan duplikasi kode yang sangat masif. Hari ini kita akan menyatukan logika tersebut ke dalam satu custom hook cerdas.

---

## 1. Konsep Sentralisasi State API Fetcher

Custom hook `useApi` yang akan kita buat bertugas:
1.  Menyediakan state `data`, `loading`, dan `error` secara terpadu.
2.  Memicu pemanggilan fungsi Axios secara asinkron di dalam blok `useEffect`.
3.  Menyediakan fungsi refresh manual (`kocokUlang`) yang bisa dipicu dari tombol refresh atau gesture pull-to-refresh.

---

## 2. Studi Kasus PMI: Kode Custom Hook `hooks/useApi.ts`

Buat file baru di direktori **`hooks/useApi.ts`**:

```typescript
// hooks/useApi.ts
import { useState, useEffect, useCallback } from 'react';
import api from '../utils/api';

export function useApi<T>(pathUrl: string) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState<boolean>(true);
  const [error, setError] = useState<string | null>(null);

  // Fungsi pengunduh data asinkron
  const unduhData = useCallback(async () => {
    setLoading(true);
    setError(null);
    try {
      // Tembak API menggunakan instance Axios terpusat (Hari 36)
      const respon = await api.get<T>(pathUrl);
      setData(respon.data);
    } catch (err: any) {
      console.error("[useApi Error]:", err);
      setError(err.response?.data?.message || err.message || 'Gagal tersambung ke server.');
    } finally {
      setLoading(false);
    }
  }, [pathUrl]);

  // Picu download pertama kali saat komponen mounted
  useEffect(() => {
    unduhData();
  }, [unduhData]);

  // Kembalikan state reaktif beserta fungsi refresh manual 'kocokUlang'
  return { data, loading, error, kocokUlang: unduhData };
}
```

---

## 3. Latihan Soal Mandiri
1. Amati parameter `<T>` pada baris deklarasi fungsi `useApi<T>` di atas.
2. Jelaskan fungsi dari parameter generik `<T>` tersebut bagi ketepatan identifikasi tipe data (*Type Safety*) di lingkungan TypeScript.
3. Sebutkan nama properti hasil kembalian (*Return value*) dari custom hook `useApi` di atas yang bisa kita panggil untuk memicu pengambilan data ulang dari server ketika terjadi error koneksi.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Keunggulan TypeScript Generics <T>
*   **Tipe Data Dinamis (Generics)**: Huruf `<T>` adalah simbol tipe data generik di TypeScript. 
*   Dia memungkinkan komponen pemanggil untuk menentukan tipe objek apa yang akan dikembalikan oleh API (misalnya: jika memanggil rute `/stok`, kita bisa menyetel `useApi<StokDarah[]>`, sedangkan jika memanggil `/profil` kita menyetel `useApi<Petugas>`). 
*   Ini memastikan editor VS Code bisa memunculkan auto-complete untuk nama properti objek hasil download secara aman tanpa melempar tipe data `any` yang rawan bug salah tulis.

### Jawaban 3: Fungsi kocokUlang (Refresh Trigger)
*   Properti tersebut bernama **`kocokUlang`** yang merujuk pada fungsi callback `unduhData`.
```
[FASE 3: HARI 37 SUKSES]
```
