# Hari 79: Pengenalan Push Notifications
## Memahami Cara Kerja Push Notifications APNs Apple & Firebase Cloud Messaging (FCM) Google Di Handphone Mobile

Selamat datang di Hari 79! Hari ini kita akan mempelajari teori di balik **Push Notifications** (Notifikasi Dorong).

Untuk kasus PMI, ketika terjadi musibah kecelakaan besar dan rumah sakit membutuhkan suplai 20 kantong darah O negatif segera, admin PMI tidak mungkin menelepon relawan satu per satu. Admin akan mengirimkan sinyal notifikasi darurat. 

Meskipun aplikasi DonorKu di HP relawan sedang ditutup rapat atau HP mereka sedang dalam saku terkunci, notifikasi harus menyembul berdering memanggil mereka.

---

## 1. Aliran Arsitektur Push Notification

Aplikasi mobile tidak bisa sembarangan terus-menerus membuka koneksi WebSocket di latar belakang karena akan menghabiskan kuota baterai HP. Oleh karena itu, pengiriman notifikasi harus melewati gerbang resmi sistem operasi HP:
*   **Android (Google)**: Menggunakan server **Firebase Cloud Messaging (FCM)**.
*   **iOS (Apple)**: Menggunakan server **Apple Push Notification service (APNs)**.

```
[EXPRESS.JS BACKEND] ---> (Kirim Payload Pesan) 
                                |
                                v
               [EXPO PUSH SERVICE / FCM / APNS]
                                |
             (Distribusi Melalui Satelit Resmi OS)
                                |
                                v
                    [HP RELAWAN (NOTIFIKASI)]
```

---

## 2. Token Perangkat (Push Token)

Agar pesan bisa mendarat di HP yang tepat, setiap HP unik yang mengunduh aplikasi kita akan menerbitkan satu string alamat unik rahasia yang disebut **Expo Push Token** (bentuknya seperti: `ExponentPushToken[xxxxxxxxxxxx]`).

*   Mobile app meminta token unik ini ke OS HP.
*   Mobile app mengirimkan token ini ke database backend Express.js untuk ditabung di tabel `user_tokens`.
*   Saat ingin mengirim pesan, backend Express.js memanggil API Expo Service dengan menargetkan token tersebut.

---

## 3. Latihan Soal Mandiri
1. Mengapa aplikasi mobile dilarang keras terus-menerus membuka jalur koneksi real-time WebSocket di latar belakang saat aplikasi diminimalkan di saku celana? Jelaskan akibatnya pada ketahanan baterai HP.
2. Sebutkan nama dua gerbang server resmi milik Google dan Apple yang bertugas mengantarkan paket data notifikasi dari server internet menuju sistem operasi internal ponsel.
3. Apa kegunaan utama dari string rahasia **Expo Push Token** bagi kelayakan alamat sasaran pengiriman notifikasi dari server backend?

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Pengurasan Daya Baterai Secara Cepat (Battery Drain Protection)
*   **Koneksi Konstan**: Jalur WebSocket memaksa antena modem Wi-Fi/seluler HP tetap menyala reaktif mencari sinyal tanpa henti. 
*   Hal ini akan menguras daya baterai HP petugas hingga habis dalam beberapa jam saja. Dengan menggunakan Push Notification bawaan OS, HP cukup membuka 1 pintu koneksi tunggal yang hemat baterai ke server milik Google/Apple untuk melayani seluruh aplikasi yang terinstal.

### Jawaban 2: Google FCM dan Apple APNs
*   Gerbang server tersebut adalah **FCM (Firebase Cloud Messaging)** untuk sistem operasi Android, dan **APNs (Apple Push Notification service)** untuk sistem operasi iOS iPhone.

### Jawaban 3: Alamat Rumah HP Penerima (Target Address)
*   **Alamat Pengiriman**: Sama halnya dengan nomor telepon atau alamat rumah, database backend membutuhkan target yang presisi. 
*   Expo Push Token bertindak sebagai alamat pos unik digital HP tersebut. Tanpa token ini, server backend PMI tidak akan pernah tahu ke perangkat mana pesan darurat kantong darah CITO tersebut harus diledakkan.
```
[FASE 5: HARI 79 SUKSES]
```
