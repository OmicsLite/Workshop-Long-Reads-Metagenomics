# 🔧 Panduan Troubleshooting & Solusi Error

Dokumen ini berisi daftar masalah teknis aktual yang mungkin dihadapi peserta selama menjalankan Google Colab praktikum dan solusi terverifikasi untuk menanganinya.

---

## 🚀 Masalah 1: Biner Bioinformatika Tidak Ditemukan (`command not found`) Setelah Sel 1.1

* **Gejala**: Saat mengeksekusi sel `SeqKit`, `Chopper`, `NanoPlot`, atau `Kraken2`, muncul pesan error `FileNotFoundError` atau `command not found`.
* **Penyebab**: Variabel lingkungan sistem operasi `PATH` pada kernel Python Colab belum diperbarui dengan direktori `/content/bioenv/bin` tempat `micromamba` memasang paket.
* **Solusi**:
  1. Pastikan baris kode penambahan `PATH` dieksekusi:
     ```python
     import os
     os.environ["PATH"] = f"/content/bioenv/bin:{os.environ['PATH']}"
     ```
  2. Periksa ketersediaan biner dengan menjalankan perintah:
     ```bash
     !which chopper kraken2 seqkit NanoPlot
     ```
  3. Jika masih gagal, jalankan ulang Sel 1.1 dari awal hingga tanda centang hijau muncul.

---

## 🌐 Masalah 2: Pengunduhan FASTQ dari FTP ENA Gagal atau Terhenti (*Connection Timeout*)

* **Gejala**: Perintah `wget` pada Sel 2.1 terhenti sangat lama atau menampilkan pesan `Connection timed out` saat mengunduh data dari `ftp.sra.ebi.ac.uk`.
* **Penyebab**: Server FTP ENA di Eropa kadang membatasi jumlah koneksi simultan ketika puluhan peserta mengeksekusi pengunduhan pada saat yang bersamaan, atau jaringan kampus/instansi memblokir protokol FTP port 21.
* **Solusi**:
  1. `wget` pada skrip notebook sudah dilengkapi dengan flag `--continue --tries=3 --timeout=30` untuk mencoba mengulang koneksi secara otomatis.
  2. Jika koneksi ENA tetap gagal, gunakan **jalur cadangan (*fallback mirror*)** dengan beralih ke protokol HTTPS langsung:
     ```python
     # Ganti url ftp:// menjadi https:// pada manifest
     samples["url"] = samples["url"].str.replace("ftp://", "https://")
     ```
  3. Instruktur juga menyediakan arsip data ringkas di Google Drive / GitHub Releases yang dapat diunduh sekali jalan.

---

## 📊 Masalah 3: Visualisasi Krona Plot atau NanoPlot Tidak Tampil (Layar Putih Kosong)

* **Gejala**: Sel visualisasi `display(HTML(filename=...))` dieksekusi tanpa pesan error, tetapi jendela keluaran di bawah sel hanya menampilkan kotak kosong atau layar putih.
* **Penyebab**: Kebijakan keamanan isolasi (*iframe sandboxing*) pada peramban web modern (Google Chrome, Brave, atau Safari) sering memblokir eksekusi skrip JavaScript interaktif di dalam *iframe* notebook Colab.
* **Solusi**:
  1. Hal ini tidak berarti analisis Anda gagal! File laporan HTML interaktif telah berhasil diproduksi di direktori `results/`.
  2. Buka panel berkas di sebelah kiri Google Colab (ikon folder).
  3. Masuk ke folder `16s_workshop/results/`.
  4. Klik kanan pada file laporan (misal `V01_NanoPlot-report.html` atau `SRR11923954.krona.html`), lalu pilih **Download**.
  5. Buka berkas `.html` yang telah terunduh langsung di peramban web laptop Anda untuk menjelajahi grafik secara interaktif dengan lancar.

---

## 💾 Masalah 4: Sesi Google Colab Terputus (*RAM Exhausted / Out of Memory*)

* **Gejala**: Status runtime di kanan atas Colab berubah menjadi merah dengan notifikasi *"RAM exhausted"*, dan sesi terputus (*Disconnected*).
* **Penyebab**: Peserta mencoba memuat database taksonomi Kraken2 standar yang terlalu besar (>50 GB) yang melampaui kapasitas RAM gratis Colab (12 GB).
* **Solusi**:
  1. Selalu gunakan **database resmi ONT EPI2ME NCBI Targeted Loci 16S/18S** (~8,6 MB terkompresi / ~15 MB memori) yang diunduh otomatis pada Sel 1.2 praktikum. Database ini dirancang khusus agar sangat ringan (<20 MB RAM) dan cepat saat dijalankan.
  2. Klik menu **Runtime** $\rightarrow$ **Restart session**, jalankan kembali sel konfigurasi variabel (Sel 0.1), lalu lanjutkan ke sel analisis.

---

## ⏳ Masalah 5: Perbedaan Nilai Uji Statistik Antar-Peserta

* **Gejala**: Peserta mendapati nilai *Alpha Diversity* atau $p$-value PERMANOVA sedikit berbeda dari rekan sebelahnya.
* **Penyebab**: Langkah *rarefaction* dan uji permutasi PERMANOVA melibatkan pembangkitan bilangan acak (*random sampling*).
* **Solusi**:
  1. Pastikan Anda tidak mengubah parameter `SEED = 42` pada Sel 0.1 dan Sel 7.1. Nilai seed tetap menjamin hasil *subsampling* hipergeometrik dapat direproduksi identik 100% di semua komputer.
