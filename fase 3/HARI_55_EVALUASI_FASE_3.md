# Hari 55: Evaluasi Fase 3 - Integrasi API & State Management
## Tantangan Akhir: Membangun Form Login Petugas Terintegrasi API Backend, Menyimpan Token Ke SecureStore, Serta Proteksi Akses Layar Menggunakan Zustand Auth Store

Selamat datang di Hari 55! Ini adalah **hari terakhir dari Fase 3**.

Dalam 20 hari terakhir ini, Anda telah berhasil melintasi jembatan asinkron aplikasi mobile. Anda telah menguasai Axios Instance terpusat, Custom Fetcher Hooks `useApi`, penjinakan CORS & IP virtual Android Emulator `10.0.2.2`, rendering dynamic list terintegrasi GET API, penguasaan gestur Pull-to-Refresh, State Management Global menggunakan **Zustand**, pembuatan Auth Store, persistensi Token JWT aman menggunakan **`expo-secure-store`**, otomatisasi Bearer Token via Axios Interceptors, validasi skema input ketat menggunakan **React Hook Form** + **Zod Schema**, perancangan visual Live Form Feedback border merah, pengeksekusan Axios POST API, pelemparan Toast notifikasi melayang, pembuatan layar transisi Loading Overlay global, pengamanan crash fatal via `ErrorBoundary`, optimasi loading gambar cerdas via `expo-image`, pengiriman modifikasi parsial Axios PATCH/PUT, dan perekaman preferensi lokal non-sensitif menggunakan `AsyncStorage`.

Hari ini Anda ditantang menyatukan seluruh materi integrasi data di atas ke dalam alur otentikasi nyata yang aman.

---

## 1. Spesifikasi Proyek Ujian Akhir Fase 3

Anda diminta merakit alur otentikasi login dinas petugas PMI Lampung dengan mekanisme sebagai berikut:

1.  **Formulir Login Petugas** (`app/login.tsx`):
    *   Form dibuat menggunakan **React Hook Form** & **Zod Schema**.
    *   Kolom input Email wajib berformat email, dan sandi password minimal 6 karakter.
    *   Jika input salah, warna border TextInput otomatis berubah merah menyala dengan teks deskripsi error di bawahnya.
2.  **Transaksi POST Authentication**:
    *   Saat tombol login ditekan, tampilkan **LoadingOverlay** global dan kirim payload ke endpoint API `/auth/login` menggunakan Axios POST.
3.  **Rekam Session & Persistensi Token**:
    *   Jika server backend membalas sukses (HTTP 200), rekam data user petugas ke dalam **Zustand Auth Store** (`loginPetugas`), dan kunci string token JWT-nya ke dalam **`expo-secure-store`** menggunakan helper `storageHelper.simpanToken`.
    *   Picu notifikasi melayang **Toast** berwarna hijau bertuliskan *"Login Berhasil, Selamat bertugas!"*.
4.  **Verifikasi Boooting Awal (Session Restorer)**:
    *   Di file root `src/app/_layout.tsx (atau app/_layout.tsx)`, tulis `useEffect` awal yang bertugas membaca `SecureStore` secara otomatis. Jika di HP petugas terdeteksi token lama yang masih aktif, panggil API `/auth/me` untuk memulihkan status login ke Zustand secara otomatis, melompati layar login, dan langsung mengarahkan layar ke halaman Dashboard utama.

---

## 2. Struktur File Ujian Akhir Fase 3

Pastikan seluruh file ini terbuat dan terkonfigurasi dengan benar:

```
donorku-mobile/
├── utils/
│   ├── api.ts          <-- Axios instance with Interceptor (Hari 44)
│   └── storage.ts      <-- SecureStore Helper (Hari 43)
├── store/
│   └── useAuthStore.ts <-- Zustand Auth Store (Hari 42)
├── components/
│   ├── ErrorBoundary.tsx
│   └── LoadingOverlay.tsx
└── app/
    ├── login.tsx       <-- Login screen (Zod form + POST Axios)
    ├── (tabs)/
    │   └── index.tsx   <-- Protected Dashboard
    └── _layout.tsx     <-- Root (Check token storage -> redirect page)
```

---

## 3. Langkah Pengujian Kelayakan Otorisasi API

Lakukan 3 simulasi pengujian kualitas integrasi data berikut:
1.  **Uji Coba Validasi Input**: Ketik email salah format di form login $\rightarrow$ Pastikan border TextInput berubah menjadi merah dan tombol login terkunci tidak bisa ditekan.
2.  **Uji Coba Kirim API Real**: Masukkan email & password benar, klik Login $\rightarrow$ Layar gelap LoadingOverlay wajib menyelimuti HP sebentar, lalu menyembul Toast hijau sukses, dan layar dialihkan secara paksa ke halaman dashboard tab.
3.  **Uji Coba Persistensi RAM**: Setelah sukses masuk ke dashboard, matikan emulator/matikan paksa aplikasi Anda (*Kill Application Process*). Buka kembali aplikasi Anda $\rightarrow$ Aplikasi **wajib langsung masuk ke halaman dashboard** tanpa meminta login ulang, menandakan helper `SecureStore` sukses mengunci sesi petugas.

---

## 4. Pesan Kelulusan Fase 3

Jika alur login terintegrasi server API di atas berjalan mulus tanpa bug memory leaks di HP Anda, selamat! Anda dinyatakan **LULUS FASE 3**.

Di **Fase 4 (Hari 56 s.d Hari 75)**, kita akan masuk ke area fungsional hardware native HP: merancang proteksi rute halaman (*Route Guards*), meminta izin akses sensor OS HP (*OS Permissions*), membaca koordinat GPS satelit petugas (`expo-location`), menggambar peta interaktif **Google Maps/Apple Maps** (`react-native-maps`), memotret barang logistik menggunakan kamera fisik HP (`expo-camera`), mengunggah bukti foto pendaftaran menggunakan metode **FormData (Multipart Upload)**, serta memproses daur ulang data halaman besar menggunakan teknik **Pagination** & **Infinite Scroll**.
```
[FASE 3: API & STATE MANAGEMENT COMPLETED - PROGRESS 55/90 DAYS]
```
