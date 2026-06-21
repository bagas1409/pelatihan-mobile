# Hari 67: Infinite Scroll FlatList
## Memuat Halaman Berikutnya Secara Otomatis Saat Scroll Menyentuh Batas Bawah Menggunakan Properti onEndReached

Selamat datang di Hari 67! Melanjutkan materi pagination di Hari 66, menekan tombol "Next" berulang kali di handphone sangatlah kuno dan merepotkan. Pengguna mobile modern terbiasa dengan gaya navigasi ala media sosial: **Gulir Tanpa Batas (Infinite Scroll)**.

Ketika petugas menggulir daftar relawan dan layarnya hampir mentok ke batas bawah, aplikasi harus diam-diam menembak API halaman selanjutnya di balik layar (*Background Fetch*), lalu menyambungkan data baru tersebut ke bagian ekor list lama secara mulus tanpa menghentikan ayunan scroll jari petugas. Kita menggunakan komponen penyedot properti otomatis **`onEndReached`** milik FlatList.

---

## 1. Konsep Kerja onEndReached

Dua properti vital di dalam `<FlatList>`:
*   **`onEndReachedThreshold`**: Penentu sensitivitas batas akhir (bernilai `0.0` sampai `1.0`). Jika disetel `0.5`, fungsi fetch data baru akan mulai ditarik ketika petugas sudah men-scroll setengah layar sebelum mentok bagian dasar bawah.
*   **`onEndReached`**: Fungsi callback yang akan secara otomatis terpanggil (*Triggered*) saat jarak threshold tersebut dilewati.
*   **`ListFooterComponent`**: Ruang khusus di bagian bawah list untuk menampilkan ikon bundar muter (*Loading Spinner*) saat request halaman selanjutnya sedang berlangsung.

---

## 2. Studi Kasus PMI: Buku Besar Relawan Infinite Scroll

Mari integrasikan Infinite Scroll ke dalam buku riwayat relawan kita dengan simulasi Server-Side API berhalaman (halaman 1, 2, 3..):

```tsx
// app/(tabs)/relawan-infinite.tsx
import React, { useState, useEffect } from 'react';
import { View, Text, FlatList, ActivityIndicator } from 'react-native';

interface RelawanData {
  id: string;
  nama: string;
}

export default function LayarRelawanInfinite() {
  const [dataList, setDataList] = useState<RelawanData[]>([]);
  const [halaman, setHalaman] = useState<number>(1);
  const [isLoadingMore, setIsLoadingMore] = useState<boolean>(false);
  const [hasMoreData, setHasMoreData] = useState<boolean>(true); // Penanda apakah data di server sudah habis

  // 1. FUNGSI PENARIK DATA BERHALAMAN DARI API
  const ambilDataServer = async (pageTarget: number) => {
    if (isLoadingMore || !hasMoreData) return;
    
    setIsLoadingMore(true);
    console.log(`[InfiniteScroll] Menarik data API relawan HALAMAN ${pageTarget}...`);

    // Simulasi jeda waktu API 1.5 detik
    setTimeout(() => {
      // Dummy data 10 baris per halaman
      const dataBaru = Array.from({ length: 10 }, (_, i) => ({
        id: `H${pageTarget}-Baris${i}`,
        nama: `Relawan Blok ${pageTarget} Nomor ${i + 1}`
      }));

      // Sambungkan data baru ke ujung ekor array data lama!
      setDataList((dataLama) => [...dataLama, ...dataBaru]);
      
      // Jika sampai di halaman 5, anggap data database sudah mentok habis!
      if (pageTarget >= 5) {
        setHasMoreData(false);
      }

      setIsLoadingMore(false);
    }, 1500);
  };

  // Panggilan pertama saat layar dipasang (Mount)
  useEffect(() => {
    ambilDataServer(1);
  }, []);

  // 2. FUNGSI PEMICU KETIKA SCROLL MENTOK BAWAH
  const handleLoadMore = () => {
    if (!isLoadingMore && hasMoreData) {
      const halamanBerikutnya = halaman + 1;
      setHalaman(halamanBerikutnya);
      ambilDataServer(halamanBerikutnya);
    }
  };

  // 3. KOMPONEN KAKI (LOADING SPINNER BAWAH)
  const renderKakiList = () => {
    if (!isLoadingMore) return null;
    return (
      <View className="py-6 items-center justify-center">
        <ActivityIndicator size="small" color="#ef4444" />
        <Text className="text-[10px] font-bold text-slate-400 mt-2">Menarik data selanjutnya...</Text>
      </View>
    );
  };

  return (
    <View className="flex-1 bg-white pt-10">
      <View className="px-6 mb-4">
        <Text className="font-black text-slate-800 text-sm">♾️ Daftar Anggota Berkelanjutan</Text>
        <Text className="text-[10px] text-slate-400">Total ter-load: {dataList.length} orang</Text>
      </View>

      <FlatList
        data={dataList}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => (
          <View className="mx-6 mb-3 p-4 bg-slate-50 border border-slate-100 rounded-2xl">
            <Text className="text-xs font-bold text-slate-700">{item.nama}</Text>
          </View>
        )}
        
        // ===================================
        // 🚀 SENSOR INFINITE SCROLL
        // ===================================
        onEndReached={handleLoadMore} // Fungsi terpanggil saat scroll menyentuh batas bawah
        onEndReachedThreshold={0.5} // Sensitivitas batas: Panggil saat sisa ruang scroll tinggal 50%
        ListFooterComponent={renderKakiList} // Tempelkan komponen loading spinner merah di kaki array
        
        showsVerticalScrollIndicator={false}
      />
    </View>
  );
}
```

---

## 3. Latihan Soal Mandiri
1. Apa fungsi logis dari penerapan kode *Spread Operator* JavaScript: `[...dataLama, ...dataBaru]` pada blok pengambilan data API Infinite Scroll di atas?
2. Buka kode blok logika properti pengaman status. Mengapa kita wajib membentengi pemanggilan fungsi `ambilDataServer` menggunakan pengandaian awal pencegat `if (isLoadingMore) return;`?
3. Sebutkan kelebihan *Infinite Scroll* jika dibandingkan dengan sistem Pagination nomor-tombol konvensional (Prev 1 2 3 Next) dari sudut pandang ergonomis kenyamanan sentuhan jari satu tangan (*One-handed thumb UX*).

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Penggabungan Array Utuh (Appending/Concatenation)
*   **Merakit Gerbong Kereta**: Data API relawan yang mendarat dari server halaman ke-2 adalah deretan array baru. Jika kita menggunakan fungsi `setDataList(dataBaru)`, maka list relawan halaman 1 yang sedang tampil di layar akan musnah tertimpa hilang. 
*   Kita membongkar bungkus array `dataLama`, lalu meletakkan array `dataBaru` tepat di ujung belakangnya, menjahit keduanya menjadi satu kesatuan array yang lebih panjang tak terputus.

### Jawaban 2: Pencegahan Penarikan Data Ganda (Double Fetch Race Condition)
*   **Pemblokir Multi Klik Cepat**: Modul scroll HP sangatlah sensitif. Seringkali batas bawah disentuh 3 kali mili-detik dalam satu kali momentum usapan kencang. 
*   Jika sistem tidak dibentengi state pengunci bendera `isLoadingMore`, HP akan langsung iseng mengirim 3 rentetan request tembakan halaman ke-2 secara paralel ke server backend API dalam waktu bersamaan. Memori HP akan dipenuhi oleh balasan data ganda duplikat, dan aplikasi akan seketika *Hang*.

### Jawaban 3: Gesekan Tanpa Friksi (Frictionless Browsing)
*   **Ergonomi Jempol Tangan**: Menekan tombol navigasi angka kecil berjejer "1, 2, 3" di pojok layar memutus aliran konsentrasi visual, serta membuang kalori sentuh yang tidak perlu karena ibu jari petugas terpaksa menjangkau bagian bawah pojok memencet presisi area sempit. 
*   Sistem gulir tak terbatas ini membiarkan ibu jari hanya melakukan satu aksi gerak usap vertikal (*Scroll Swipe*) tiada henti secara natural tanpa pemikiran jeda logis klik.
```
[FASE 4: HARI 67 SUKSES]
```
