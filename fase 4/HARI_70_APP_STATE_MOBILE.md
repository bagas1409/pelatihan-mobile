# Hari 70: Memahami App States (Siklus Kehidupan Aplikasi)
## Mendeteksi Apakah Aplikasi Sedang Aktif Dilihat Atau Dimasukkan Ke Latar Belakang Saku Petugas (Background/Active)

Selamat datang di Hari 70! Berbeda dengan Tab Website di Desktop yang sering ditumpuk, pengguna aplikasi mobile sangat agresif dalam meminimalkan (*Minimize*) aplikasi.

Petugas PMI sering mengecek posisi map penyebaran stok, lalu mereka mengecilkan (minimize) aplikasi DonorKu untuk membuka aplikasi WhatsApp, lalu kembali lagi ke aplikasi DonorKu. Di ekosistem React Native, pergerakan "mengecilkan" aplikasi ke latar belakang dan "membesarkan" memanggilnya kembali ini dimonitor oleh modul super penting bernama **`AppState`**.

---

## 1. Tiga Status Fase Aplikasi Mobile

Bila kita melacak nilai `AppState.currentState`, sistem OS akan mengembalikan 3 tipe nilai:
1.  **`active`**: Aplikasi sedang tampil mentereng mendominasi penuh di monitor HP. User sedang berinteraksi menyentuhnya.
2.  **`background`**: Aplikasi dimatikan layarnya/ditumpuk di balik bayang aplikasi lain. (*Tidak nampak mata sama sekali*).
3.  **`inactive`** (Khusus iOS iPhone): Masa transisi beberapa mili-detik sesaat saat ada telepon mendadak masuk (*Incoming Call*) yang memblokir klik layar padahal visual UI Anda masih tampil transparan.

---

## 2. Studi Kasus PMI: Refresh Data CITO Saat Buka Aplikasi

Kita akan menyetel pelacak sensor agar setiap kali petugas selesai asik di WhatsApp lalu membuka kembali aplikasi DonorKu, aplikasi **otomatis me-refresh hitungan stok CITO tanpa disuruh**.

```tsx
// components/PemantauAktivitas.tsx
import React, { useEffect, useState, useRef } from 'react';
import { View, Text, AppState, AppStateStatus } from 'react-native';

export default function PemantauAktivitas() {
  const [statusApp, setStatusApp] = useState(AppState.currentState);
  
  // Menggunakan useRef agar kita bisa membandingkan pergeseran nilai sebelumnya
  const refStatusLama = useRef(AppState.currentState);

  useEffect(() => {
    // 1. Fungsi Listener Callback yang terpelatuk saat ada perubahan nasib UI Aplikasi
    const pergerakanAplikasi = (statusBaru: AppStateStatus) => {
      
      // LOGIKA: Jika tadinya dari BACKGROUND/INACTIVE, kini melompat menjadi ACTIVE
      if (
        refStatusLama.current.match(/inactive|background/) &&
        statusBaru === 'active'
      ) {
        console.log("[AppState] 🌟 APLIKASI KEMBALI DIBUKA! Menyegarkan koneksi...");
        // CONTOH AKSI: Tembak Ulang Data Refresh API di Sini! (kocokUlang())
      }

      if (statusBaru === 'background') {
        console.log("[AppState] 💤 APLIKASI DISEMBUNYIKAN KE SAKU... Stop Animasi & Polling Interval!");
      }

      // Update nilai record sejarah
      refStatusLama.current = statusBaru;
      setStatusApp(statusBaru);
    };

    // 2. Mendaftarkan Telinga Pendengar ke Modul OS Root Device
    const pendaftarListener = AppState.addEventListener('change', pergerakanAplikasi);

    // 3. CLEANUP wajib saat komponen dibongkar
    return () => {
      pendaftarListener.remove();
    };
  }, []);

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-2 items-center">
      <Text className="font-extrabold text-slate-800 text-sm">Status Hidup Aplikasi</Text>
      
      <View className={`px-4 py-2 rounded-full border ${
        statusApp === 'active' ? 'bg-emerald-50 border-emerald-100' : 'bg-slate-100 border-slate-200'
      }`}>
        <Text className={`font-black text-[10px] tracking-widest uppercase ${
          statusApp === 'active' ? 'text-emerald-500' : 'text-slate-500'
        }`}>
          {statusApp}
        </Text>
      </View>

      <Text className="text-[10px] text-slate-400 text-center px-4 mt-2">
        Kecilkan aplikasi ini ke home HP, lalu buka kembali untuk melihat aksi penyegaran otomatis terekam di console.log terminal Anda.
      </Text>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi praktis pendaftaran fungsi pendengar sensor kejadian `AppState.addEventListener('change', ...)` di perangkat ponsel Android/iOS di atas?
2. Buka kode blok pengandaian penyegaran API layar: `if (refStatusLama.current.match(/inactive|background/) && statusBaru === 'active')`. Jelaskan alasan kuat kita diwajibkan untuk mengingat nilai status sejarah masa lalu dari nilai referensi logika `useRef`.
3. Mengapa kita sangat disarankan untuk melakukan penghentian secara sadar pemanggil waktu `setInterval` atau Video Playing saat sensor melempar status baru `'background'`? Hubungkan alasan tersebut ke efisiensi suhu komponen hardware chipset handphone.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Pengontrol Siklus Ekosistem Aplikasi (App Lifecycle Hook)
*   **Respon Kondisi Nyata**: Layar mobile diatur oleh pergerakan pengguna yang tak kasat mata (seperti menutup jendela task manager). Sensor event AppState menancapkan kuping pemantau yang bertugas membangunkan dan menidurkan fitur program internal kita secara dinamis agar bereaksi adaptif terhadap tindakan drastis mematikan/menghidupkan GUI aplikasi.

### Jawaban 2: Memastikan Transisi Kembali Hidup (Return from Sleep Transition)
*   **Membaca Arah Mata Angin Pola Perilaku**: Metode listener terpanggil setiap kali layar berpindah wujud. Jika kita hanya mengecek parameter argumen `statusBaru === 'active'`, API akan salah ditarik kembali ter-refresh dua kali saat aplikasi baru booting render awal mula.
*   Dengan merunut sejarah `refStatusLama` kita merakit sistem filter cerdas yang memastikan program Refresh API CITO tersebut ***HANYA*** menembak database saat kejadian murni terjadi yaitu: *Status saat ini adalah aktif melek, yang didahului status masa lalu baru saja ketiduran (background)*.

### Jawaban 3: Penyelamatan Baterai Latar Belakang (Battery Drain Prevention)
*   **Terminator Penghisap Baterai Siluman**: Saat petugas mengecilkan layar ke saku (masuk mode `background`), mereka mengira program sedang berdiam mati santai. 
*   Jika Anda membiarkan baris code logika `setInterval` API Polling setiap 3 detik atau putaran animasi loping video terus berdetak hidup menuntut kalkulasi CPU di belakang punggung OS, aplikasi akan ditetapkan sistem Android/iOS Apple sebagai **"Malware Penghisap Baterai"** (Battery Draining App) karena membuat chipset HP meledak panas di dalam saku celana, dan akan seketika di-*Kill* pembunuhan paksa penutupan aplikasi oleh kernel keamanan sistem OS smartphone Anda!
```
[FASE 4: HARI 70 SUKSES]
```
