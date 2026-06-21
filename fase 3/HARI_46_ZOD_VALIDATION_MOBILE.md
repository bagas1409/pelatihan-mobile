# Hari 46: Validasi Skema Zod
## Mengintegrasikan Validasi Keamanan Zod Schema Dengan Resolver React Hook Form Lintas Tipe Data

Selamat datang di Hari 46! Hari ini kita akan mempelajari cara mengawinkan **Zod Schema** dengan **React Hook Form** menggunakan library resolver khusus.

Di web (Fase 3 Hari 46), kita menggunakan Zod untuk menyusun skema syarat data yang valid (seperti NIK KTP wajib berangka 16 huruf, email wajib berformat surat, dll). Di mobile development, ancaman data korup akibat petugas salah ketik jauh lebih tinggi karena sempitnya tombol keyboard virtual HP. Kita wajib menyaring data input tersebut di gerbang awal menggunakan Zod sebelum payload dikirimkan ke server Express.js.

---

## 1. Instalasi Library Resolver & Zod

Jalankan perintah berikut di terminal proyek Anda:
```bash
npm install zod @hookform/resolvers
```

---

## 2. Studi Kasus PMI: Skema Validasi Anggota Relawan Baru

Kita akan membuat skema validasi relawan baru dengan ketentuan:
1.  **Nama Lengkap**: Minimal 3 huruf, maksimal 50 huruf.
2.  **Nomor NIK KTP**: Wajib berisi persis 16 digit karakter angka.
3.  **Email**: Wajib berformat alamat email resmi yang valid.

Mari kita tulis kode schema Zod-nya di berkas **`components/FormRelawanZod.tsx`**:

```tsx
// components/FormRelawanZod.tsx
import React from 'react';
import { View, Text, TextInput, TouchableOpacity } from 'react-native';
import { useForm, Controller } from 'react-hook-form';
// Impor Zod & Resolver
import { zodResolver } from '@hookform/resolvers/zod';
import * as z from 'zod';

// 1. STRUKTUR SKEMA VALIDASI ZOD (Zod Schema)
const skemaRelawan = z.object({
  nama: z.string()
    .min(3, { message: 'Nama minimal terdiri dari 3 karakter' })
    .max(50, { message: 'Nama maksimal 50 karakter' }),
  
  nik: z.string()
    .length(16, { message: 'Nomor NIK KTP wajib berisi persis 16 angka' })
    .regex(/^[0-9]+$/, { message: 'NIK hanya boleh diisi karakter angka saja' }),
  
  email: z.string()
    .email({ message: 'Format alamat email dinas tidak valid' })
});

// Inferensi tipe data otomatis dari skema Zod
type FormRelawanData = z.infer<typeof skemaRelawan>;

export default function FormRelawanZod() {
  
  // 2. HUBUNGKAN SKEMA ZOD KE RESOLVER USEFORM
  const { control, handleSubmit, formState: { errors } } = useForm<FormRelawanData>({
    resolver: zodResolver(skemaRelawan), // Masukkan resolver zod!
    defaultValues: {
      nama: '',
      nik: '',
      email: ''
    }
  });

  const simpanRelawan = (data: FormRelawanData) => {
    console.log("[ZodSuccess] Data aman siap dikirim ke database:", data);
  };

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-200 space-y-4">
      <Text className="font-bold text-slate-800 text-sm">✍️ Registrasi Relawan (Zod Protection)</Text>

      {/* Input Nama */}
      <View className="space-y-1">
        <Text className="text-[10px] font-bold text-slate-500">NAMA LENGKAP</Text>
        <Controller
          control={control}
          name="nama"
          render={({ field: { onChange, value } }) => (
            <TextInput
              value={value}
              onChangeText={onChange}
              className="w-full px-4 py-3 rounded-2xl bg-slate-50 border border-slate-200 text-xs"
            />
          )}
        />
        {/* TAMPILKAN PESAN ERROR JIKA VALIDASI NAMA GAGAL */}
        {errors.nama && (
          <Text className="text-[9px] font-bold text-red-500 mt-1">⚠️ {errors.nama.message}</Text>
        )}
      </View>

      {/* Input NIK KTP */}
      <View className="space-y-1">
        <Text className="text-[10px] font-bold text-slate-500">NIK KTP (16 DIGIT)</Text>
        <Controller
          control={control}
          name="nik"
          render={({ field: { onChange, value } }) => (
            <TextInput
              value={value}
              onChangeText={onChange}
              keyboardType="numeric"
              maxLength={16}
              className="w-full px-4 py-3 rounded-2xl bg-slate-50 border border-slate-200 text-xs"
            />
          )}
        />
        {errors.nik && (
          <Text className="text-[9px] font-bold text-red-500 mt-1">⚠️ {errors.nik.message}</Text>
        )}
      </View>

      {/* Tombol Kirim */}
      <TouchableOpacity 
        onPress={handleSubmit(simpanRelawan)}
        className="w-full bg-slate-900 py-3.5 rounded-2xl items-center mt-2"
      >
        <Text className="text-white font-extrabold text-xs uppercase">Simpan Anggota</Text>
      </TouchableOpacity>
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari pemanggilan helper `zodResolver(skemaRelawan)` di dalam inisialisasi `useForm` di atas?
2. Buka kode `skemaRelawan` untuk properti `nik`. Jelaskan kegunaan dari validasi regex `.regex(/^[0-9]+$/, ...)` tersebut.
3. Sebutkan nama properti dari objek `formState` yang dikembalikan `useForm` yang merekam seluruh daftar pesan error kesalahan ketik input.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Pengawinan Validasi (Validation Middleware)
*   **Penerjemah Skema**: Pustaka `@hookform/resolvers` bertindak sebagai adaptor khusus. 
*   Dia bertugas menerjemahkan aturan skema kepatuhan data Zod agar bisa dikomunikasikan secara mulus ke dalam parameter internal React Hook Form. 
*   Ketika petugas menekan tombol simpan, resolver secara otomatis menjalankan mesin penguji Zod dan menghentikan alur jika ditemukan kolom isian yang melanggar hukum skema.

### Jawaban 2: Proteksi Karakter Non-Numerik
*   **Penyaring Teks Palsu**: Meskipun kita menyetel `keyboardType="numeric"`, sistem Android yang dimodifikasi (Custom ROM) atau keyboard eksternal (Bluetooth Keyboard) terkadang **tetap bisa menyuntikkan karakter huruf abjad** (seperti `'e'` atau `','`) ke dalam kolom input. 
*   Validasi regex memastikan data yang lolos masuk ke database PMI benar-benar murni berisikan karakter angka `0` s.d `9` tanpa ada selipan simbol lain.

### Jawaban 3: Objek errors
*   Objek tersebut bernama **`errors`** (yang diperoleh dari pemilahan destructing `formState: { errors }`).
```
[FASE 3: HARI 46 SUKSES]
```
