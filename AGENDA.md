# ⏰ Agenda & Rundown Workshop

Workshop **"Nanopore 16S rRNA Sequencing Analysis for Clinical Applications"** dirancang dengan dua skenario durasi fleksibel untuk mengakomodasi format kelas yang berbeda:

---

## 📅 Skenario A: Format Intensif Penuh (180 Menit / 3 Jam) — Direkomendasikan

Format ini sangat direkomendasikan agar peserta dari latar belakang klinis maupun biologi mendapatkan pemahaman konseptual yang utuh, menjalankan alur bioinformatika secara mandiri, dan berdiskusi mendalam mengenai interpretasi hasil statistik ekologi mikroba.

| Waktu | Durasi | Sesi | Pokok Bahasan & Aktivitas |
|---|---|---|---|
| **09:00 - 09:15** | 15 Menit | **Pembukaan & Orientasi** | Sambutan, presensi, orientasi lingkungan Colab, dan pembagian tautan kerja. |
| **09:15 - 09:50** | 35 Menit | **Kuliah Konsep 1** | **Fondasi Sekuensing Nanopore & Desain Amplikon 16S**<br>• Prinsip deteksi arus ionik & kimia R9 vs R10.<br>• Basecalling deep learning (`Dorado`).<br>• Amplicon 16S vs Shotgun metagenomics.<br>• Target amplikon V3–V9 (~1.140 bp) kohort *PreventADALL* (PRJNA637202). |
| **09:50 - 10:00** | 10 Menit | **Setup Runtime** | Peserta membuka notebook Colab, membuat salinan di Google Drive, dan menjalankan Sel 1.1 (`micromamba` setup) & Sel 1.2 (download & verifikasi database NCBI Targeted Loci 16S/18S). |
| **10:00 - 10:35** | 35 Menit | **Hands-on Sesi 1** | **Kendali Mutu (QC) & Filtering Sekuens**<br>• Inspeksi metadata manifest (`sample_manifest.tsv`).<br>• Rangkuman statistik read mentah via `SeqKit stats`.<br>• Visualisasi interaktif panjang & kualitas dengan `NanoPlot`.<br>• Penyaringan terstandar panjang (1.000–1.700 bp) & kualitas ($Q \ge 10$) via `Chopper`. |
| **10:35 - 10:45** | 10 Menit | *Coffee Break* | Istirahat sejenak sambil memastikan proses filtering 12 sampel selesai. |
| **10:45 - 11:20** | 35 Menit | **Hands-on Sesi 2** | **Klasifikasi Taksonomi & Visualisasi Komposisi**<br>• Klasifikasi k-mer cepat dengan `Kraken2` (`--confidence 0.01`) pada database NCBI Targeted Loci 16S/18S.<br>• Ekstraksi profil multitingkat: Genus (`rank = G`) dan Spesies terarah (`rank = S`).<br>• Kehati-hatian resolusi spesies 16S, alur `Kraken2 → Bracken`, serta opsi berbasis *alignment* (`minimap2` `wf-16s` / `Emu`).<br>• Visualisasi bagan bertingkat interaktif `Krona Plot` via `ktImportText`. |
| **11:20 - 11:45** | 25 Menit | **Hands-on Sesi 3** | **Analisis Ekologi Komunitas (Diversity Analysis)**<br>• Mengapa perlu *Rarefaction* sebelum membandingkan keanekaragaman.<br>• Perhitungan 5 indeks Alpha Diversity (`Observed`, `Pielou J'`, `Shannon`, `Simpson`, `Inverse Simpson`).<br>• Evaluasi Beta Diversity: `Bray–Curtis` (kelimpahan) vs `Jaccard` (ada/tidak) & plot PCoA.<br>• Uji signifikansi PERMANOVA ganda & differential abundance Mann–Whitney + Benjamini–Hochberg (FDR). |
| **11:45 - 12:00** | 15 Menit | **Sintesis & Diskusi Klinis** | **Interpretasi Kritis & Etika Riset Klinis**<br>• Asosiasi vs Kausalitas pada mode persalinan bayi (Vaginal vs Sesar).<br>• Faktor perancu (ASI, antibiotik maternal), bias CNV, dan *kitome*.<br>• Batas tegas antara riset edukatif vs diagnostik klinis.<br>• Post-Test & Penutup. |

---

## 📅 Skenario B: Format Ringkas (120 Menit / 2 Jam)

Bila durasi acara dibatasi maksimal 2 jam, fokus praktikum difokuskan pada alur utama QC hingga klasifikasi taksonomi, sedangkan analisis statistik keragaman (*diversity*) disajikan sebagai demonstrasi terpandu (*guided demonstration*):

| Waktu | Durasi | Aktivitas | Topik / Sesi |
|---|---|---|---|
| **09:00 - 09:10** | 10 Menit | Pembukaan | Pengenalan instruktur, pembagian tautan Colab, dan koneksi runtime. |
| **09:10 - 09:35** | 25 Menit | Sesi Teori | Pengantar Nanopore 16S, target V3–V9 PRJNA637202, dan prinsip QC. |
| **09:35 - 09:55** | 20 Menit | Hands-on QC | Menjalankan `SeqKit stats`, `NanoPlot`, dan filtering dengan `Chopper`. |
| **09:55 - 10:25** | 30 Menit | Hands-on Klasifikasi | Menjalankan `Kraken2` + NCBI Targeted Loci 16S/18S dan merender `Krona Plot`. |
| **10:25 - 10:45** | 20 Menit | Guided Diversity | Mengeksekusi sel *Rarefaction*, Alpha/Beta Diversity (PCoA & PERMANOVA) dengan penjelasan langsung oleh instruktur. |
| **10:45 - 11:00** | 15 Menit | Diskusi & Penutup | Diskusi interpretasi klinis bertanggung jawab, tanya jawab, dan kuis akhir. |

---

## 🎯 Target Pencapaian Peserta (Milestones)
- **Menit ke-20**: Seluruh peserta berhasil mengaktifkan runtime Colab dan memverifikasi biner bioinformatika via `micromamba`.
- **Menit ke-50**: Peserta berhasil membuat laporan `NanoPlot` dan file FASTQ bersih hasil `Chopper`.
- **Menit ke-85**: Laporan klasifikasi `.k2report` dan diagram `Krona Plot` interaktif berhasil dibuka di layar peserta.
- **Menit ke-115**: Plot PCoA keragaman mikrobioma usus bayi (Vaginal vs Sesar) dan hasil uji PERMANOVA berhasil ditampilkan.
