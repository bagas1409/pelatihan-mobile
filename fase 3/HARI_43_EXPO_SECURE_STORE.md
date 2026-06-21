# Hari 43: Persistensi State Aman (SecureStore)
## Menyimpan Token JWT Secara Aman Lintas Restart Aplikasi Menggunakan Enkripsi Hardware OS expo-secure-store

Selamat datang di Hari 43! Hari ini kita akan mempelajari teknik **Persistensi State Aman** menggunakan modul **`expo-secure-store`**.

Di Fase 3 (Hari 44) kita menggunakan Cookie otomatis untuk menyimpan token di web. Di perangkat mobile handphone, kita tidak mengenal Cookie browser. Jika kita menyimpan token JWT di Zustand biasa, token tersebut akan **langsung terhapus dari RAM saat petugas menutup atau me-restart aplikasi HP-nya**. Petugas akan terpaksa mengetik ulang password login setiap kali membuka aplikasi. 

Kita akan mengonfigurasi penyimpanan lokal terenkripsi di tingkat chip hardware smartphone agar token login awet tersimpan meskipun HP mati.

---

## 1. Apa itu Expo SecureStore?

*   **Keamanan Level OS**: `expo-secure-store` menyimpan data bertipe string berukuran kecil (maksimal 2KB) langsung ke dalam memori penyimpanan terenkripsi hardware handphone: **KeyStore** (pada Android) atau **Keychain** (pada iOS Apple).
*   Data yang disimpan di sini dilindungi oleh sandi enkripsi bawaan HP, sehingga aman dari ancaman pencurian data oleh aplikasi nakal lain yang terpasang di HP yang sama.

---

## 2. Instalasi Library SecureStore

Buka terminal proyek Anda dan jalankan perintah:
```bash
npx expo install expo-secure-store
```

---

## 3. Studi Kasus PMI: Helper Simpan & Baca Token Terenkripsi (`utils/storage.ts`)

Buat berkas helper di direktori **`utils/storage.ts`** untuk memproses simpan dan ambil token:

```typescript
// utils/storage.ts
import * as SecureStore from 'expo-secure-store';

const KEY_TOKEN = 'donorku_jwt_token';

export const storageHelper = {
  // 1. SIMPAN TOKEN (Asinkron)
  simpanToken: async (token: string): Promise<void> => {
    try {
      await SecureStore.setItemAsync(KEY_TOKEN, token);
      console.log("[SecureStore] Token sukses dienkripsi & disimpan.");
    } catch (error) {
      console.error("[SecureStore Save Error]:", error);
    }
  },

  // 2. AMBIL TOKEN
  ambilToken: async (): Promise<string | null> => {
    try {
      const token = await SecureStore.getItemAsync(KEY_TOKEN);
      return token;
    } catch (error) {
      console.error("[SecureStore Load Error]:", error);
      return null;
    }
  },

  // 3. HAPUS TOKEN (Saat Petugas Logout)
  hapusToken: async (): Promise<void> => {
    try {
      await SecureStore.deleteItemAsync(KEY_TOKEN);
      console.log("[SecureStore] Token berhasil dibakar bersih.");
    } catch (error) {
      console.error("[SecureStore Delete Error]:", error);
    }
  }
};
```

---

## 4. Latihan Soal Mandiri
1. Apa perbedaan keamanan mendasar antara menyimpan token JWT menggunakan `AsyncStorage` biasa dengan menggunakan `expo-secure-store` pada perangkat mobile?
2. Buka kode `storageHelper.simpanToken` di atas. Sebutkan nama metode asinkron dari library `SecureStore` yang bertugas mengunci dan menyimpan berkas string token.
3. Mengapa kapasitas penyimpanan `SecureStore` dibatasi maksimal hanya sebesar 2 Kilobyte saja? Jelaskan dari sisi peruntukannya.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Perbedaan Tingkat Enkripsi
*   **`AsyncStorage`**: Menyimpan data berupa teks polos (*Plain-text*) di database SQLite lokal HP. Siapa saja yang berhasil menghubungkan HP ke komputer debug (USB Debugging) bisa menyalin database tersebut dan membaca token JWT Anda secara polos tanpa sandi.
*   **`SecureStore`**: Mengenkripsi data sebelum ditulis ke disk menggunakan algoritma AES-256 bawaan hardware chip HP. Hanya aplikasi bersangkutan yang memiliki kunci dekripsi fisik, menjamin kerahasiaan token dari ancaman peretasan database lokal.

### Jawaban 2: Metode Set Item
*   Metode tersebut bernama **`setItemAsync(key, value)`**.

### Jawaban 3: Kecepatan Dekripsi Hardware
*   **Alokasi Khusus**: Chip enkripsi perangkat mobile dirancang khusus untuk memproses data keamanan vital berukuran mikro (seperti token akses, pin login, atau password bank). 
*   Membatasi ukuran maksimal sebesar 2KB menjamin proses enkripsi dan dekripsi data berjalan instan di bawah 5 milidetik, mencegah penurunan performa aplikasi saat melakukan verifikasi kunci di awal booting.
```
[FASE 3: HARI 43 SUKSES]
```
