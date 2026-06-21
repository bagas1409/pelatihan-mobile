# Hari 45: Pengamanan Input dengan React Hook Form
## Merancang Formulir Input Logistik Menggunakan React Hook Form Untuk Menghemat Pembuatan State Input Berulang

Selamat datang di Hari 45! Hari ini kita akan mempelajari library pengelola formulir terbaik di ekosistem mobile React: **`React Hook Form`**.

Di Hari 8 kita membuat form menggunakan state lokal `useState` manual untuk masing-masing TextInput. Menulis state satu persatu untuk form pendaftaran relawan yang memiliki 10 kolom input (seperti Nama, NIK, Alamat, Golongan Darah, No HP, dst) sangatlah membosankan dan memicu penurunan performa karena HP **akan me-render ulang seluruh layar pada setiap ketukan satu tombol huruf keyboard** (*Unnecessary Re-renders*). 

Hari ini kita akan mengintegrasikan React Hook Form yang mengelola state input secara terpusat tanpa memicu rerender layar yang lambat.

---

## 1. Instalasi React Hook Form

Jalankan perintah berikut di terminal proyek Anda:
```bash
npm install react-native-select-dropdown react-hook-form
```

---

## 2. Mengenal Komponen `<Controller>`

React Hook Form membutuhkan pembungkus khusus bernama **`<Controller>`** agar bisa memantau pergerakan nilai data di dalam komponen custom TextInput React Native (karena React Native tidak memiliki elemen input HTML `<input>` bawaan).

Tiga properti wajib di dalam Controller:
1.  **`control`**: Objek pusat pengawas form yang diperoleh dari hook `useForm()`.
2.  **`name`**: String nama kunci pengenal data di database (misal: `"namaRelawan"`).
3.  **`render`**: Fungsi visual yang bertugas menggambar komponen `TextInput` asli dan menyambungkan event `onChange` serta `value` reaktifnya.

---

## 3. Studi Kasus PMI: Form Pengiriman CITO dengan React Hook Form

Mari kita buat formulir pengiriman logistik PMI di berkas **`components/FormCitoHook.tsx`**:

```tsx
// components/FormCitoHook.tsx
import React from 'react';
import { View, Text, TextInput, TouchableOpacity } from 'react-native';
// Impor useForm & Controller
import { useForm, Controller } from 'react-hook-form';

// Tipe data isian form
interface FormCitoData {
  namaRs: string;
  jumlahKantong: string;
}

export default function FormCitoHook() {
  
  // 1. SETUP DEKLARASI USEFORM
  const { control, handleSubmit, reset } = useForm<FormCitoData>({
    defaultValues: {
      namaRs: '',
      jumlahKantong: ''
    }
  });

  const kirimFormulir = (data: FormCitoData) => {
    console.log("[ReactHookForm] Payload Terkumpul:", data);
    reset(); // Kosongkan form kembali setelah sukses
  };

  return (
    <View className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4">
      <Text className="font-bold text-slate-800 text-sm">🚨 Pemicu Darurat CITO</Text>

      <!-- 2. KONTROL INPUT RUMAH SAKIT -->
      <View className="space-y-1.5">
        <Text className="text-[10px] font-bold text-slate-500 uppercase">Nama Rumah Sakit</Text>
        <Controller
          control={control}
          name="namaRs"
          render={({ field: { onChange, value } }) => (
            <TextInput
              value={value}
              onChangeText={onChange} // Sambungkan handler ketikan ke Controller
              placeholder="RSU Abdul Moeloek..."
              className="w-full px-4 py-3 rounded-2xl bg-slate-50 border border-slate-200 text-xs"
              placeholderTextColor="#94a3b8"
            />
          )}
        />
      </View>

      <!-- 3. KONTROL INPUT JUMLAH KANTONG -->
      <View className="space-y-1.5">
        <Text className="text-[10px] font-bold text-slate-500 uppercase">Jumlah Bag Darah</Text>
        <Controller
          control={control}
          name="jumlahKantong"
          render={({ field: { onChange, value } }) => (
            <TextInput
              value={value}
              onChangeText={onChange}
              placeholder="Contoh: 5"
              keyboardType="numeric"
              className="w-full px-4 py-3 rounded-2xl bg-slate-50 border border-slate-200 text-xs"
              placeholderTextColor="#94a3b8"
            />
          )}
        />
      </View>

      <!-- 4. TOMBOL SIMPAN (Menggunakan handleSubmit helper) -->
      <TouchableOpacity 
        onPress={handleSubmit(kirimFormulir)}
        className="w-full bg-red-500 py-3.5 rounded-2xl items-center"
      >
        <Text className="text-white font-extrabold text-xs uppercase tracking-wider">Kirim Panggilan CITO</Text>
      </TouchableOpacity>

    </View>
  );
}
```

---

## 4. Latihan Soal Mandiri
1. Apa fungsi utama dari komponen `<Controller>` pada integrasi React Hook Form di React Native? Mengapa kita tidak bisa langsung mengikat properti register (seperti `register('namaRs')` pada React Web) pada TextInput?
2. Buka kode `onPress={handleSubmit(kirimFormulir)}` di atas. Jelaskan fungsi dari helper `handleSubmit` tersebut.
3. Sebutkan kelebihan penggunaan React Hook Form dibandingkan menggunakan `useState` manual dari segi penghematan render ulang (*Performance Optimization*).

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Perantara Custom Component (Input Wrapper)
*   **Tanpa DOM API**: Di lingkungan browser web, React Hook Form bisa langsung mencengkeram elemen HTML `<input ref={register} />` menggunakan DOM API. 
*   Di React Native, komponen `TextInput` adalah komponen native abstrak yang **tidak memiliki elemen DOM**. Komponen `<Controller>` bertindak sebagai jembatan yang secara manual menerima value dan meneruskan callback `onChangeText` agar data tersinkronisasi ke dalam memori internal library.

### Jawaban 2: Penanganan Data Submit (Form Wrapper)
*   **Penyaring & Kolektor**: Helper `handleSubmit` menghentikan aksi default, mengumpulkan seluruh nilai input dari memori rahasia, membungkusnya menjadi satu objek utuh sesuai type schema, dan hanya menyerahkan data tersebut ke fungsi `kirimFormulir` jika tidak ditemukan kesalahan validasi.

### Jawaban 3: Uncontrolled Inputs (Performa Tinggi)
*   **Bebas Lag Keyboard**: React Hook Form menggunakan sistem input tidak terkontrol (*Uncontrolled inputs*). 
*   Saat petugas mengetik huruf di keyboard HP, library mencatat huruf tersebut secara diam-diam di background memori RAM **tanpa me-render ulang seluruh visual layar**. Layar hanya di-render ulang jika status validasi error berubah, menjamin respon keyboard tetap instan tanpa tersendat di HP spesifikasi rendah.
```
[FASE 3: HARI 45 SUKSES]
```
