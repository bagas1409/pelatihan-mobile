# Hari 5: Dasar JSX & React Native Core Components
## Memahami Cara Menyusun UI Menggunakan View, Text, Serta Memuat Gambar Melalui Image Component

Selamat datang di Hari 5! Hari ini kita akan mempelajari sintaks **JSX** dan tiga komponen visual utama di React Native: **`View`**, **`Text`**, dan **`Image`**.

Saat merakit tampilan web di Fase 2 s.d 5, kita menggunakan tag HTML seperti `<div>`, `<p>`, dan `<img>`. Di dunia mobile development, tag-tag tersebut tidak dikenal oleh handphone. Kita harus berkenalan dengan pengganti-penggantinya yang bertugas menyuruh HP menggambar elemen native asli.

---

## 1. Tabel Padanan Tag HTML vs React Native Core Components

| Kegunaan Visual | Tag Web HTML | Komponen React Native | Keterangan Fungsi |
| :--- | :--- | :--- | :--- |
| Wadah Pembungkus | `<div>`, `<section>` | **`View`** | Kontainer kotak pembatas layout (menggunakan flexbox secara default). |
| Teks Tulisan | `<p>`, `<span>`, `<h1>` | **`Text`** | Satu-satunya komponen yang diperbolehkan memuat teks tulisan di mobile. |
| Gambar/Visual | `<img>` | **`Image`** | Memuat gambar dari folder lokal maupun URL internet. |

*   *Peringatan Keras*: Di React Native, Anda **tidak boleh menuliskan teks tulisan langsung** di dalam `<View>`. Menulis `<View>Halo Petugas</View>` akan langsung memicu crash error merah di HP Anda. Teks wajib dibungkus di dalam `<Text>`: `<View><Text>Halo Petugas</Text></View>`.

---

## 2. Studi Kasus PMI: Membuat Kartu Informasi Posko Donor

Mari kita buat contoh kode penyusunan layout sederhana menggunakan JSX TypeScript.

```tsx
// components/CardPosko.tsx
import React from 'react';
import { View, Text, Image, StyleSheet } from 'react-native';

export default function CardPosko() {
  const URL_GAMBAR_PMI = 'https://pmi.or.id/wp-content/uploads/2019/10/pmi-logo.png';

  return (
    // View bertindak sebagai pembungkus kartu (Card Container)
    <View style={styles.cardContainer}>
      
      <!-- Memuat Gambar Logo PMI Lampung dari Internet (URI) -->
      <Image 
        source={{ uri: URL_GAMBAR_PMI }} 
        style={styles.logoPmi} 
      />

      <!-- Area Teks Detail Informasi -->
      <View style={styles.textContainer}>
        <Text style={styles.txtJudul}>UDD PMI Kedaton</Text>
        <Text style={styles.txtAlamat}>Jl. Sam Ratulangi No. 105, Bandar Lampung</Text>
      </View>

    </View>
  );
}

// Pengenalan Dasar Styling StyleSheet
const styles = StyleSheet.create({
  cardContainer: {
    flexDirection: 'row', // Sejajarkan gambar dan teks ke samping (Horizontal)
    backgroundColor: '#ffffff',
    padding: 16,
    borderRadius: 20,
    borderWidth: 1,
    borderColor: '#e2e8f0',
    alignItems: 'center'
  },
  logoPmi: {
    width: 50,
    height: 50,
    borderRadius: 25, // Membuat gambar bulat sempurna (lebar / 2)
    marginRight: 12
  },
  textContainer: {
    flex: 1
  },
  txtJudul: {
    fontWeight: 'bold',
    fontSize: 14,
    color: '#1e293b'
  },
  txtAlamat: {
    fontSize: 12,
    color: '#64748b',
    marginTop: 4
  }
});
```

---

## 3. Latihan Soal Mandiri
1. Mengapa penulisan teks langsung di dalam `<View>` (tanpa dibungkus `<Text>`) akan memicu crash error di React Native, sedangkan di HTML web penulisan teks di dalam `<div>` diperbolehkan?
2. Buka dokumentasi properti `<Image>`. Apa perbedaan cara penulisan nilai properti `source` saat kita memuat gambar dari **Internet** (menggunakan `{ uri: ... }`) dibandingkan memuat gambar **Lokal** dari folder `./assets/` (menggunakan `require('...')`)?
3. Jelaskan fungsi dari properti `flexDirection: 'row'` di dalam styling `cardContainer` di atas.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Aturan Tegas Text Node di Mobile
*   **Perbedaan Engine**: Browser web memiliki mesin parser HTML yang toleran dan otomatis membungkus string yatim piatu ke dalam node teks anonim. 
*   Di sistem operasi HP (Android/iOS), komponen native `ViewGroup` (Android) atau `UIView` (iOS) **sama sekali tidak memiliki kemampuan menampilkan teks**. Kemampuan menulis teks hanya dimiliki oleh kelas `TextView` (Android) atau `UILabel` (iOS). Oleh karena itu, React Native melarang keras penulisan teks di luar komponen `<Text>` untuk menghindari error fatal di level engine OS asli.

### Jawaban 2: Sintaks Load Image (URI vs Require)
*   **Gambar Internet (Dinamis)**: Menggunakan objek uri: `source={{ uri: 'https://...' }}`. Wajib disuplai ukuran lebar dan tinggi (`width` & `height`) di styling CSS, karena browser/HP tidak tahu dimensi gambar internet sebelum di-download.
*   **Gambar Lokal (Statis)**: Menggunakan fungsi require: `source={require('../assets/logo.png')}`. Tidak wajib menulis lebar tinggi karena compiler otomatis membaca ukuran dimensi fisik file gambar lokal saat proses build.

### Jawaban 3: Arah Layout Flexbox (Row)
*   **`flexDirection: 'row'`** merubah arah aliran penataan komponen anak di dalam `<View>` menjadi **Horizontal (dari kiri ke kanan)**. Tanpa penulisan row, secara default seluruh layout flexbox di React Native beraliran **Vertical (dari atas ke bawah / `column`)**, kebalikan dari CSS web yang default-nya horizontal.
