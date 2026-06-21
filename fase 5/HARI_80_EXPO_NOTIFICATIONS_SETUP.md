# Hari 80: Konfigurasi expo-notifications
## Memasang Library expo-notifications Serta Menarik String Push Token Perangkat HP Untuk Dikirim Ke Server Database Backend

Selamat datang di Hari 80! Hari ini kita akan mempraktikkan cara setup **`expo-notifications`** guna mendapatkan string **Expo Push Token** dari perangkat HP petugas/relawan.

Token unik yang diperoleh hari ini harus segera ditransfer menuju server backend Express.js kita agar server bisa mencatat alamat HP tersebut ke kolom database relawan PMI.

---

## 1. Pemasangan Pustaka Notifications

Jalankan perintah berikut di terminal Anda:
```bash
npx expo install expo-notifications expo-device
```
*(Modul `expo-device` diperlukan untuk memastikan program tidak meledak crash jika dijalankan di emulator PC yang ketiadaan modul hardware SIM Card/Satelit notifikasi).*

---

## 2. Studi Kasus PMI: Mengambil Token Perangkat (`utils/notifications.ts`)

Mari kita buat berkas asisten pengambil token di direktori **`utils/notifications.ts`**:

```typescript
// utils/notifications.ts
import * as Notifications from 'expo-notifications';
import * as Device from 'expo-device';
import { Platform } from 'react-native';

export async function ambilPushTokenPetugas(): Promise<string | null> {
  // 1. CEK APAKAH INI PERANGKAT HP FISIK SUNGGUHAN?
  if (!Device.isDevice) {
    console.log("[Notification] Token tidak dibuat karena ini Emulator PC!");
    return null;
  }

  try {
    // 2. CEK STATUS IZIN NOTIFIKASI OS (Hari 57)
    const { status: statusLama } = await Notifications.getPermissionsAsync();
    let statusFinal = statusLama;

    // Jika belum pernah ditanya, mintalah izin sekarang!
    if (statusLama !== 'granted') {
      const { status } = await Notifications.requestPermissionsAsync();
      statusFinal = status;
    }

    if (statusFinal !== 'granted') {
      console.log("[Notification] Petugas menolak izin notifikasi.");
      return null;
    }

    // 3. TARIK TOKEN UNIK EXPO PUSH SERVICE
    // Dapatkan token resmi. projectId didapatkan dari dashboard expo dev (Hari 83)
    const tokenObjek = await Notifications.getExpoPushTokenAsync({
      projectId: 'id-project-expo-anda-nanti' 
    });

    console.log("[Notification] Token Berhasil Terbit:", tokenObjek.data);

    // Kustomisasi khusus Android agar notifikasi muncul bersuara nyaring di laci HP
    if (Platform.OS === 'android') {
      Notifications.setNotificationChannelAsync('default', {
        name: 'default',
        importance: Notifications.AndroidImportance.MAX,
        vibrationPattern: [0, 250, 250, 250],
        lightColor: '#ef4444',
      });
    }

    return tokenObjek.data; // Mengembalikan string token

  } catch (error) {
    console.error("[Notification Setup Error]:", error);
    return null;
  }
}
```

---

## 3. Latihan Soal Mandiri
1. Mengapa kita memanggil validasi checking `Device.isDevice` sebelum meminta token notifikasi? Apa efek buruknya jika kode ini dijalankan di Emulator Android Studio PC biasa?
2. Buka kode pemanggilan izin `Notifications.requestPermissionsAsync()`. Jelaskan alur penanganan jika petugas dengan sengaja menolak izin popup notifikasi tersebut.
3. Sebutkan nama properti konfigurasi Android channel `importance` agar notifikasi darurat kantong darah CITO PMI Lampung muncul membunyikan dering suara kencang menyela layar HP.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Emulator Device Limitation
*   **Emulator Ketiadaan Modul**: Emulator PC tidak memiliki identitas hardware unik yang terdaftar di Google Play Services Cloud. 
*   Memaksa menembak fungsi `.getExpoPushTokenAsync()` di emulator PC seringkali memicu **Error Script Fatal (Reject Promise)** yang dapat menghentikan booting awal aplikasi petugas. `Device.isDevice` membentengi agar kode ini diabaikan secara damai saat testing di PC.

### Jawaban 2: Penanganan Penolakan Izin
*   Jika petugas menekan "Tolak", status izin bernilai selain `'granted'` (yaitu `'denied'`). 
*   Program akan langsung keluar dari fungsi (`return null`) tanpa mengeksekusi penarikan token, menyelamatkan aplikasi dari crash pelanggaran keamanan OS.

### Jawaban 3: AndroidImportance.MAX
*   Properti tersebut bernama **`Notifications.AndroidImportance.MAX`** (importance tingkat tinggi agar notifikasi menyembul di layar / *Heads-up notification*).
```
[FASE 5: HARI 80 SUKSES]
```
