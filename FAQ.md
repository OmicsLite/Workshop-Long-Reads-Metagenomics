# ❓ FAQ & Checklist Pra-Workshop

Gunakan dokumen ini untuk mempersiapkan diri sebelum sesi workshop dimulai dan mencari jawaban atas pertanyaan teknis serta metodologis yang sering diajukan peserta.

---

## 📋 Checklist Persiapan Awal (Pra-Workshop)

Sebelum hari pelaksanaan kelas, pastikan Anda telah mencentang hal-hal berikut:
- [ ] **Akun Google Aktif**: Pastikan Anda dapat masuk (*login*) ke akun Google untuk menggunakan Google Colab.
- [ ] **Penyimpanan Google Drive Cukup**: Salinan notebook praktikum dan arsip hasil unduhan membutuhkan ruang kosong sekitar ≈200 MB di Google Drive Anda.
- [ ] **Browser Modern**: Pastikan browser Anda (Chrome/Firefox/Edge/Safari) sudah diperbarui ke versi terbaru dan mengizinkan JavaScript untuk menampilkan bagan interaktif Krona Plot dan NanoPlot.
- [ ] **Koneksi Internet Stabil**: Karena analisis berjalan di cloud Google Colab, Anda memerlukan koneksi internet stabil untuk berinteraksi dengan runtime virtual machine.

---

## 💬 Pertanyaan yang Sering Diajukan (FAQ)

### 1. Apakah saya wajib menginstal Linux di laptop saya untuk mengikuti praktikum?
**Tidak.** Praktikum ini berjalan 100% di atas server cloud Google Colab yang berbasis sistem operasi Linux di awan. Anda hanya membutuhkan browser internet untuk mengendalikannya. Sistem operasi laptop Anda (Windows, macOS, ChromeOS, atau Linux) tidak memengaruhi eksekusi praktikum.

### 2. Berapa kapasitas RAM laptop saya yang dibutuhkan?
Karena komputasi pemrosesan FASTQ dan Kraken2 dijalankan pada virtual machine Google Colab (yang menyediakan 12 GB RAM secara gratis), laptop dengan RAM 4 GB sekalipun dapat mengikuti workshop ini dengan lancar tanpa menjadi lambat.

### 3. Apakah kita menganalisis data hingga tingkat Spesies pada praktikum ini?
**Ya.** Dengan mengintegrasikan database resmi ONT EPI2ME NCBI Targeted Loci 16S/18S (`ncbi_targeted_loci_kraken2`), praktikum ini membedah data hingga tingkat **Spesies** (`rank = S`) sekaligus menyediakan matriks perbandingan pada tingkat **Genus** (`rank = G`). Namun, peserta diajarkan prinsip kehati-hatian ilmiah: identifikasi 16S tingkat spesies memiliki batasan mendasar akibat kemiripan sekuens antarspesies dekat (seperti *Escherichia coli* dan *Shigella* spp.) serta laju error indel Nanopore, sehingga takson tingkat spesies diposisikan sebagai hipotesis komputasional terarah (*putative species assignment*).

### 4. Mengapa kita menggunakan hitungan clade reads langsung dari Kraken2 dan bukan Bracken?
Database NCBI Targeted Loci yang kita gunakan memang menyertakan model distribusi k-mer 1.000 bp (`database1000mers.kmer_distrib`) yang kompatibel dengan panjang fragmen kita (≈1.140 bp). Namun, untuk alur kerja praktikum inti, kita sengaja menggunakan **clade reads langsung dari laporan Kraken2 (`.k2report`)**. Pendekatan ini mempertahankan transparansi hirarki pohon taksonomi tanpa penataan ulang probabilistik Bayesian yang berisiko memperbesar *false positive* pada data R9.4.1 ber-error *indel*. Bracken tetap dibahas sebagai pengayaan (lebih cocok untuk *shotgun* atau R10.4.1 Q20+), bersama opsi klasifikasi berbasis *alignment* seperti mode `minimap2` pada ONT EPI2ME (`wf-16s`) atau **`Emu`** (Curry et al., 2022, *Nature Methods*).

### 5. Mengapa rentang filter panjang disetel ke 1.000–1.700 bp, bukan 1.300–1.700 bp?
Dataset yang kita gunakan (NCBI PRJNA637202; Angell et al., 2020) menargetkan wilayah gen 16S rRNA **V3 hingga V9** (≈1.140 bp), bukan V1–V9 (≈1.500 bp). Jika kita memaksakan batas minimum 1.300 bp, lebih dari 90% *reads* biologis yang valid akan terbuang secara salah! Mengetahui desain primer amplikon laboratorium basah sangat penting sebelum menentukan parameter filter bioinformatika.

### 6. Apakah hasil analisis di workshop ini dapat dipakai untuk mendiagnosis pasien?
**Sama sekali tidak.** Alur kerja ini adalah alat eksplorasi untuk **pendidikan dan penelitian akademik**. Pengambilan keputusan klinis individual (seperti diagnosis penyakit atau pemilihan antibiotik) mewajibkan alur kerja terakreditasi laboratorium medis (ISO 15189 / CLIA), kendali batas deteksi analitis (LoD/LoQ), serta konfirmasi mikrobiologi fenotipik.
