# Hari 48: Integrasi POST API - Registrasi Donor
## Mengirimkan Data Form Payload Pendaftaran Ke Server Backend Menggunakan Axios POST & Menangani Response Status

Selamat datang di Hari 48! Hari ini kita akan mempraktikkan proses pengiriman data nyata (*Write Transaction*) dari aplikasi mobile kita ke database server backend Express.js menggunakan perintah **Axios POST**.

Kita akan menyambungkan formulir pendaftaran relawan baru yang kita rancang menggunakan React Hook Form + Zod di Hari 46 agar mengirimkan paket data (*Payload*) secara fisik ke endpoint API `/relawan` dan menangani status respon sukses maupun gagal dari server.

---

## 1. Aliran Transaksi POST di Perangkat Mobile

```
[INPUT FORM HP] 
      | (Klik Tombol Simpan - Validasikan Zod)
      v
[AXIOS POST /relawan] ---- (Kirim JSON Payload) ----> [BACKEND API]
                                                           |
[NOTIFIKASI SUKSES HP] <--- (Kembalikan Status 201) <--- [DATABASE WRITE]
```

---

## 2. Studi Kasus PMI: Mengirim Pendaftaran Relawan Baru ke API

Mari kita buat file halaman pendaftaran anggota relawan aktif di direktori **`app/relawan/daftar.tsx`**:

```tsx
// app/relawan/daftar.tsx
import React, { useState } from 'react';
import { View, Text, TextInput, TouchableOpacity, Alert, ActivityIndicator } from 'react-native';
import { useForm, Controller } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { router } from 'expo-router';
import * as z from 'zod';
// Impor Axios Instance terpusat (Hari 44 yang otomatis menyisipkan token JWT)
import api from '../../utils/api';

const skemaDaftar = z.object({
  nama: z.string().min(3, { message: 'Nama minimal 3 huruf' }),
  golonganDarah: z.string().min(1, { message: 'Golongan darah wajib diisi' })
});

type FormDaftarData = z.infer<typeof skemaDaftar>;

export default function LayarPendaftaranDonor() {
  const [loading, setLoading] = useState(false);

  const { control, handleSubmit, formState: { errors } } = useForm<FormDaftarData>({
    resolver: zodResolver(skemaDaftar),
    defaultValues: { nama: '', golonganDarah: '' }
  });

  // FUNGSI UTAMA PENGIRIM DATA POST KE API BACKEND
  const kirimKeApi = async (payload: FormDaftarData) => {
    setLoading(true);
    try {
      console.log("[AxiosPOST] Mengirim payload ke database...", payload);
      
      // Tembak API backend menggunakan Axios POST
      const respon = await api.post('/relawan', payload);

      if (respon.status === 201 || respon.status === 200) {
        Alert.alert('✓ Registrasi Sukses', 'Relawan baru berhasil didaftarkan di database PMI Lampung.');
        router.replace('/(tabs)'); // Kembali ke dashboard
      }
    } catch (error: any) {
      console.error("[AxiosPOST Error]:", error);
      const pesanError = error.response?.data?.message || 'Gagal menyimpan data ke server.';
      Alert.alert('🚨 Pendaftaran Gagal', pesanError);
    } finally {
      setLoading(false);
    }
  };

  return (
    <View className="flex-1 bg-slate-50 p-6 justify-center">
      <View className="bg-white p-6 rounded-3xl border border-slate-100 shadow-sm space-y-4">
        <Text className="font-extrabold text-slate-800 text-sm">✍️ Form Relawan Baru</Text>

        {/* Input Nama */}
        <View className="space-y-1">
          <Text className="text-[10px] font-bold text-slate-400">NAMA LENGKAP</Text>
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
          {errors.nama && <Text className="text-[9px] text-red-500 font-bold">⚠️ {errors.nama.message}</Text>}
        </View>

        {/* Input Golongan Darah */}
        <View className="space-y-1">
          <Text className="text-[10px] font-bold text-slate-400">GOLONGAN DARAH (A/B/O/AB)</Text>
          <Controller
            control={control}
            name="golonganDarah"
            render={({ field: { onChange, value } }) => (
              <TextInput
                value={value}
                onChangeText={onChange}
                placeholder="Contoh: AB"
                className="w-full px-4 py-3 rounded-2xl bg-slate-50 border border-slate-200 text-xs"
              />
            )}
          />
          {errors.golonganDarah && <Text className="text-[9px] text-red-500 font-bold">⚠️ {errors.golonganDarah.message}</Text>}
        </View>

        {/* Tombol Simpan */}
        <TouchableOpacity 
          onPress={handleSubmit(kirimKeApi)}
          disabled={loading}
          className="w-full bg-red-500 py-3.5 rounded-2xl items-center flex-row justify-center space-x-2 disabled:bg-slate-200"
        >
          {loading && <ActivityIndicator size="small" color="#ffffff" />}
          <Text className="text-white font-extrabold text-xs uppercase">
            {loading ? 'Menyimpan...' : 'Simpan Data Relawan'}
          </Text>
        </TouchableOpacity>
      </View>
    </View>
  );
}
```,Description:
