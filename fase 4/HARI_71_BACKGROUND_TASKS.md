# Hari 71: Tugas Latar Belakang (Background Tasks)
## Mengirim Update Lokasi GPS Ambulans Secara Diam-Diam Saat Aplikasi Diminimalkan Menggunakan expo-task-manager

Selamat datang di Hari 71! Di Hari 70, kita belajar menghentikan proses saat aplikasi diminimalkan (`background`). Namun, ada **kasus pengecualian khusus** di mana kita *wajib* membiarkan proses tetap berjalan di latar belakang: **Pelacakan Lokasi Ambulans Darurat**.

Admin pusat PMI Lampung harus terus memantau posisi mobil kurir ambulans di peta meskipun sopir mematikan layar HP (Screen Off) atau membuka Google Maps navigasi. Kita tidak bisa memakai `setInterval` biasa karena sistem operasi Android/iOS akan langsung membunuh proses kita dalam 3 menit. Kita wajib mendaftarkan proses resmi menggunakan **`expo-task-manager`**.

---

## 1. Pemasangan Pustaka Khusus Background

Jalankan perintah berikut di terminal Anda:
```bash
npx expo install expo-task-manager expo-location
```
*(Catatan: Anda juga wajib menambah plugin permission khusus lokasi background di file `app.json` saat merakit APK).*

---

## 2. Struktur Dasar Penugasan Background Task

Aturan emas `Task Manager`: Pendaftaran nama tugas dan fungsi eksekutornya wajib diletakkan di luaran *Global Scope* (di luar komponen React manapun, biasanya di file root), karena tugas ini berumur lebih panjang dari layar UI dan dijalankan oleh benang sistem UI utama Android/iOS OS.

---

## 3. Studi Kasus PMI: Pelacak Ambulans Latar Belakang (`app/_layout.tsx`)

Buka file root layout Anda, tambahkan pendefinisian tugas GPS background di luar fungsi komponen:

```tsx
// app/_layout.tsx
import React, { useEffect } from 'react';
import { View, Text, TouchableOpacity } from 'react-native';
import * as TaskManager from 'expo-task-manager';
import * as Location from 'expo-location';

// 1. TENTUKAN NAMA TUGAS RAHASIA PENGINGAT
const NAMA_TUGAS_PELACAK_GPS = 'AMBULANS_BACKGROUND_GPS_TASK';

// 2. DAFTARKAN FUNGSI EKSEKUTOR DI LUAR KOMPONEN REACT (GLOBAL SCOPE)
TaskManager.defineTask(NAMA_TUGAS_PELACAK_GPS, async ({ data, error }) => {
  if (error) {
    console.error("[BG Task Error]:", error);
    return;
  }
  if (data) {
    const { locations } = data as any;
    // Koordinat terbaru ambulans di latar belakang ditangkap di sini!
    const lintang = locations[0].coords.latitude;
    const bujur = locations[0].coords.longitude;
    
    console.log(`📡 [Kirim API Background]: Mengirim posisi Ambulans (${lintang}, ${bujur}) ke server PMI...`);
    // Eksekusi Axios PUT/PATCH API ke database server di sini!
  }
});

export default function LayoutUtamaBackgroundTasks() {

  // FUNGSI PEMICU SAKLAR MENYALAKAN FITUR BACKGROUND OS
  const nyalakanRadarBackground = async () => {
    // Izin ganda! Minta izin khusus 'Background' lokasi
    const { status: statusForeground } = await Location.requestForegroundPermissionsAsync();
    if (statusForeground === 'granted') {
      const { status: statusBackground } = await Location.requestBackgroundPermissionsAsync();
      
      if (statusBackground === 'granted') {
        console.log("[Radar] Menyalakan radar satelit abadi latar belakang...");
        
        // 3. DAFTARKAN KONTRAK PELACAKAN KEPADA SISTEM OPERASI HP
        await Location.startLocationUpdatesAsync(NAMA_TUGAS_PELACAK_GPS, {
          accuracy: Location.Accuracy.Balanced, // Akurasi Menengah (hemat baterai)
          timeInterval: 15000, // Minimal Update setiap 15 Detik
          distanceInterval: 50, // Atau Update setiap bergerak sejauh 50 meter!
          showsBackgroundLocationIndicator: true, // Wajib iOS: Munculkan notifikasi biru di atas HP status bar
          foregroundService: {
            // Wajib Android: Munculkan Notifikasi Lengket bahwa aplikasi sedang memantau
            notificationTitle: 'GPS PMI Aktif',
            notificationBody: 'Melacak rute distribusi CITO.',
          }
        });
      }
    }
  };

  const matikanRadar = async () => {
    await Location.stopLocationUpdatesAsync(NAMA_TUGAS_PELACAK_GPS);
    console.log("[Radar] Satelit dimatikan. Istirahat.");
  };

  return (
    <View className="flex-1 bg-white justify-center p-6 space-y-4">
      {/* ... Layar Navigasi Anda yang biasa ... */}
      <View className="p-6 bg-slate-50 border border-slate-200 rounded-3xl space-y-3">
        <Text className="font-bold text-slate-800 text-sm">Pelacak Ambulans</Text>
        <TouchableOpacity onPress={nyalakanRadarBackground} className="bg-red-500 p-3 rounded-xl items-center">
          <Text className="text-white font-bold text-xs">Mulai Pelacakan Latar Belakang</Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={matikanRadar} className="bg-slate-200 p-3 rounded-xl items-center">
          <Text className="text-slate-600 font-bold text-xs">Stop Pelacakan</Text>
        </TouchableOpacity>
      </View>
    </View>
  );
}
```

---

## 4. Latihan Soal Mandiri
1. Apa alasan keamanan fatal mengapa modul pelacak `Location.requestBackgroundPermissionsAsync()` mewajibkan developer untuk mengisi parameter pengaturan `foregroundService.notificationTitle` pada HP platform Android?
2. Buka baris blok pengaturan `distanceInterval: 50`. Apa fungsi efisiensi dari variabel penahan satuan panjang meter ini bagi keawetan jumlah kuota pulsa internet petugas armada?
3. Sebutkan nama aturan tempat khusus lokasi global scope di mana blok definisi pendaftaran fungsi tubuh `TaskManager.defineTask(...)` HANYA boleh ditulis! Mengapa penulisan fungsi TaskManager diharamkan dimasukkan diselipkan di dalam komponen visual layar React (Hooks)?

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Perlindungan Privasi Anti-Mata-Mata (Anti Spyware Policy)
*   **Transparansi Pemantauan Latar Belakang OS**: Kebijakan Google secara tegas melarang keras pencurian data lokasi koordinat manusia secara sembunyi-sembunyi saat layar HP mati. 
*   Memasukkan parameter `foregroundService` ini bertindak sebagai surat sumpah aplikasi di mana sistem Android secara otomatis diwajibkan menjahitkan satu **Banner Notifikasi Lem (Sticky Notification)** yang tidak bisa dihapus di layar laci HP Petugas yang tertulis: *"Aplikasi ini sedang menggunakan dan membaca GPS Lokasi Anda di Belakang"*, memastikan privasi sadar sang manusia supir tetap transparan dilindungi.

### Jawaban 2: Pencegahan Penembakan API Lokasi Diam-Diam (Idle Restrict Optimization)
*   **Sensor Jarak Geser Hemat**: Variabel angka pembatas jarak ini menyelamatkan miliaran tembakan request API sia-sia. 
*   Jika ambulans petugas terjebak macet total lampu merah dan tidak bergerak satu milimeter pun, fungsi pembaca lokasi sensor ini akan otomatis di-"Tidurkan" paksa, tidak membuang data pemanggilan server. Update GPS hanya ditarik dikirim jika ban mobil supir sudah bergerak minimal bergeser sejauh lebih dari 50 meter radius diameter menjauh dari titik koordinat yang lama.

### Jawaban 3: Terkunci di Ruang Angkasa Global (Global Scope Requirement)
*   **Lolos Dari Pembantaian Lifecycle Rendering UI**: Blok pendaftar memori Task Manager **harus berdiri sendirian** di luar batas `{...}` kurung komponen root utama. 
*   Apabila blok tersebut diletakkan di dalam layar fungsi UI, maka saat petugas memindah/menutup layar tab tersebut, React akan otomatis men-destruct menghancurkan fungsi task pelacak tersebut mati bersama layarnya. Letak yang di luar menempatkannya sejajar dengan memori Kernel sistem UI Root aplikasi agar fungsi pelacak dapat terus bernapas abadi walaupun komponen-komponen UI lain telah terpotong dibantai mati ditelan OS di saat RAM habis.
```
[FASE 4: HARI 71 SUKSES]
```
