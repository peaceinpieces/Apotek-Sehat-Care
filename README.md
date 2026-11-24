# Apotek SehatCare

Aplikasi web apotek satu file (`index.html`) dengan dua mode: **Pasien** dan **Admin & Kasir**. Dibangun dengan HTML, Tailwind CSS (CDN), dan Vanilla JavaScript. Data disimpan di `localStorage` browser, jadi tidak membutuhkan backend maupun proses build.

Repo: https://github.com/peaceinpieces/Apotek-Sehat-Care

## Fitur

### Mode Pasien
- Katalog obat dengan ilustrasi kemasan (botol sirup, strip tablet, kapsul, botol vitamin) dan simbol golongan obat Indonesia (lingkaran hijau = Bebas, biru = Bebas Terbatas, merah huruf K = Keras).
- Pencarian dan filter keluhan: Semua, Batuk, Pilek, Meriang, Diare, Vitamin, Keras.
- Keranjang: ubah jumlah, pilih Ambil di Apotek atau Antar ke Alamat (ongkir Rp5.000).
- Obat Keras mewajibkan unggah resep. Tersedia tombol "Gunakan Contoh Resep" untuk uji cepat.
- Konsultasi Apoteker: chat cara minum obat dan jadwal minum (Pagi, Siang, Malam).
- Riwayat pesanan dan E-Faktur yang siap cetak, lengkap dengan aturan minum obat.

### Mode Admin & Kasir
- Statistik: Total Merek, Stok Menipis (stok 10 atau kurang), Pesanan Baru, Pendapatan.
- Manajemen Stok: ubah stok, hapus obat, dan tambah merek baru lewat "+ Tambah Merek Obat". Obat baru langsung muncul di katalog pasien.
- Pelayanan Online: verifikasi pesanan masuk (termasuk foto resep untuk obat keras), terbitkan faktur, atau tolak.
- POS Kasir: transaksi langsung, lalu "Terbitkan Faktur" otomatis memotong stok dan menampilkan faktur yang bisa dicetak dengan `window.print()`.
- Konsol Chat Pasien: balas pertanyaan pasien dengan Quick Replies aturan minum obat.

## Data Awal

| Obat | Keluhan | Golongan | Kemasan | Stok | Harga |
|---|---|---|---|---|---|
| OBH Combi | Batuk | Bebas Terbatas | Sirup 100ml | 25 | Rp18.500 |
| Siladex | Batuk | Bebas Terbatas | Sirup 60ml | 18 | Rp21.000 |
| Decolgen | Pilek | Bebas Terbatas | Strip 4 tab | 40 | Rp4.500 |
| Sanmol 500mg | Meriang | Bebas | Strip 10 tab | 50 | Rp5.000 |
| Panadol Extra | Meriang | Bebas | Strip 10 kaplet | 32 | Rp14.500 |
| New Diatabs | Diare | Bebas | Strip 4 tab | 28 | Rp4.000 |
| Entrostop | Diare | Bebas | Strip 12 tab | 8 | Rp9.500 |
| Imboost Force | Vitamin | Bebas | Strip 10 kaplet | 20 | Rp78.000 |
| Amoxicillin 500mg | Keras | Obat Keras | Strip 10 kaplet | 15 | Rp12.000 |

## Cara Menjalankan

Buka `index.html` langsung di browser, atau jalankan server lokal:

```bash
npx serve .
```

Koneksi internet dibutuhkan karena Tailwind CSS dan font Plus Jakarta Sans dimuat dari CDN.

## Deploy

File `index.html` harus berada di **root repo**.

**Vercel**
1. Import repo di Vercel.
2. Framework Preset: `Other`. Kosongkan Build Command dan Output Directory.
3. Pastikan commit berisi `index.html` ada di Production Branch (biasanya `main`), lalu Deploy.

**GitHub Pages**
1. Buka Settings, lalu Pages.
2. Pilih branch `main` dan folder `/ (root)`, lalu Save.

## Alur Pesanan

1. Pasien membuat pesanan, status **Menunggu Verifikasi**. Stok belum berkurang.
2. Admin menekan **Terbitkan Faktur**, status menjadi **Faktur Terbit**, dan stok terpotong.
3. Jika ditolak, status menjadi **Ditolak** dan stok tidak berubah.
4. Transaksi POS Kasir langsung berstatus **Selesai** dan stok langsung terpotong.

## Batasan

- Data tersimpan di `localStorage` browser masing-masing perangkat. Pasien dan admin di perangkat berbeda tidak berbagi data. Sinkronisasi hanya berjalan antar tab di browser yang sama.
- Tidak ada login. Siapa pun bisa berpindah mode, jadi aplikasi ini cocok untuk demo atau tugas, bukan produksi.
- Foto resep dikompres ke lebar maksimal 600px. Batas `localStorage` sekitar 5 MB, dan toast error muncul jika penyimpanan penuh.
- Data obat hanya bisa diubah stoknya. Untuk mengubah nama, harga, atau aturan pakai, hapus lalu tambah ulang.
- Untuk penggunaan nyata, ganti `localStorage` dengan backend seperti Firebase atau Supabase.

## Reset Data

Buka DevTools (F12), tab Console, lalu jalankan:

```js
localStorage.removeItem('sehatcare_v1'); location.reload();
```

## Teknologi

- HTML5 dan Vanilla JavaScript
- Tailwind CSS (CDN)
- Font Plus Jakarta Sans
- `localStorage` untuk penyimpanan data
