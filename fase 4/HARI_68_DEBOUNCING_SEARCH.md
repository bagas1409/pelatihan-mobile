# Hari 68: Debouncing Input Pencarian
## Meredam Lonjakan Panggilan Request API Berlebih Saat Petugas Mengetik Filter Nama Relawan Menggunakan Fitur setTimeout

Selamat datang di Hari 68! Hari ini kita akan mempelajari teknik stabilisasi performa input pencarian yang sangat vital untuk mengamankan resource server backend Anda: **Debouncing**.

Di formulir kolom pencarian nama relawan PMI (Search Box), jika setiap petugas mengetik satu huruf HP langsung menembak request `API.get('/relawan?q=a')`, `API.get('/relawan?q=ab')`, `API.get('/relawan?q=abd')`... Ribuan panggilan API sampah (*Spam Request*) akan menghujani server backend dalam hitungan detik untuk pencarian kata yang belum selesai diketik! Kita harus meredamnya.

---

## 1. Apa Itu Debouncing?

**Debouncing** adalah teknik pemrograman penunda sinyal waktu (*Timer Delay*).
Prinsipnya: *"Jangan langsung tembak API. Tunggu dulu sampai pengguna berhenti mengetik selama 500 milidetik (setengah detik). Jika dalam 500ms pengguna mengetik huruf baru lagi, batalkan timer hitungan lama dan reset timer kembali ke 0. Jika sudah sepi tidak ada yang diketik, barulah tembakkan kata terakhir itu ke API."*

---

## 2. Studi Kasus PMI: Fitur Pencarian Relawan Anti-Spam (Gunakan `useEffect` + `setTimeout`)

Mari kita sisipkan teknik debouncer ini ke dalam input kolom cari relawan kita:

```tsx
// components/KolomPencarianDebounce.tsx
import React, { useState, useEffect } from 'react';
import { View, Text, TextInput, ActivityIndicator } from 'react-native';
import api from '../utils/api';

export default function KolomPencarianDebounce() {
  const [kataKunci, setKataKunci] = useState('');
  const [hasilPencarian, setHasilPencarian] = useState<string[]>([]);
  const [isSearching, setIsSearching] = useState(false);

  // LOGIKA DEBOUNCER BERSARANG DI DALAM USE-EFFECT
  useEffect(() => {
    // 1. Jangan lakukan apa-apa jika input masih kosong murni
    if (kataKunci.trim() === '') {
      setHasilPencarian([]);
      return;
    }

    setIsSearching(true);

    // 2. PASANG TIMER PENUNDAAN (500 milidetik)
    const penundaWaktuId = setTimeout(async () => {
      console.log(`[Debounce] Petugas berhenti mengetik. Menembak API pencarian: "${kataKunci}"`);
      
      try {
        // Tembakkan API GET
        // Simulasi hit API:
        // const response = await api.get(`/relawan/cari?q=${kataKunci}`);
        
        // Simulasi hasil palsu untuk latihan
        setHasilPencarian([`Relawan ${kataKunci} Akbar`, `Bapak ${kataKunci} Setiawan`]);
      } catch (error) {
        console.error("Gagal mencari data", error);
      } finally {
        setIsSearching(false);
      }
    }, 500); // Tahan nafas selama setengah detik!

    // 3. FUNGSI CLEANUP: PEMBUNUH TIMER LAMA (VITAL!)
    // Ini berjalan jika useEffect terpanggil lagi SEBELUM 500ms berakhir (karena user mengetik huruf baru)
    return () => {
      console.log(`[Debounce] Huruf baru ditekan! Membatalkan pencarian kata lama...`);
      clearTimeout(penundaWaktuId); // Hancurkan timer lama agar request batal!
    };
    
  }, [kataKunci]); // Menjalankan useEffect otomatis SETIAP KALI variabel kataKunci berubah!

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4">
      <Text className="font-extrabold text-slate-800 text-sm">🔍 Filter Anggota Cepat</Text>
      
      {/* Kolom Input Search */}
      <View className="flex-row items-center border border-slate-200 rounded-2xl bg-slate-50 px-4">
        <Text className="text-slate-400 mr-2">🔎</Text>
        <TextInput
          value={kataKunci}
          onChangeText={setKataKunci}
          placeholder="Ketik nama (min 3 huruf)..."
          className="flex-1 py-3 text-xs"
        />
        {isSearching && <ActivityIndicator size="small" color="#ef4444" />}
      </View>

      {/* Area Hasil */}
      <View className="bg-slate-50 p-4 rounded-2xl border border-slate-100 min-h-[80px]">
        {hasilPencarian.length > 0 ? (
          hasilPencarian.map((nama, idx) => (
            <Text key={idx} className="text-xs font-bold text-slate-700 py-1">👤 {nama}</Text>
          ))
        ) : (
          <Text className="text-[10px] text-slate-400 text-center mt-2">Belum ada hasil pencarian.</Text>
        )}
      </View>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi perlindungan server database yang dihasilkan dengan cara memanggil modul waktu tunda callback penahan `setTimeout(..., 500)` di atas?
2. Buka kode blok pengembalian sisa pembersih memori: `return () => clearTimeout(...)`. Jelaskan efek buruk memori HP bila fungsi pembunuh antrean timer ini lupa ditulis.
3. Sebutkan kelebihan komponen Debouncing ini bagi efisiensi data kuota internet seluler petugas PMI.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Filter Spamming Perlindungan DDOS Internal
*   **Pemecah Gelombang Tembakan API**: Jika petugas mengetik nama "ABDUL" secara cepat, state string `kataKunci` berubah 5 kali beruntun (*A*, *AB*, *ABD*, *ABDU*, *ABDUL*). 
*   Jika tak diredam, server PMI akan dibombardir 5 tembakan API GET ke database. Fitur Debounce menelan 4 tembakan pertama yang diketik, dan API menembak 1 kali eksekusi final ketika petugas tuntas merampungkan ketikan "ABDUL". Menyelamatkan kinerja server Express.js secara eksponensial.

### Jawaban 2: Gagalnya Fitur Pembatalan (Race Condition Bug)
*   **Tabrakan Data Silang**: Fungsi `clearTimeout` bertugas sebagai pembunuh (*Terminator*) yang mencegah sisa eksekusi jadwal API lama terlempar ke internet. 
*   Jika baris `clearTimeout` ini luput/hilang, debouncing Anda sama sekali tidak akan berfungsi! 5 antrean `setTimeout` 500ms akan tetap berjalan berbarengan secara independen dan meledak memuntahkan 5 request API dalam jeda waktu yang bersamaan.

### Jawaban 3: Penurunan Beban Pemakaian Paket Data (Bandwidth Saver)
*   **Hemat Data Paket Internet Seluler**: Tanpa Debounce, setiap ketikan huruf akan mendownload payload JSON hasil pencarian yang belum selesai (Spam Data). Menunggu tulisan lengkap menekan total konsumsi memori internet download aplikasi menjadi berkali lipat lebih ringan, membantu kinerja sinyal HP di pelosok Lampung.
```
[FASE 4: HARI 68 SUKSES]
```
