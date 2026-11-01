# ⚡ Panduan Setup Lingkungan Kerja (Google Colab & Lokal)

Workshop **"Nanopore 16S rRNA Sequencing Analysis for Clinical Applications"** dirancang agar dapat dijalankan secara instan di awan (*cloud*) menggunakan **Google Colab** tanpa memerlukan instalasi software apa pun di komputer peserta. Selain itu, repositori ini juga menyediakan panduan bagi peserta yang ingin menjalankan analisis di komputer lokal.

---

## ☁️ Opsi 1: Google Colab (Direkomendasikan untuk Workshop)

### 📋 Prasyarat Awal:
1. **Akun Google**: Diperlukan untuk mengakses Google Colab dan menyimpan notebook di Google Drive Anda.
2. **Koneksi Internet**: Untuk mengunduh dataset sekuensing FASTQ dan database taksonomi resmi ONT EPI2ME NCBI Targeted Loci 16S/18S (~8,6 MB terkompresi).
3. **Peramban Web Modern**: Google Chrome, Mozilla Firefox, Microsoft Edge, atau Safari versi terbaru dengan dukungan JavaScript aktif.

### 🚀 Langkah Menjalankan di Google Colab:
1. Buka notebook praktikum melalui tautan [hands-on/gut_microbiome_16s.ipynb](hands-on/gut_microbiome_16s.ipynb) atau klik badge **Open in Colab** di `README.md`.
2. Klik menu **File** $\rightarrow$ **Save a copy in Drive** (Simpan salinan di Drive) agar seluruh modifikasi dan berkas hasil analisis tersimpan aman di Google Drive Anda sendiri.
3. Klik tombol **Connect** di pojok kanan atas untuk menghubungkan runtime virtual machine Linux gratis dari Google.
4. Jalankan sel berurutan dari atas ke bawah:
   - **Sel 0.1**: Inisialisasi parameter kerja dan direktori.
   - **Sel 1.1**: Memasang `micromamba` dan seluruh tools (`chopper`, `kraken2`, `krona`, `nanoplot`, `seqkit`). Proses ini memakan waktu < 2 menit tanpa perlu me-restart kernel Python!
   - **Sel 1.2**: Mengunduh dan memverifikasi integritas checksum SHA-256 database taksonomi resmi ONT EPI2ME NCBI Targeted Loci 16S/18S (`ncbi_targeted_loci_kraken2.tar.gz`, ~8,6 MB terkompresi / ~15 MB terekstrak).

---

## 💻 Opsi 2: Eksekusi Lokal (Linux / macOS / Windows WSL2)

Bagi peserta atau peneliti yang ingin menjalankan alur kerja ini di server laboratorium atau laptop pribadi secara mandiri pasca-workshop:

### 📋 Spesifikasi Minimum Komputer:
- **Sistem Operasi**: Linux (Ubuntu 20.04/22.04 LTS), macOS (Intel atau Apple Silicon), atau Windows 10/11 melalui **WSL2** (Windows Subsystem for Linux).
- **RAM**: Minimal 8 GB RAM (disarankan 16 GB jika ingin menjalankan `Emu`).
- **Penyimpanan Bebas**: Minimal 10 GB ruang kosong diska.

### 🛠️ Langkah Instalasi via Conda / Mamba:
1. Pastikan Anda telah memasang **Miniconda**, **Mambaforge**, atau **Micromamba**.
2. Kloning repositori ini ke komputer Anda:
   ```bash
   git clone https://github.com/OmicsLite/Workshop-Long-Reads-Metagenomics.git
   cd Workshop-Long-Reads-Metagenomics
   ```
3. Buat lingkungan kerja terisolasi menggunakan file `environment.yml` yang telah disediakan:
   ```bash
   conda env create -f environment.yml
   ```
4. Aktifkan lingkungan kerja:
   ```bash
   conda activate nanopore-16s-workshop
   ```
5. Verifikasi bahwa seluruh program bioinformatika telah terpasang dengan benar:
   ```bash
   chopper --version
   kraken2 --version
   seqkit version
   NanoPlot --version
   ktImportText
   ```
6. Jalankan Jupyter Lab untuk membuka notebook praktikum:
   ```bash
   jupyter lab hands-on/gut_microbiome_16s.ipynb
   ```
