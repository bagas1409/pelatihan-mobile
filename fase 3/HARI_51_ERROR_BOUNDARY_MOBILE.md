# Hari 51: Penanganan Error Global (ErrorBoundary)
## Mencegah Aplikasi Crash Keluar Sendiri Saat Terjadi Kesalahan Script Menggunakan ErrorBoundary Custom Component

Selamat datang di Hari 51! Hari ini kita akan mempelajari cara merancang **ErrorBoundary** untuk melindungi aplikasi mobile kita dari ancaman crash fatal.

Di dalam ekosistem React Native, jika terjadi kesalahan ketikan script JavaScript kecil saja di salah satu sub-komponen anak (seperti mencoba membaca properti dari objek bernilai *undefined*: `user.nama` padahal `user` bernilai `null`), **seluruh aplikasi mobile akan langsung crash force close keluar ke home screen HP secara instan**. Ini sangat berbahaya di lapangan. 

Kita akan merakit pembungkus steril yang bertugas menangkap error tersebut secara damai, menahan aplikasi agar tidak keluar, dan menyajikan layar peringatan yang user-friendly lengkap dengan tombol restart aplikasi.

---

## 1. Bagaimana ErrorBoundary Bekerja?

`ErrorBoundary` adalah komponen kelas khusus (*Class Component*) React. Hanya komponen kelas yang diperbolehkan memanggil metode siklus hidup **`componentDidCatch`** dan **`getDerivedStateFromError`** guna menangkap error JavaScript dari komponen-komponen anak di bawahnya layaknya blok `try-catch` visual.

---

## 2. Studi Kasus PMI: Komponen Pembungkus Penyelamat (`components/ErrorBoundary.tsx`)

Buat berkas penyelamat error global di direktori **`components/ErrorBoundary.tsx`**:

```tsx
// components/ErrorBoundary.tsx
import React, { Component, ErrorInfo, ReactNode } from 'react';
import { View, Text, TouchableOpacity, BackHandler } from 'react-native';

interface Props {
  children: ReactNode;
}

interface State {
  adaError: boolean;
  pesanError: string;
}

export default class ErrorBoundary extends Component<Props, State> {
  public state: State = {
    adaError: false,
    pesanError: ''
  };

  // 1. TANGKAP ERROR DAN UPDATE STATE STATUS
  public static getDerivedStateFromError(error: Error): State {
    return { adaError: true, pesanError: error.message };
  }

  // 2. LOG ERROR KE CONSOLE SERVER
  public componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    console.error("[ErrorBoundary Catch]:", error, errorInfo);
  }

  private handleTutupApp = () => {
    BackHandler.exitApp(); // Keluar aplikasi secara tertib
  };

  public render() {
    // Jika terdeteksi error script fatal
    if (this.state.adaError) {
      return (
        // Tampilkan Layar Peringatan Darurat Medis
        <View className="flex-1 bg-slate-50 justify-center items-center p-6 text-center">
          <View className="bg-white p-8 rounded-3xl border border-slate-200 shadow-sm items-center w-full max-w-sm space-y-4">
            
            <View className="w-16 h-16 rounded-full bg-red-100 items-center justify-center">
              <Text className="text-2xl">⚠️</Text>
            </View>

            <Text className="font-extrabold text-slate-800 text-sm">Terjadi Kesalahan Sistem</Text>
            
            <Text className="text-[10px] text-slate-400 text-center leading-relaxed">
              Aplikasi DonorKu mendeteksi kegagalan internal. Laporkan pesan ini ke admin IT PMI Lampung:
            </Text>

            <View className="w-full bg-slate-100 p-4 rounded-2xl border border-slate-200">
              <Text className="text-[9px] font-mono text-slate-600 leading-normal">
                {this.state.pesanError || 'Unknown JavaScript Error'}
              </Text>
            </View>

            <TouchableOpacity 
              onPress={this.handleTutupApp}
              className="w-full bg-slate-900 py-3.5 rounded-2xl items-center"
            >
              <Text className="text-white font-bold text-xs uppercase">Tutup Aplikasi</Text>
            </TouchableOpacity>

          </View>
        </View>
      );
    }

    // Jika normal, gambar komponen anak seperti biasa
    return this.props.children;
  }
}
```

Bungkus halaman terluar di layout global `src/app/_layout.tsx (atau app/_layout.tsx)` menggunakan komponen `<ErrorBoundary>` ini untuk melindungi seluruh alur sistem.

---

## 3. Latihan Soal Mandiri
1. Mengapa kita wajib menggunakan **Class Component** untuk merancang `ErrorBoundary`, bukan menggunakan Functional Component biasa?
2. Buka kode `componentDidCatch` di atas. Apa fungsi dari parameter `errorInfo`?
3. Sebutkan nama metode kelas React yang bertugas merubah state status `adaError` menjadi `true` sesaat setelah kesalahan script ditangkap.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Batasan Functional Component
*   **Ketiadaan Lifecycle Khusus**: Modul penangkap error `componentDidCatch` dan `getDerivedStateFromError` adalah fitur eksklusif milik arsitektur Class Component React. 
*   Sampai rilis React modern saat ini, **belum diciptakan Hook React setara** (misal `useErrorBoundary`) untuk menangkap error di Functional Component, sehingga pembuatan Class Component bersifat mutlak untuk kasus ini.

### Jawaban 2: Analisis Jejak Crash (Stack Trace)
*   **Detektor File**: Parameter `errorInfo` berisi rekaman jejak baris file (*Stack Trace*) di mana letak persis crash terjadi (contoh: *crash terjadi di `CardRelawan.tsx` baris 24*). 
*   Ini berguna jika kita ingin mengirimkan laporan bug tersebut ke server log pemantau (seperti Sentry) untuk bahan perbaikan kode di masa depan.

### Jawaban 3: getDerivedStateFromError
*   Metode tersebut adalah **`getDerivedStateFromError(error)`**.
```
[FASE 3: HARI 51 SUKSES]
```
