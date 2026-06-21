# Hari 75: Evaluasi Fase 4 - Fitur Lanjutan & Hardware
## Tantangan Akhir: Membuat Modul Distribusi Darah CITO Berbasis Lokasi GPS, Peta Radar Satelit, Serta Unggah Form Payload Bukti Kamera KTP Transaksi Relawan

Selamat datang di Hari 75! Ini adalah **hari terakhir dari Fase 4**.

Dalam 20 hari terakhir ini, Anda telah berhasil merajut jubah zirah fungsional hardware aplikasi mobile yang menembus batas browser konvensional. Anda telah menguasai penjagaan gembok Route Guards di layout Router, meminta Izin Pop-Up OS secara bijak sesuai konteks (*Foreground Permissions*), membajak membaca koordinat Latitude GPS petugas langsung dari Antena Satelit fisik HP (`expo-location`), menggambar kanvas map interaktif kelas berat menggunakan engine `react-native-maps`, menancapkan jarum Marker dan pop-up Callout, membuka lensa live feed sensor Kamera belakang (`expo-camera`), membajak isi Galeri privasi HP (`expo-image-picker`), mengemas kargo pengiriman biner Multipart FormData Axios beranimasi hitung-hitungan Progress Bar persentase, mengoptimasi ram List berkapasitas ribuan baris list riwayat menipis (`maxToRenderPerBatch`), membongkar mesin fungsi murni Array Pagination Javascript slice, meredam tembakan SPAM api perusak hitungan debouncing penahan batas, membaca brankas tembolok memory Caching Local offline pertama, mendeteksi memata-matai arah pergerakan nafas aplikasi (*AppState*) transisi background-foreground active, mendelegasikan tugas berat pelacak GPS tersembunyi tanpa batas Task Manager Background abadi, men-download membagikan resep data PDF ke Aplikasi eksternal WhatsApp via perantara laci rahasia File System, menjaring jaring laba-laba penangkap URL Deep Link, hingga menguasai bedah filter pembersih Regex pelindung aplikasi dari infeksi tulisan ketikan bahaya siluman.

Hari ini Anda ditantang merakit seluruh modul hardware dan optimasi kecepatan data ini ke dalam satu fitur aplikasi tugas jalan yang komprehensif mantap.

---

## 1. Spesifikasi Proyek Ujian Akhir Fase 4

Anda diminta membangun satu layar Menu Distribusi Kurir UDD PMI dengan rincian fitur gabungan sbb:

1.  **Layar Peta Distribusi CITO** (`app/(tabs)/distribusi.tsx`):
    *   Tampilkan elemen Peta MapView (PROVIDER_GOOGLE) yang layarnya diblok penuh.
    *   Pasang 1 buah Marker penanda warna Merah untuk rumah sakit RS Abdul Moeloek, lengkap dengan kotak Callout kecil bertuliskan *"Kirim Gol. O ke Sini!"*.
    *   Saat layar dibuka pertama kali, mintalah Izin GPS dari OS HP Anda. Jika di-Approve, paksa properti `showsUserLocation` menyala dan pindahkan koordinat awal posisi Peta mengarah menembak berpusat letak lokasi GPS asli HP Anda saat ini berada!
2.  **Lembar Drawer Bukti Laporan Penyerahan Darah**:
    *   Buat sebuah tombol melayang putih di pojok bawah layar peta berikon 📸. Saat ditekan, akan membuka layar Modal penuh dari bawah.
    *   Di dalam layar Modal, sediakan 2 metode pemilihan gambar: **Jepret Lensa Kamera Asli HP** atau **Pilih dari Album Galeri KTP**.
    *   Tampilkan Preview gambar yang sudah sukses diambil di atas form.
3.  **Unggahan Form Transaksi Progress Bar**:
    *   Sediakan kolom TextInput nama kurir pengirim di bawah preview gambar. Kolom ini harus terlindungi regex *Sanitizer* anti-simbol kutip ilegal.
    *   Sediakan satu tombol merah solid **"UNGGAH LAPORAN DISTRIBUSI BINER"**.
    *   Saat tombol di-klik, rakiti bungkus gambar tersebut ke kargo mesin `FormData`.
    *   Tembakkan FormData menggunakan pelatuk **Axios POST API**. Sematkan callback interceptor *onUploadProgress* untuk menghidupkan merentangkan Div animasi indikator Loading Bar warna Hijau dari angka persentase 0% memanjang ke arah 100% mengisi penuh bar layaknya sedotan air di bawah layar Modal.

---

## 2. Struktur Peta Rute Ujian Akhir Fase 4

```
donorku-mobile/
├── components/
│   └── LoadingOverlay.tsx        <-- Modal layar tancap loading (Hari 50)
└── app/
    ├── (tabs)/
    │   └── distribusi.tsx        <-- PETA MAPVIEW UTAMA! (Hari 59, 60, 58)
    └── modal-upload-bukti.tsx    <-- FORM BINER KAMERA & GALERI (Hari 61, 62, 63, 64)
```

---

## 3. Langkah Pengujian Integrasi Kelayakan Celah Hardware

Jalankan HP perangkat aslinya, lalu susuri empat tahap rintangan pembuktian fisik berikut:
1.  **Uji Coba Dialog OS Izin Privasi Hak Paten**: Buka tab halaman Peta Distribusi. Sistem Android/iOS Apple HP milik Anda **wajib memuntahkan satu tembakan pop-up interupsi bawaan sistem** (*"Izinkan Aplikasi PMI Mengakses Lokasi Perangkat Ini?"*). Tekan "Izinkan (Allow)".
2.  **Uji Coba Lensa Hardware Laporan Biner**: Buka lembar form modal dari bawah peta. Tekan tombol ambil foto kamera. Modul lensa fisik memotret nyata foto tangan jari Anda secara fokus. Setelah menekan jepret centang konfirmasi resolusi 0.7 kualitas kompres, layar balik modal wajib langsung menge-draw memunculkan Preview Image bayangan gambar kotak hasil foto jari tangan Anda tersebut berserta URL path `file:///data/user/` di monitor secara instan.
3.  **Uji Coba Gergaji Input Kotor**: Ketik sengaja menguji nama kurir: `Suprianto'#;>>` di kolom TextInput. Input otomatis menggergaji mencuci nama tersebut terhapus huruf haramnya menyisakan `"Suprianto"` yang aman saja.
4.  **Uji Coba Sirkuit Pengiriman Jalur Kargo Biner**: Tekan tombol unggah merah besar. Jika jaringan koneksi internet API tertembak benar, layar akan tidak berpindah statis membeku melainkan menyuguhkan bilah animasi Loading Progress Bar berwarna hijau memanjang cepat perlahan bergerak dari kiri kosong memakan ruang ke sisi kanan mentok berukuran merentang 100%, diakhiri kemunculan notifikasi sukses Toast di jidat dahi layar melayang!

---

## 4. Pesan Kelulusan Fase 4

Bila bar loading mulus merentang 100% dan jarum lokasi GPS titik biru akurat memancarkan radar kedip di peta layar HP tanpa meremukkan crash Android mesin HP memori merah Anda sedikitpun, sangat-sangat selamat luar biasa! Anda resmi diakui tangguh **LULUS MENAKLUKKAN FASE 4 (FASE HARDWARE INTEGRATION)**.

Di ronde ring tinju terminal fase pungkasan **Fase 5 (Hari 76 s.d Hari 90)**, kita akan memasuki tahap penyelesaian elit rilis produksi komersial (*Release Pipeline CI/CD*): Merakit melukis papan grafik interaktif indah **Chart Data Logistik** (`react-native-gifted-charts`), membangun ekosistem sinyal Push Notification ke seluruh HP relawan jarak jauh via awan **Expo Notifications Service (FCM)** menembak alarm bergetar berdering relawan tertidur di pelosok antah berantah, serta memproses panjang optimasi konfigurasi sertifikat enkripsi kunci Google Keystore kompilasi bundel mesin kompilator *EAS Build Tool (Expo Application Services)* merakit melahirkan file APK instalan murni utuh dan AAB biner siap dilemparkan menabrak perbatasan gerbang meja ulasan juri penilai publik Play Store dan App Store iOS.
```
[FASE 4: HARDWARE & ADVANCED OPTIMIZATION COMPLETED - PROGRESS 75/90 DAYS]
```
