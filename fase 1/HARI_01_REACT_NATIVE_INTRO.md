# Hari 1: Pengenalan React Native & Arsitektur Baru
## Memahami Cara Kerja React Native Menjembatani JavaScript dengan Engine Grafis Asli HP (Java/Swift)

Selamat datang di Hari 1! Hari ini kita akan memulai perjalanan menjadi Mobile Frontend Developer menggunakan **Expo** dan **React Native**.

Sebelum kita mulai mengetik kode program, kita harus memahami terlebih dahulu apa itu React Native, bagaimana aplikasi JavaScript bisa berjalan mulus di dalam handphone Android & iOS, serta apa keunggulan arsitektur modern (**New Architecture / TurboModules**) dibandingkan arsitektur jembatan lama.

---

## 1. Analogi Sederhana: Penerjemah Bahasa Dinas UDD
Bayangkan Anda adalah seorang petugas dinas PMI Lampung yang hanya bisa berbicara **Bahasa Indonesia** (diwakili oleh **JavaScript**). Anda ingin mendirikan posko donor darah di dua pulau yang berbeda:
1.  **Pulau Android**: Penduduknya hanya mengerti **Bahasa Jawa** (diwakili oleh bahasa **Java / Kotlin**).
2.  **Pulau iOS**: Penduduknya hanya mengerti **Bahasa Sunda** (diwakili oleh bahasa **Objective-C / Swift**).

Bagaimana agar Anda bisa menyuruh pekerja di kedua pulau tersebut membuat gedung posko tanpa harus mempelajari bahasa daerah mereka?
*   **Solusi React Native**: React Native bertindak sebagai **Penerjemah Dinas** yang cerdas. Anda cukup memberikan instruksi dalam Bahasa Indonesia (JavaScript), lalu React Native akan menerjemahkan instruksi tersebut ke dalam bahasa daerah masing-masing pulau secara otomatis di latar belakang.
*   Hasilnya, Anda mendapatkan posko fisik asli (*Native Components*) yang dibangun oleh pekerja lokal daerah tersebut, bukan sekedar gambar posko di dalam kertas (seperti aplikasi berbasis Web/WebView biasa).

---

## 2. Cara Kerja React Native: Bridge vs New Architecture

Sejak tahun 2015 s.d 2023, React Native bekerja menggunakan sistem **Bridge (Jembatan)**.
*   **Masalah Bridge**: JavaScript dan Native berkomunikasi dengan saling bertukar pesan JSON melalui satu jembatan sempit secara bergiliran (*Asynchronous Serialization*). Jika petugas mengirimkan instruksi animasi scroll tabel relawan yang sangat cepat, jembatan ini akan tersumbat (*Bottleneck*), menyebabkan animasi terasa tersendat/patah-patah (*Lag*).
*   **New Architecture (TurboModules & JSI)**: Mulai tahun 2024 ke atas (Expo SDK terbaru), jembatan tersebut dihancurkan dan diganti dengan teknologi **JSI (JavaScript Interface)**. JavaScript kini bisa langsung memanggil fungsi Native Java/Swift secara instan di memori RAM tanpa perlu konversi JSON (*Direct Execution*), menghasilkan performa aplikasi mobile yang setara dengan aplikasi native murni buatan Google/Apple.

---

## 3. Latihan Soal Mandiri
1. Jelaskan perbedaan mendasar antara aplikasi **React Native (Hybrid Native)** dengan aplikasi **Webview Hybrid** (seperti Cordova/Ionic) dari segi hasil akhir elemen visual yang digambar di layar HP.
2. Mengapa teknologi **JSI (JavaScript Interface)** pada New Architecture React Native bisa membuat performa animasi scroll data relawan terasa jauh lebih mulus dibandingkan sistem **Bridge** lama?
3. Sebutkan nama bahasa pemrograman asli yang digunakan sistem operasi Android dan iOS untuk menggambar komponen tombol layar sebelum diterjemahkan oleh React Native.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Perbedaan React Native vs WebView
*   **React Native**: Menggambar komponen fisik asli sistem operasi HP. Saat Anda menulis `<Text>`, HP Android akan merender komponen `android.widget.TextView` asli dan iOS merender `UITextView` asli.
*   **WebView**: Hanya membuka browser web mini tersembunyi di dalam aplikasi. Komponen digambar menggunakan HTML5 biasa, yang kinerjanya jauh lebih lambat karena CPU HP terbebani oleh proses rendering halaman web penuh di dalam memori.

### Jawaban 2: Performa JSI vs Bridge
*   **JSI** membuang proses konversi teks JSON berulang-kali. JavaScript bisa langsung memegang alamat memori C++ milik modul Java/Swift secara sinkron (*Synchronous Call*), memotong jalur birokrasi komunikasi data sehingga animasi scroll 60 FPS bisa dicapai dengan konsumsi RAM yang sangat minim.

### Jawaban 3: Bahasa Pemrograman Native Asli
*   **Android**: Java atau Kotlin.
*   **iOS**: Objective-C atau Swift.
*   React Native membebaskan kita dari menulis kedua pasang bahasa tersebut, cukup menulis satu berkas TypeScript/JavaScript.
