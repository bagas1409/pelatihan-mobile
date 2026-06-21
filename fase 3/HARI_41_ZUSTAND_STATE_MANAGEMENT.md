# Hari 41: State Management Global dengan Zustand
## Mengenal Zustand Sebagai Pengganti Pinia Yang Super Ringan, Cepat, Dan Mudah Diintegrasikan Pada Ekosistem React Native

Selamat datang di Hari 41! Hari ini kita akan mempelajari teknik pengelolaan **State Global** menggunakan **Zustand**.

Di Fase 3 (Hari 42) kita menggunakan Pinia untuk menyimpan data login user dinas di web Nuxt 3. Di ekosistem React/React Native, kita tidak menggunakan Pinia. Sebagai gantinya, kita menggunakan **Zustand**, sebuah pustaka state management global modern yang sangat dicintai oleh developer karena ukurannya yang super kecil (hanya beberapa kilobyte), tidak membutuhkan boilerplate Redux yang rumit, dan tidak memerlukan pembungkus context provider di layout root.

---

## 1. Instalasi Zustand

Buka terminal proyek Anda dan jalankan perintah berikut:
```bash
npm install zustand
```

---

## 2. Mengapa Memilih Zustand Dibanding Redux/Context?

| Aspek | Redux / Redux Toolkit | React Context API | Zustand |
| :--- | :--- | :--- | :--- |
| **Boilerplate** | Sangat Panjang (Actions, Reducers, Types). | Sedang (Provider, Consumer). | Sangat Pendek (Hanya 1 baris store). |
| **Rerender Bug** | Terkontrol ketat. | Rawan rerender masif di seluruh anak komponen. | Sangat Efisien (Hanya komponen yang memanggil yang di-rerender). |
| **Setup Provider** | Wajib di root `App.tsx`. | Wajib di root `App.tsx`. | **Bebas Tanpa Provider** (Tinggal import langsung pakai). |

---

## 3. Studi Kasus PMI: Menyusun Store Sederhana Logistik

Mari kita buat store global pertama kita untuk mencatat total kantong darah yang siap disalurkan ke mobil box distribusi di berkas **`store/useLogistikStore.ts`**:

```typescript
// store/useLogistikStore.ts
import { create } from 'zustand';

// 1. DEFINISIKAN INTERFACE TIPE DATA STATE
interface LogistikState {
  jumlahKantongSiap: number;
  namaPetugasAktif: string;
  tambahKantong: () => void;
  setPetugas: (nama: string) => void;
  resetLogistik: () => void;
}

// 2. CIPTAKAN STORE GLOBAL MENGGUNAKAN CREATE
const useLogistikStore = create<LogistikState>((set) => ({
  // State data awal
  jumlahKantongSiap: 0,
  namaPetugasAktif: 'Belum Login',

  // Actions pengubah state (setara mutations/actions di Pinia)
  tambahKantong: () => set((state) => ({ jumlahKantongSiap: state.jumlahKantongSiap + 1 })),
  setPetugas: (nama) => set({ namaPetugasAktif: nama }),
  resetLogistik: () => set({ jumlahKantongSiap: 0 })
}));

export default useLogistikStore;
```

---

## 4. Latihan Soal Mandiri
1. Apa fungsi dari parameter `set` pada pembuatan store Zustand di atas?
2. Jelaskan mengapa Zustand tidak memerlukan pembungkus `<Provider>` di root file `_layout.tsx` untuk menyalurkan datanya, tidak seperti Redux atau Context API.
3. Sebutkan nama fungsi action yang bertugas mengubah nama petugas aktif di dalam store `useLogistikStore` di atas.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Fungsi Merger State (set)
*   **Pengubah State**: Fungsi `set` di Zustand adalah helper bawaan yang bertugas menggabungkan (*Shallow Merge*) nilai state baru yang kita inputkan dengan state lama yang ada di memori. 
*   Dia otomatis memicu render ulang (*Re-render*) pada seluruh komponen visual luar yang saat itu sedang mengimpor variabel yang bersangkutan.

### Jawaban 2: Konsep Hooks-based Store (Tanpa Provider)
*   **Akses Langsung**: Zustand didesain berbasis React Hooks murni. 
*   Saat kita mengeksekusi `create()`, Zustand membuat objek store terisolasi di memori RAM dan mengembalikan hook React kustom. 
*   Komponen visual tinggal mengimpor hook tersebut layaknya fungsi JavaScript biasa. Karena tidak menggunakan Context API di balik layarnya, kita tidak perlu repot menyelimuti root file `src/app/_layout.tsx (atau app/_layout.tsx)` menggunakan komponen Provider, menghemat kebersihan struktur file root proyek.

### Jawaban 3: Nama Action Pengubah
*   Fungsi tersebut bernama **`setPetugas`**.
```
[FASE 3: HARI 41 SUKSES]
```
