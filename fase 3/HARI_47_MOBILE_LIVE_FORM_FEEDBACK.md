# Hari 47: Umpan Balik Formulir Live (Live Feedback)
## Mewarnai Border Input Menjadi Merah Menyala Saat Terjadi Error Validasi Serta Menghidup-Matikan Tombol Submit Secara Dinamis

Selamat datang di Hari 47! Hari ini kita akan mempelajari cara meningkatkan kualitas kegunaan formulir (*Usability*) dengan merancang **Umpan Balik Visual Secara Live** (Live Form Feedback).

Tampilan form yang statis dan kaku membingungkan pengguna. Saat terjadi kesalahan input, elemen teks peringatan saja tidak cukup menarik perhatian mata. Hari ini kita akan memodifikasi gaya input text agar **garis tepi border-nya otomatis berubah warna menjadi merah menyala** jika terjadi error, serta mematikan fungsi klik tombol simpan (*Disabled State*) selama form belum terisi dengan benar.

---

## 1. Konsep Pemantauan Status Reaktif Form

React Hook Form menyediakan properti **`isValid`** di dalam parameter `formState` yang bernilai boolean:
*   **`isValid`**: Bernilai `true` jika seluruh kolom form sudah lolos pemeriksaan Zod Schema. Kita bisa mengikat nilai ini ke properti `disabled` tombol: `disabled={!isValid}`.
*   **`errors.namaField`**: Jika bernilai truthy (ada error), kita mewarnai border input dengan kelas Tailwind merah `border-red-500`, dan jika aman diwarnai border abu-abu normal `border-slate-200`.

---

## 2. Studi Kasus PMI: Form Input Dengan Live Feedback Visual

Mari kita modifikasi komponen input formulir kita agar responsif secara dinamis:

```tsx
// components/FormFeedbackLive.tsx
import React from 'react';
import { View, Text, TextInput, TouchableOpacity } from 'react-native';
import { useForm, Controller } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import * as z from 'zod';

const skemaCepat = z.object({
  telepon: z.string().min(10, { message: 'Nomor telepon minimal 10 digit angka' })
});

type FormCepatData = z.infer<typeof skemaCepat>;

export default function FormFeedbackLive() {
  
  // Ambil properti isValid dari formState
  const { control, handleSubmit, formState: { errors, isValid } } = useForm<FormCepatData>({
    resolver: zodResolver(skemaCepat),
    mode: 'onChange', // PENTING: Memicu pemeriksaan validasi pada setiap ketukan huruf keyboard!
    defaultValues: { telepon: '' }
  });

  const kirimData = (data: FormCepatData) => {
    console.log("[LiveForm] Kirim data sukses:", data);
  };

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-200 space-y-4">
      <Text className="font-bold text-slate-800 text-sm">📞 Hubungi Koordinator Lapangan</Text>

      <View className="space-y-1.5">
        <Text className="text-[10px] font-bold text-slate-500">NOMOR TELEPON (MINIMAL 10 ANGKA)</Text>
        
        <Controller
          control={control}
          name="telepon"
          render={({ field: { onChange, value } }) => (
            <TextInput
              value={value}
              onChangeText={onChange}
              keyboardType="phone-pad"
              
              // WARNAI BORDER SECARA DINAMIS BERDASARKAN STATUS ERROR
              className={`w-full px-4 py-3 rounded-2xl bg-slate-50 border text-xs outline-none ${
                errors.telepon 
                  ? 'border-red-500 bg-red-50/20' // Merah menyala jika error
                  : 'border-slate-200 focus:border-slate-900' // Abu-abu normal jika aman
              }`}
            />
          )}
        />

        {errors.telepon && (
          <Text className="text-[9px] font-bold text-red-500 mt-1">⚠️ {errors.telepon.message}</Text>
        )}
      </View>

      {/* 
        TOMBOL DENGAN KONDISI DISABLE DINAMIS
        Jika form belum valid (!isValid), redupkan warna tombol & matikan interaksi tekan
      */}
      <TouchableOpacity 
        onPress={handleSubmit(kirimData)}
        disabled={!isValid} // Matikan klik jika form belum valid!
        className={`w-full py-3.5 rounded-2xl items-center mt-2 ${
          isValid 
            ? 'bg-red-500 active:bg-red-600' // Menyala merah jika valid
            : 'bg-slate-200 opacity-60' // Redup abu-abu jika belum valid
        }`}
      >
        <Text className={`font-extrabold text-xs uppercase tracking-wider ${
          isValid ? 'text-white' : 'text-slate-400'
        }`}>Hubungi Sekarang</Text>
      </TouchableOpacity>

    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa kegunaan dari parameter konfigurasi `mode: 'onChange'` pada pembuatan hook `useForm` di atas? Apa efeknya jika baris tersebut lupa kita setel?
2. Buka kode `FormFeedbackLive.tsx`. Jelaskan bagaimana cara kita merubah warna border TextInput secara dinamis memanfaatkan status `errors.telepon`.
3. Sebutkan properti boolean dari React Hook Form yang bertugas mencatat apakah tombol simpan sudah pernah ditekan oleh pengguna (*Submitted status*).

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Pengaruh Mode Validasi Reaktif (Validation Mode)
*   **Validasi Real-time**: Setelan `mode: 'onChange'` menginstruksikan library untuk melakukan evaluasi kecocokan data skema Zod **pada setiap perubahan satu karakter huruf yang diketikkan user**. 
*   **Perilaku default jika lupa**: Jika dibiarkan default (`mode: 'onSubmit'`), validasi **hanya akan berjalan sekali di akhir saat tombol ditekan**. 
*   Pengguna tidak akan tahu bahwa nomor yang mereka ketik kurang angka sampai mereka menekan tombol simpan, merusak kenyamanan umpan balik live.

### Jawaban 2: Evaluasi Ekspresi CSS Dinamis (Template Literals)
*   Kita menggunakan sistem evaluasi kondisi JavaScript di dalam string `className`. 
*   Jika properti `errors.telepon` berisi data kesalahan (bernilai truthy), kita menyuntikkan string kelas border merah `'border-red-500 bg-red-50/20'`. Jika kosong (bernilai falsy), kita mengembalikan ke kelas border normal `'border-slate-200'`.

### Jawaban 3: Properti isSubmitted
*   Properti tersebut bernama **`isSubmitted`** (atau **`isSubmitting`** jika kita ingin melacak status proses pengiriman API yang sedang berlangsung).
```
[FASE 3: HARI 47 SUKSES]
```
