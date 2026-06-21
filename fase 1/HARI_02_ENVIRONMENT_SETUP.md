# Hari 2: Setup Development Environment
## Menginstal Node.js, Git, VS Code Extensions, Serta Mempersiapkan Expo Go di Handphone Fisik

Selamat datang di Hari 2! Hari ini kita akan mempersiapkan perkakas tempur di laptop kita dan handphone fisik kita agar siap merakit serta menguji aplikasi mobile secara real-time.

Kita akan menggunakan **Expo Go**, sebuah aplikasi pembantu di HP yang dikembangkan oleh tim Expo agar kita bisa melihat hasil ketikan kode di layar HP dalam waktu 1 detik tanpa perlu mengunduh Android Studio SDK yang berukuran belasan gigabyte di awal belajar.

---

## 1. Langkah Persiapan di Laptop (Development PC)

### A. Instalasi Node.js (LTS Version)
React Native dan Expo membutuhkan Node.js sebagai mesin pengelola dependensi paket javascript.
1. Unduh Node.js versi LTS terbaru dari situs resmi [nodejs.org](https://nodejs.org/).
2. Jalankan file installer, ikuti petunjuknya sampai selesai.
3. Buka terminal terminal (Command Prompt / PowerShell) dan ketik perintah berikut untuk memverifikasi kesuksesan:
   ```bash
   node -v
   npm -v
   ```

### B. Instalasi Git & VS Code
1. Unduh dan pasang editor kode **Visual Studio Code** dari [code.visualstudio.com](https://code.visualstudio.com/).
2. Unduh **Git** dari [git-scm.com](https://git-scm.com/) untuk manajemen repositori proyek.

### C. Pasang Ekstensi VS Code Wajib
Buka menu Extensions di VS Code (klik ikon kotak empat di sisi kiri atau tekan `Ctrl+Shift+X`) lalu cari dan pasang:
1.  **Expo Tools** (Membantu autocompletion file konfigurasi Expo `app.json`).
2.  **React Native Tools** (Membantu proses debugging kode react native).
3.  **Tailwind CSS IntelliSense** (Membantu memunculkan auto-complete class utility NativeWind/Tailwind).

---

## 2. Langkah Persiapan di Handphone Fisik (Android / iOS)

Kita tidak akan menggunakan emulator virtual yang berat dan memakan RAM laptop. Kita akan langsung menguji di HP fisik kita sendiri:
1.  **Pengguna Android**: Buka Google Play Store, cari dan pasang aplikasi **Expo Go** buatan *Expo Project*.
2.  **Pengguna iPhone (iOS)**: Buka Apple App Store, cari dan pasang **Expo Go**.
3.  Pastikan Laptop dan HP fisik Anda terhubung pada **satu jaringan Wi-Fi yang sama** (sangat krusial agar laptop bisa memancarkan sinyal server ke HP Anda).

---

## 3. Latihan Soal Mandiri
1. Sebutkan versi Node.js yang terpasang di komputer Anda setelah menjalankan perintah `node -v` di terminal.
2. Mengapa handphone fisik Anda dan laptop wajib terhubung pada satu jaringan Wi-Fi/Hotspot yang sama saat menggunakan Expo Go? Jelaskan jalur komunikasinya.
3. Apa keunggulan menguji aplikasi menggunakan handphone fisik dibandingkan menggunakan Android Emulator bawaan Android Studio dari segi penggunaan daya RAM laptop?

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Verifikasi Node.js
*   *Jawaban disesuaikan dengan output komputer Anda*. Biasanya versi LTS yang direkomendasikan adalah versi 20.x atau yang lebih baru.

### Jawaban 2: Komunikasi Server Lokal (LAN)
*   Saat kita menyalakan proyek Expo di laptop, Expo akan membuat server lokal (misalnya di alamat IP `192.168.1.10:8081`). 
*   Agar aplikasi Expo Go di HP bisa mengunduh file bundel JavaScript tersebut dari laptop Anda, kedua perangkat harus berada di dalam jaringan lokal (LAN) yang sama agar bisa saling berkirim paket data IP. Jika berbeda jaringan, HP Anda tidak akan bisa melacak keberadaan server laptop.

### Jawaban 3: Efisiensi Resource RAM
*   **Android Emulator**: Membuka emulator virtual sama dengan menyalakan sistem operasi Android penuh di dalam laptop Anda. Ini memakan RAM minimal **4GB s.d 8GB** secara konstan dan membuat kipas laptop berputar kencang panas.
*   **HP Fisik**: Seluruh proses rendering grafis dan konsumsi memori sistem operasi dijalankan langsung oleh CPU & RAM handphone Anda sendiri. Laptop Anda hanya bertugas mengirim berkas teks kode kecil via Wi-Fi, menghemat kapasitas RAM laptop Anda agar tetap ringan untuk menulis kode.
