# Hari 42: Membuat Auth Store Dengan Zustand
## Merancang Mekanisme Simpan Session Petugas UDD Dan Token JWT Secara Global Lintas Rute Halaman

Selamat datang di Hari 42! Hari ini kita akan merancang **Auth Store** global menggunakan Zustand.

Di aplikasi DonorKu PMI Lampung, status login petugas (seperti nama lengkap, ID divisi, email dinas) dan token JWT yang diberikan oleh server backend setelah login sukses harus direkam di satu lokasi terpusat. Data token JWT ini sangat krusial karena wajib disisipkan di setiap transaksi kirim data CITO maupun input relawan. Hari ini kita akan merancang store global yang mengelola alur login, logout, dan pemantauan token tersebut.

---

## 1. Desain Tipe Data Auth Store (`types/auth.ts`)

Mari kita buat berkas tipe data khusus otentikasi di direktori **`types/auth.ts`**:

```typescript
// types/auth.ts
export interface UserPetugas {
  id: string;
  nama: string;
  email: string;
  role: 'Admin' | 'PetugasLapangan';
}

export interface AuthState {
  user: UserPetugas | null;
  token: string | null;
  isAuthenticated: boolean;
  loginPetugas: (dataUser: UserPetugas, tokenJwt: string) => void;
  logoutPetugas: () => void;
}
```

---

## 2. Kode Pembuatan Auth Store (`store/useAuthStore.ts`)

Buat file store di direktori **`store/useAuthStore.ts`**:

```typescript
// store/useAuthStore.ts
import { create } from 'zustand';
import { AuthState } from '../types/auth';

const useAuthStore = create<AuthState>((set) => ({
  // 1. STATE DEFAULTS
  user: null,
  token: null,
  isAuthenticated: false,

  // 2. ACTIONS 1: MENANGANI LOGIN SUKSES
  loginPetugas: (dataUser, tokenJwt) => set({
    user: dataUser,
    token: tokenJwt,
    isAuthenticated: true
  }),

  // 3. ACTIONS 2: LOGOUT / MEMBERSIHKAN SESSION
  logoutPetugas: () => set({
    user: null,
    token: null,
    isAuthenticated: false
  })
}));

export default useAuthStore;
```

---

## 3. Cara Mengonsumsi Data Auth Store di Halaman Profil

Buka file halaman profil Anda di **`app/(tabs)/profil.tsx`** dan panggil variabel user dari store Zustand secara reaktif:

```tsx
// app/(tabs)/profil.tsx
import React from 'react';
import { View, Text, TouchableOpacity } from 'react-native';
import { router } from 'expo-router';
// Impor auth store global
import useAuthStore from '../../store/useAuthStore';

export default function LayarProfilDinas() {
  // Ambil state user dan action logout dari Zustand
  const { user, logoutPetugas } = useAuthStore();

  const handleKeluar = () => {
    logoutPetugas(); // Bersihkan memori JWT di Zustand!
    
    // Arahkan paksa petugas kembali ke layar login awal
    router.replace('/login'); 
  };

  return (
    <View className="flex-1 bg-slate-50 p-6 justify-center">
      <View className="bg-white p-6 rounded-3xl border border-slate-100 shadow-sm space-y-6 items-center">
        
        <View className="w-16 h-16 rounded-full bg-slate-100 items-center justify-center">
          <Text className="text-xl">👤</Text>
        </View>

        {/* Tampilkan Nama & Role Petugas secara dinamis */}
        <View className="items-center space-y-1">
          <Text className="font-extrabold text-slate-800 text-sm">
            {user?.nama || 'Petugas Tamu'}
          </Text>
          <Text className="text-[10px] font-bold text-slate-400 uppercase tracking-widest">
            ID Divisi: {user?.role || 'Guest'}
          </Text>
        </View>

        {/* Tombol Logout */}
        <TouchableOpacity 
          onPress={handleKeluar}
          className="w-full bg-red-500 py-3 rounded-2xl items-center"
        >
          <Text className="text-white font-bold text-xs uppercase tracking-wider">Keluar Akun</Text>
        </TouchableOpacity>

      </View>
    </View>
  );
}
```

---

## 4. Latihan Soal Mandiri
1. Apa kegunaan dari variabel boolean `isAuthenticated` di dalam store Zustand di atas?
2. Buka kode `handleKeluar` di atas. Jelaskan mengapa kita menggunakan perintah `router.replace('/login')` setelah logout, bukan `router.push('/login')`.
3. Sebutkan nama 3 data user petugas yang dicatat di dalam interface `UserPetugas` di atas.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Indikator Proteksi Rute (Route Guard Flag)
*   **Penyaring Akses**: Variabel `isAuthenticated` bertindak sebagai bendera penentu. 
*   Jika nilainya `false`, sistem navigasi di file layout root `src/app/_layout.tsx (atau app/_layout.tsx)` akan langsung mencegat pemuatan halaman dan melempar balik browser/aplikasi ke layar login karena user belum memiliki otorisasi token JWT yang valid.

### Jawaban 2: Membakar Riwayat Halaman
*   **Keamanan Ganda**: Menggunakan `router.replace` menjamin riwayat tumpukan halaman dashboard petugas **dihapus secara bersih dari memori stack**. 
*   Jika kita memakai `push`, petugas yang sudah logout tetap bisa mengklik tombol back fisik HP Android-nya untuk menyelinap masuk kembali melihat layar dashboard tanpa harus memasukkan password kembali, yang merupakan celah kebocoran keamanan yang fatal.

### Jawaban 3: Struktur Data Petugas
*   Tiga data tersebut adalah **`id`**, **`nama`**, dan **`email`**.
```
[FASE 3: HARI 42 SUKSES]
```
