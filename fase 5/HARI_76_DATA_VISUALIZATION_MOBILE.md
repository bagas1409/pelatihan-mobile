# Hari 76: Visualisasi Data Mobile
## Pentingnya Grafik Visual Statistik Donor Untuk Mempermudah Pembacaan Stok Darah Di HP Petugas PMI

Selamat datang di Hari 76! Hari ini kita memulai **Fase 5 (Fase Terakhir)** dari program pelatihan mobile front-end developer.

Fase ini berfokus pada penyajian data analitik yang indah serta persiapan kompilasi rilis produksi aplikasi Anda ke Google Play Store & Apple App Store. Hari ini kita akan membahas teori dasar **Visualisasi Data Mobile**. 

Di dashboard PMI Lampung, jika petugas hanya disuguhkan daftar deretan teks angka tabel stok darah secara baris-per-baris, otak mereka akan membutuhkan waktu lama untuk menyimpulkan golongan darah mana yang kritis menipis. Dengan visualisasi grafik, kesimpulan tersebut bisa diambil secara instan dalam 1 detik.

---

## 1. Hambatan Layar Mobile Bagi Grafik

Merancang grafik di smartphone memiliki tantangan ergonomi tersendiri dibandingkan desktop:
1.  **Keterbatasan Ruang (Narrow Canvas)**: Layar HP sangat sempit secara horizontal. Grafik garis (*Line Chart*) dengan 30 titik koordinat tanggal akan saling menumpuk tidak terbaca.
2.  **Senggolan Jari (Touch Target)**: Batang grafik (*Bar*) harus cukup lebar agar bisa disentuh oleh ujung jari petugas yang ingin memunculkan pop-up tooltip detail stok kantong.
3.  **Responsivitas Orientasi**: Grafik harus mampu menyesuaikan lebarnya secara fleksibel jika petugas memutar posisi layar HP mereka menjadi tidur (*Landscape Mode*).

---

## 2. Pilihan Jenis Grafik untuk Kasus PMI

*   **Grafik Batang (Bar Chart)**: Paling ideal untuk membandingkan sisa jumlah stok kantong darah antar golongan darah (A vs B vs O vs AB).
*   **Grafik Donat (Donut Chart)**: Paling cantik untuk menampilkan proporsi persentase distribusi golongan darah yang paling banyak disalurkan bulan ini.

---

## 3. Library Pilihan: `react-native-gifted-charts`

Dibandingkan dengan library lawas seperti `react-native-chart-kit` yang kaku dan lambat, **`react-native-gifted-charts`** adalah pustaka modern yang sangat responsif, memiliki performa rendering GPU tinggi, dan mendukung styling Tailwind NativeWind secara penuh.

---

## 4. Latihan Soal Mandiri
1. Sebutkan 2 hambatan utama perancangan visualisasi grafik analitik pada perangkat mobile (smartphone) dibandingkan komputer desktop layar lebar!
2. Mengapa grafik bertipe **Bar Chart** lebih unggul untuk menyajikan data perbandingan kuantitatif sisa kantong darah PMI dibandingkan grafik lingkaran pie chart penuh?
3. Sebutkan nama pustaka/library modern React Native penggambar bagan grafik yang mendukung integrasi NativeWind secara fleksibel.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Ruang Piksel Sempit & Akurasi Sentuhan Jari
*   **Kanvas Terbatas**: Layar HP sempit menuntut pemangkasan legenda teks grafik agar tidak tumpang tindih. 
*   **Touch Precision**: Ujung jari manusia membutuhkan target tap minimal 44x44 piksel. Batang grafik yang terlalu tipis akan mustahil disentuh petugas untuk melihat tooltip angka stok detailnya.

### Jawaban 2: Kejelasan Skala Linear
*   Mata manusia secara natural sangat ahli membandingkan perbedaan tinggi garis vertikal (grafik batang), namun sangat payah memperkirakan proporsi pembagian sudut lingkaran (pie chart) jika selisihnya tipis (misal membedakan irisan 23% dengan 25% pada donat stok darah).

### Jawaban 3: Pustaka react-native-gifted-charts
*   Pustaka tersebut adalah **`react-native-gifted-charts`**.
```
[FASE 5: HARI 76 SUKSES]
```
