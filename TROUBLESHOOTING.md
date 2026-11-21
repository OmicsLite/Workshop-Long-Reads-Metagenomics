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

## 🌐 Masalah 2: Pengunduhan FASTQ Gagal atau Terhenti (*Connection Timeout*)

* **Gejala**: Pengunduhan 12 file FASTQ pada Sel 2.1 berjalan lambat atau menampilkan peringatan kegagalan koneksi (*Connection timed out* / ukuran byte tidak cocok).
* **Penyebab**: Server ENA di Eropa kadang membatasi koneksi simultan saat puluhan peserta mengunduh bersamaan, atau jaringan kampus/instansi mengalami lonjakan trafik.
* **Solusi**:
  1. Sel 2.1 sudah dilengkapi **pengunduh 2 lapis otomatis**:
     - **Lapis 1 (Utama)**: Mengunduh 1 arsip ringkas `prjna637202_12samples_raw.tar.gz` (~490 MB) langsung dari **GitHub Releases** (`v1.0-workshop-data`), biasanya selesai dalam ~10–20 detik di server Colab.
     - **Lapis 2 (*Fallback* Otomatis)**: Jika GitHub Release tidak dapat diakses, skrip otomatis mengunduh per sampel melalui **HTTPS paralel (4 koneksi bersamaan)** dari ENA dengan flag `--continue --tries=5 --timeout=45` serta verifikasi ukuran byte eksak.
  2. Jika satu file gagal di tengah jalan, cukup **jalankan ulang Sel 2.1** — file yang sudah utuh akan otomatis dilewati (*ready*) dan hanya file yang belum lengkap yang dilanjutkan.

---

## 📊 Masalah 3: Visualisasi Krona Plot atau NanoPlot Tidak Tampil (Layar Putih Kosong)

* **Gejala**: Sel visualisasi NanoPlot (Sel 3.1) atau *iframe* Krona Plot (Sel 6.2) selesai tanpa error, tetapi kotak keluaran hanya menampilkan layar putih atau tidak bisa diklik.
* **Penyebab**: Kebijakan keamanan isolasi (*iframe sandboxing*) pada beberapa peramban web (Safari/Brave/Chrome dengan ekstensi pemblokir skrip) dapat membatasi eksekusi JavaScript interaktif di dalam notebook Colab.
* **Solusi**:
  1. Hal ini tidak berarti analisis Anda gagal! Seluruh file HTML interaktif telah tersimpan rapi di folder `results/`.
  2. Buka panel berkas di sebelah kiri Google Colab (ikon folder).
  3. Arahkan ke subfolder yang sesuai:
     - Laporan NanoPlot: `16s_workshop/results/01_qc/V01_NanoPlot-report.html`
     - Diagram Krona: `16s_workshop/results/04_krona/html/SRR11923954.krona.html` (atau file sampel lain).
  4. Klik kanan pada file `.html` tersebut, pilih **Download**, lalu buka langsung di tab browser laptop Anda untuk eksplorasi interaktif penuh.

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
