# 👨‍🏫 Panduan Fasilitator & Instruktur Workshop

Panduan ini disiapkan khusus bagi pemateri (*lead instructor*) dan fasilitator praktikum (*teaching assistants*) untuk memandu jalannya workshop **"Nanopore 16S rRNA Sequencing Analysis for Clinical Applications"**.

---

## 🎯 Peran & Tanggung Jawab Fasilitator

1. **Memastikan Kesiapan Peserta (Menit 0–15)**:
   - Pastikan peserta berhasil membuka Google Colab dan melakukan **File $\rightarrow$ Save a copy in Drive**.
   - Pantau eksekusi Sel 1.1 (`micromamba`). Jika ada peserta yang lambat atau mengalami kendala jaringan, segera arahkan untuk memeriksa panel status runtime kanan atas (Connected).
2. **Menjaga Alokasi Waktu (Pacing)**:
   - Jangan biarkan kelas terhenti terlalu lama pada proses unduhan data. Jika jaringan lokal peserta bermasalah, arahkan peserta menggunakan file *pre-computed* di folder `results/` atau gunakan dataset manifest lokal.
3. **Memandu Checkpoint Diskusi**:
   - Fasilitator bertugas memantik diskusi kritis pada setiap jeda materi, bukan sekadar membantu membetulkan sintaks error kode.

---

## 💡 Kunci Pembahasan Checkpoint Diskusi Notebook

### Checkpoint Diskusi 1 (Setelah Sel 3.2 — Quality Control & Filtering):
* **Pertanyaan 1**: *Apakah puncak panjang read berada dekat 1.500 bp?*
  - **Jawaban Fasilitator**: Tidak. Puncak panjang *reads* pada dataset ini berpusat di sekitar **1,14 kb (~1.140 bp)**. Jelaskan kepada peserta bahwa ini **bukan DNA terdegradasi**, melainkan karena desain primer PCR yang dipilih oleh peneliti studi *PreventADALL* (Angell et al., 2020) menargetkan wilayah **V3 hingga V9**, bukan V1–V9 utuh.
* **Pertanyaan 2**: *Berapa persen read yang hilang setelah filter Chopper, dan parameter mana yang paling berpengaruh?*
  - **Jawaban Fasilitator**: Rata-rata retensi read berada di kisaran 70–85%. Parameter yang paling banyak membuang read adalah batas kualitas `MIN_MEAN_Q = 10` dan batas bawah panjang `MIN_LENGTH = 1000`. Tunjukkan tabel `retained_pct` yang dicetak di notebook.
* **Pertanyaan 3**: *Apa trade-off jika `MIN_MEAN_Q` dinaikkan menjadi Q15 atau Q20?*
  - **Jawaban Fasilitator**: Pada data Nanopore kimia R9.4.1, menaikkan ambang ke Q15 akan membuang >90% data, dan menyetel ke Q20 akan menghasilkan 0 reads lolos filter! Ambang filter harus disesuaikan secara realistis dengan kimia sekuensing yang digunakan (R9.4.1 vs R10.4.1).

---

### Checkpoint Diskusi 2 (Setelah Sel 8 — Interpretasi Bertanggung Jawab):
* **Pertanyaan 1**: *Apakah ada perbedaan komposisi mikrobioma usus antara bayi persalinan Vaginal vs Sesar?*
  - **Jawaban Fasilitator**: Ya, pada subset eksploratif 12 sampel ini terlihat tren perbedaan yang jelas. Kelompok bayi persalinan vaginal menunjukkan kolonisasi awal genus *Bacteroides* dan *Bifidobacterium* yang lebih menonjol, sementara bayi persalinan sesar seringkali memiliki proporsi *Enterobacteriaceae* atau genus kulit maternal/lingkungan yang lebih bervariasi pada bulan-bulan awal kehidupan.
* **Pertanyaan 2**: *Apakah hasil uji signifikansi (p < 0.05 atau q < 0.05) membuktikan bahwa cara persalinan menjadi penyebab langsung perbedaan tersebut?*
  - **Jawaban Fasilitator**: **SAMA SEKALI TIDAK.** Tekankan prinsip **Korelasi/Asosiasi $\neq$ Kausalitas**. Desain studi ini bersifat observasional. Terdapat faktor perancu (*confounding factors*) yang sangat kuat, seperti pemberian antibiotik profilaksis maternal sebelum operasi sesar, inisiasi menyusui dini (ASI vs susu formula), serta paparan lingkungan rumah sakit. Tanpa mengontrol kovariat-kovariat tersebut, kita tidak boleh menarik kesimpulan kausal.
* **Pertanyaan 3**: *Bagaimana kita menyikapi hasil penugasan taksonomi tingkat Spesies dari Kraken2 vs tingkat Genus?*
  - **Jawaban Fasilitator**: Database resmi ONT EPI2ME NCBI Targeted Loci 16S/18S menyediakan penugasan hingga tingkat Spesies (`rank = S`). Namun, fasilitator harus mengingatkan peserta bahwa gen 16S rRNA memiliki kemiripan sekuens yang sangat tinggi pada kelompok bakteri tertentu (misal: *E. coli* vs *Shigella* atau *Bacillus cereus sensu lato*), dan laju error indel sekuensing R9.4.1 dapat menghasilkan klasifikasi positif palsu jika hanya mengandalkan pencocokan k-mer eksak. Oleh karena itu, kita menyajikan profil **Spesies sebagai taksonomi terarah (*putative species*)** dan mendampinginya dengan kontrol komparatif pada tingkat **Genus** yang lebih robust. Jika peneliti membutuhkan resolusi spesies yang definitif pada penelitian lanjutannya, disarankan menggunakan alur penjajaran penuh berbasis EM seperti **`Emu`** atau sekuensing genom utuh (*shotgun metagenomics*).

---

## ⏱️ Manajemen Waktu & Antisipasi Hambatan di Kelas

| Hambatan di Kelas | Solusi Langsung Fasilitator |
|---|---|
| **Colab lambat saat unduh FASTQ ENA** | Arahkan peserta menggunakan manifest dengan protokol HTTPS atau sediakan tautan ZIP Drive ringkas yang sudah disiapkan panitia. |
| **Peserta mengubah kode dan error** | Minta peserta mengeklik menu **Edit $\rightarrow$ Undo** atau jalankan ulang sel konfigurasi Sel 0.1. |
| **Iframe Krona Plot putih kosong di browser peserta** | Minta peserta mengunduh berkas `.html` dari panel Files kiri Colab dan membukanya di browser laptop lokal (Chrome/Safari). |

