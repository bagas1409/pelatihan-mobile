# Hari 74: Sanitasi Keamanan Data Input Lanjut
## Memangkas Simbol Karakter Berbahaya Injeksi XSS Dari Kolom Pencarian Relawan Menggunakan Teknik Pembersihan String Teks Polos Native

Selamat datang di Hari 74! Hari ini kita akan mempelajari topik krusial bagi kebersihan sistem keamanan aplikasi Anda: **Pembersihan String (Input Sanitization)**.

Di Fase 4 Web (Hari 71) kita telah mempelajari tentang `DOMPurify` untuk membersihkan tag script peretas di lingkungan perenderan dokumen HTML Web. Di dunia aplikasi platform Native Mobile, komponen `<Text>` tidak mengeksekusi merender secara buta ancaman script `alert('hacked')` menjadi program aktif. Namun, kita tetap harus mengawal, mensterilkan membuang simbol-simbol huruf aneh karakter kutip SQL dari ketikan liar bebas yang petugas ketikkan di kolom input, demi mencegah mesin server backend SQL PMI kita mendadak terbakar crash error karena kemasukan simbol ilegal mematikan.

---

## 1. Bahaya Injeksi Celah Ketikan Bebas

*   Bila di formulir nama petugas, seorang usil mengetikkan: `Budi O'Connor OR 1=1;--`
*   Jika string kotor ini langsung ditembakkan lurus ke rute API `API.post('/relawan')`, dan programmer backend PMI Anda ternyata pemula yang lalai menggunakan pelindung SQL Prepared Statements, string `';--` ini akan dieksekusi menjadi instruksi kode SQL perusak database sungguhan!
*   **Solusi Front-End**: Kita saring pangkas buang karat simbol kutip haram mematikan tersebut persis dari dalam aplikasi Mobile HP ini sebelum ia berangkat terbang dikirimkan ke cloud internet.

---

## 2. Studi Kasus PMI: Mesin Penyaring Debu Simbol Input (Sanitizer)

Mari kita merakit satu modul fungsi kecil tukang jagal (*Stripper Filter*) yang dipasangkan rekat bereaksi dengan `<TextInput>` menggunakan manipulasi string Javascript murni bawaan RegExp `replace()`.

```tsx
// components/KolomKunciAman.tsx
import React, { useState } from 'react';
import { View, Text, TextInput, TouchableOpacity, Alert } from 'react-native';

export default function KolomKunciAman() {
  const [catatanRawan, setCatatanRawan] = useState('');

  // 1. FUNGSI FILTER JAWARA PEMBERSIH KOTORAN SIMBOL
  const fungsiGergajiFilterSimbol = (teksBebasMasuk: string) => {
    // Mesin regex ini HANYA MENGIZINKAN huruf Alfabet A-Z, Angka 0-9, Titik, dan Spasi saja!
    // Apapun yang berupa kutip satu ('), kutip dua ("), kurung tutup (>), atau titik koma (;)
    // AKAN LANGSUNG DITEBAS HILANG MENJADI KOSONG ('')
    const teksBersihSteril = teksBebasMasuk.replace(/[^a-zA-Z0-9\s.]/g, '');
    
    setCatatanRawan(teksBersihSteril); // Ganti tulisan kotor dengan teks steril yang baru disaring!
  };

  const kirimLaporanMurni = () => {
    Alert.alert('Dikirim Aman', `Catatan log ke server: "${catatanRawan}"`);
    console.log("[Sanitasi Aman] Payload String dikirim:", catatanRawan);
  };

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4">
      <Text className="font-extrabold text-slate-800 text-sm">🔒 Catatan Transaksi (Steril)</Text>

      <View className="space-y-1.5">
        <Text className="text-[10px] font-bold text-slate-400">DETAIL LAPORAN KHUSUS</Text>
        
        {/* Kolom TextInput Rawan Ancaman */}
        <TextInput
          value={catatanRawan}
          // Setiap ketikan satu huruf masuk, lemparkan ke kawah mesin filter Regex di atas
          onChangeText={fungsiGergajiFilterSimbol}
          
          placeholder="Dilarang keras memakai simbol kutip!"
          placeholderTextColor="#94a3b8"
          className="w-full px-4 py-3 rounded-2xl bg-slate-50 border border-slate-200 text-xs text-slate-800"
        />
        
        <Text className="text-[9px] text-emerald-500 font-bold mt-1">
          ✓ Pengaman Aktif. Input Anda otomatis disterilkan saat mengetik.
        </Text>
      </View>

      <TouchableOpacity 
        onPress={kirimLaporanMurni}
        className="w-full bg-slate-900 py-3.5 rounded-2xl items-center mt-2"
      >
        <Text className="text-white font-bold text-xs uppercase tracking-widest">Kirim Laporan</Text>
      </TouchableOpacity>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Mengapa platform React Native mobile dianggap aman secara natural terhadap insiden mematikan suntikan script *Cross Site Scripting (XSS)* jika kita menjabarkan nilai variabel curian berbahaya tersebut lurus polos jatuh menabrak tertampang masuk ke dalam isi properti badan komponen `<Text>{teksKotorXSS}</Text>`?
2. Buka baris bedah pelindung alat bedah filter `replace(/[^a-zA-Z0-9\s.]/g, '')`. Jelaskan maksud kegunaan dari rangkaian tanda panah topi terbalik sisipan simbol penanda `[^...]` pada penentu aturan mesin pendeteksi Regex Javascript pemotong karakter tersebut di atas!
3. Apa kelebihan drastis perancangan implementasi sistem peredam bahaya sanitasi kotoran simbol secara proaktif di layer aplikasi Mobile HP sisi *Front-End* dibandingkan sekadar menunggu membuangnya telat menyerahkan tugas peredam penjagal filter tersebut menumpuk di sisi *Back-End* server awan Internet?

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Bukan Parser Kompiler DOM HTML Murni
*   **Perisai Lingkungan Native Engine**: Peramban Browser Web merender `<script>` dan tag html rawan lainnya karena ia menyusun membacanya dari DOM Parser compiler mesin bahasa string secara mentah tekstual HTML hidup. 
*   Mesin kompilator render platform React Native tidak mengerti dan sangat buta buta abjad terhadap elemen DOM Web! String nilai tulisan `"<script>alert()</script>"` akan ditangani digambar secara bisu lugu polos menjadi teks tulisan huruf visual kaku benda mati belaka tanpa berani mengeksekusinya menjadi ancaman kode injeksi merusak!

### Jawaban 2: Logika Regex Pembalik Arah Kutub (Negated Set)
*   **Pemotongan Karakter Negatif**: Sisipan simbol topi payung awalan pangkat terbalik `^` ditaruh awal dalam susunan rel blok kurung `[...]` memiliki arti kuat pembalikan makna kalimat "*KECUALI / BUKAN*". 
*   RegExp secara harfiah akan menyapu mencari: **"Pangkas hilang musnahkan semua karakter APAPUN ITU di kalimat ini, KECUALI JIKA dia termasuk rombongan kelompok grup huruf kecil a-z, besar A-Z, angka 0-9, dan spasi kosong"**. Karakter koma, kutip dua, kutip tunggal, kurung siku otomatis ikut ditelan hilang oleh fungsi filter pembuangan array string tersebut.

### Jawaban 3: Mencegah Kelelahan Mesin Server Beban Filter Ringan (Offloading Firewall Backend)
*   **Keringanan Penolakan Lalu Lintas Payload Sampah Hit**: Bila Anda memaksa membiarkan string kutip kotor sampah membanjiri payload terkirim terbang menembus awan mendarat hingga server internet, Mesin RAM Server CPU Backend harus terpaksa bekerja keras panas lembur ekstra menjalankan tugas mesin Regex membuang jutaan sampah huruf miliaran data dari seluruh HP pengguna relawan. 
*   Mengerjakan filter ringan ini disebarkan pecah dilimpahkan dipisah langsung disaring potong di sisi chip processor masing-masing individu ratusan aplikasi HP relawan sang penginput, murni berhasil mereduksi drastis menghemat beban kerja komputasi tagihan pengeluaran listrik komputasi Cloud API Server Server backend PMI di awan!
```
[FASE 4: HARI 74 SUKSES]
```
