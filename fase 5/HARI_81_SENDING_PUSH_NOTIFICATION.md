# Hari 81: Mengirim Push Notification dari Backend
## Memasang Logika Pengirim Notifikasi Berbasis Node.JS Di Server Backend Express Untuk Menembakkan Alarm Panggilan CITO

Selamat datang di Hari 81! Kemarin kita berhasil menarik **Expo Push Token** dari aplikasi mobile. Hari ini kita akan berpindah tempat sejenak ke sisi **Server Backend Express.js**.

Kita akan merancang logika pembuat tembakan notifikasi di server Express.js. Saat admin PMI memasukkan data stok darah kritis CITO baru di database backend, script Node.js server kita akan memanggil server antrean milik Expo untuk menembakkan notifikasi alarm ke handphone petugas di lapangan.

---

## 1. Pustaka Pendukung Server Node.js: `expo-server-sdk`

Tim Expo menyediakan pustaka pembantu di sisi server Node.js agar kita tidak perlu merakit HTTP request mentah secara manual.
Instalasi di folder server backend Express.js Anda:
```bash
npm install expo-server-sdk
```

---

## 2. Studi Kasus PMI: Script Pemanggil Relawan Darurat di Express.js (`controllers/notif.controller.ts`)

Mari kita rancang fungsi pengirim notifikasi massal di file controller Express.js server kita:

```typescript
// pmi-backend/src/controllers/notif.controller.ts
import { Request, Response } from 'express';
// Impor SDK resmi Expo Server
import { Expo } from 'expo-server-sdk';

// Inisialisasi client server Expo
const expoInstance = new Expo();

export const kirimAlarmCitoDarah = async (req: Request, res: Response) => {
  try {
    // 1. Dapatkan daftar Token HP relawan dari database (Simulasi penarikan tabel user_tokens)
    const tokenRelawanPMI = [
      'ExponentPushToken[xxxxxxxxxxxx_device1]',
      'ExponentPushToken[yyyyyyyyyyyy_device2]'
    ];

    // Wadah penampung pesan
    const daftarPesan: any[] = [];

    for (const token of tokenRelawanPMI) {
      // 2. CEK APAKAH TOKEN VALID FORMATNYA?
      if (!Expo.isExpoPushToken(token)) {
        console.error(`Token tidak valid format: ${token}`);
        continue;
      }

      // 3. SUSUN RENCANA PAYLOAD PESAN NOTIFIKASI
      daftarPesan.push({
        to: token, // Target HP Penerima
        sound: 'default', // Nyalakan dering bawaan HP
        title: '🚨 PANGGILAN CITO DARURAT',
        body: 'Dibutuhkan 5 Kantong darah O Negatif segera di RSU Abdul Moeloek!',
        data: { ruteTujuan: 'stok/detail/30' }, // Data rahasia kargo tambahan untuk diolah HP (Hari 82)
      });
    }

    // 4. TEMBAKKAN MASSAL KE SERVER EXPO
    // Kirim pesan secara batch (kelompok) agar hemat lalu lintas data server
    const chunks = expoInstance.chunkPushNotifications(daftarPesan);
    
    for (const chunk of chunks) {
      const ticketChunk = await expoInstance.sendPushNotificationsAsync(chunk);
      console.log("[Express Backend] Notifikasi terkirim sukses:", ticketChunk);
    }

    res.status(200).json({ success: true, message: 'Alarm notifikasi CITO berhasil ditembakkan!' });

  } catch (error: any) {
    console.error("Gagal mengirim notif:", error);
    res.status(500).json({ success: false, message: error.message });
  }
};
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi dari pemanggilan metode validasi `Expo.isExpoPushToken(token)` di sisi server Express.js di atas?
2. Buka kode payload pengiriman notifikasi. Apa fungsi dari properti `data: { ruteTujuan: 'stok/detail/30' }` yang diselipkan di dalam body pesan?
3. Mengapa kita memotong-motong daftar pesan menggunakan fungsi `expoInstance.chunkPushNotifications(daftarPesan)` sebelum melepaskannya ke internet?

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Filter Format Token (Format Safeguard)
*   **Keamanan Eksekusi**: API server Expo hanya menerima format token berawalan `ExponentPushToken[...]`. 
*   Jika server database kita menyimpan data sampah (contoh: string kosong atau token FCM mentah), menembakkannya langsung ke Expo akan memicu error kegagalan request total. Pengecekan ini menyaring dan membuang token kotor secara lokal sebelum Axios menembak server Expo.

### Jawaban 2: Kargo Data Latar Belakang (Silent Data Payload)
*   **Navigasi Otomatis**: Properti `data` berisi payload JSON rahasia yang tidak nampak secara visual di banner notifikasi HP relawan. 
*   Data ini akan dibaca oleh program Mobile App saat notifikasi tersebut disentuh (diklik) untuk mengarahkan layar HP langsung melompat menuju rute detail stok darah pasien kritis yang bersangkutan tanpa membuka dashboard halaman utama (Hari 82).

### Jawaban 3: Menghindari Batasan Payload HTTP (Batching Request Limit)
*   Jika relawan PMI berjumlah 10.000 orang, menembak 10.000 request HTTP tunggal satu-per-satu ke server Expo akan membuat server Express Anda hang/terkunci kehabisan memori port. 
*   Metode `chunkPushNotifications` secara pintar mengelompokkan 10.000 data tersebut menjadi potongan kecil (misal per 100 pesan per satu tembakan request HTTP), meminimalkan waktu tunggu server backend Anda.
```
[FASE 5: HARI 81 SUKSES]
```
