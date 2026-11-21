# 🧬 Nanopore 16S rRNA Sequencing Analysis for Clinical Applications

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/OmicsLite/Workshop-Long-Reads-Metagenomics/blob/main/hands-on/gut_microbiome_16s.ipynb)
[![Bioinformatics](https://img.shields.io/badge/Bioinformatics-16S%20Amplicon-blue?style=flat-square)](#)
[![Nanopore](https://img.shields.io/badge/Sequencing-Oxford%20Nanopore-yellow?style=flat-square)](#)
[![Community](https://img.shields.io/badge/Community-OmicsLite-emerald?style=flat-square)](#)
[![License: MIT](https://img.shields.io/badge/Code%20License-MIT-green.svg)](LICENSE)
[![License: CC BY 4.0](https://img.shields.io/badge/Content%20License-CC%20BY%204.0-lightgrey.svg)](LICENSE)

Repositori ini berisi kurikulum ilmiah, modul teori terstruktur, dan lingkungan praktikum komputasi terpadu untuk workshop **"Nanopore 16S rRNA Sequencing Analysis for Clinical Applications"**. 

Workshop ini dirancang bagi klinisi, mahasiswa kedokteran, biologi, biomedis, dan peneliti pemula untuk memahami prinsip sekuensing generasi ketiga (Oxford Nanopore Technologies), melakukan kendali mutu (*quality control*), mengklasifikasikan taksonomi bakteri, menganalisis keanekaragaman komunitas (*alpha/beta diversity*), serta menarik kesimpulan biologis secara bertanggung jawab dalam konteks penelitian kesehatan.

> [!TIP]
> **🗺️ Peta Belajar Cepat (Big Picture dalam 10 Detik)**
> Jangan terintimidasi oleh banyaknya istilah teknis! Seluruh workshop ini sebenarnya hanya mengikuti **6 langkah sederhana**:
> 1. **Kenali Datanya** ([Modul 01](modules/01-nanopore-101/README.md)): Bagaimana Nanopore mengubah sinyal listrik DNA menjadi huruf `A, C, G, T`.
> 2. **Kenali Desainnya** ([Modul 02](modules/02-metagenomics/README.md)): Mengapa kita memakai gen penanda 16S (bukan *shotgun*) & apa saja bias lab-basahnya.
> 3. **Saring Sampahnya** ([Modul 03](modules/03-qc-filtering/README.md)): Membuang bacaan pendek, terlalu panjang, atau buram dengan `Chopper`.
> 4. **Beri Nama Bakterinya** ([Modul 04](modules/04-classification/README.md)): Mencocokkan sekuens ke database `NCBI Targeted Loci` via `Kraken2` (Genus & Spesies).
> 5. **Bandingkan Kelompoknya** ([Modul 05](modules/05-visualization/README.md)): Melihat pola & uji statistik bayi Vaginal vs Sesar (`Krona`, `Alpha/Beta Diversity`, `PERMANOVA`).
> 6. **Tafsirkan Secara Bijak** ([Modul 06](modules/06-clinical-interpretation/README.md)): Membedakan asosiasi riset dari diagnosis klinis.

---

## 🎯 Target Pembelajaran (Learning Outcomes)

Setelah menyelesaikan rangkaian workshop ini, peserta diharapkan mampu:
1. **Memahami Prinsip Molekuler & Sekuensing**: Menjelaskan anatomi gen 16S rRNA (V1–V9 vs V3–V9), mekanisme pori protein Nanopore, kimia R9.4.1 vs R10.4.1, serta proses *basecalling* deep learning (`Dorado`).
2. **Membedakan Metodologi**: Membedakan secara tegas antara *16S amplicon sequencing* (profil penanda taksonomi) dengan *shotgun metagenomics*, serta batasan identifikasi galur (*strain*).
3. **Kendali Mutu & Filtering Data FASTQ**: Menganalisis laporan mutu `NanoPlot` dan melakukan penyaringan panjang sekuens serta skor Phred Phred+33 menggunakan program modern `Chopper` (atau `SeqKit`).
4. **Klasifikasi Taksonomi Tingkat Spesies & Genus**: Menjalankan klasifikasi taksonomi berbasis minimizer $k$-mer (`Kraken2` + database resmi ONT EPI2ME NCBI Targeted Loci 16S/18S) pada tingkat Spesies dan Genus, membandingkan profil taksonomi multitingkat, serta memahami batasan gen penanda 16S vs pendekatan berbasis *alignment* (`minimap2` `wf-16s` / `Emu`) dan shotgun metagenomics.
5. **Analisis Ekologi Komunitas (Diversity Analysis)**: Menerapkan *rarefaction* terstandar, menghitung 5 indeks Alpha Diversity (`Observed Genera`, `Pielou J'`, `Shannon H'`, `Simpson 1 - D`, `Inverse Simpson 1/D`), serta mengevaluasi Beta Diversity (`Bray–Curtis` & `Jaccard`) via Principal Coordinates Analysis (PCoA) dan uji signifikansi PERMANOVA.
6. **Interpretasi Klinis Kritis & Bertanggung Jawab**: Menjelaskan sifat komposisional data mikrobioma, mengidentifikasi bias eksperimental (ekstraksi, primer, CNV 16S, *kitome*), serta membedakan asosiasi riset dari diagnosis klinis.

---

## 🔬 Studi Kasus Praktikum: Dataset PRJNA637202

Praktikum *hands-on* menggunakan dataset publik nyata yang telah ter-deidentifikasi:
* **Sumber Data**: BioProject NCBI **[PRJNA637202](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA637202)** (European Nucleotide Archive).
* **Publikasi Asal**: Angell, I. L., et al. (2020). *"De novo species identification using 16S rRNA gene nanopore sequencing."* *PeerJ*, 8, e10029. DOI: [10.7717/peerj.10029](https://doi.org/10.7717/peerj.10029).
* **Karakteristik Sampel**: Kohort feses bayi usia 3 bulan (*PreventADALL Study*), menargetkan wilayah gen 16S rRNA **V3–V9** (~1.140 bp) yang disekuensing dengan MinION (kimia R9.4.1).
* **Desain Eksperimen**: Subset berimbang 12 sampel ($n=6$ persalinan **Vaginal** vs $n=6$ persalinan **Sesar**) yang dipetakan langsung dari berkas metadata klinis terbuka [`MetadataPublish.xlsx`](hands-on/MetadataPublish.xlsx) dan manifest [`sample_manifest.tsv`](hands-on/sample_manifest.tsv).

---

## 📂 Struktur Direktori Repositori

```text
nanopore-16s-workshop/
├── README.md                          # 🏠 Panduan utama repositori & arsitektur workshop
├── LICENSE                            # 📜 Lisensi ganda: MIT (kode) & CC BY 4.0 (materi edukasi)
├── CITATION.cff                       # 🔖 Metadata sitasi repositori & atribusi dataset PRJNA637202
├── environment.yml                    # 📦 Spesifikasi environment Conda/Mamba mandiri
├── AGENDA.md                          # ⏰ Rundown kegiatan (Format Intensif 180m & Compact 120m)
├── SETUP.md                           # ⚡ Panduan persiapan Google Colab & eksekusi lokal
├── FAQ.md                             # ❓ Pertanyaan umum teknis & metodologis
├── GLOSSARY.md                        # 📖 Kamus istilah bioinformatika & ekologi mikrobioma
├── TROUBLESHOOTING.md                 # 🔧 Panduan penanganan error teknis aktual
│
├── modules/                           # 📚 Modul teori & fondasi sains
│   ├── 01-nanopore-101/              # Prinsip Nanopore, kimia R9 vs R10, Dorado, & error profile
│   ├── 02-metagenomics/              # 16S amplicon vs shotgun, anatomi 16S V3-V9, bias CNV & kitome
│   ├── 03-qc-filtering/              # Struktur FASTQ, Phred+33, NanoPlot, & filtering Chopper
│   ├── 04-classification/            # Kraken2 LCA, NCBI Targeted Loci 16S/18S, Bracken 1.000bp, & Emu
│   ├── 05-visualization/             # Krona Plot, Rarefaction, Alpha/Beta Diversity, PCoA & PERMANOVA
│   └── 06-clinical-interpretation/   # Nomenklatur ICNP 2021, kritik rasio F/B, & etika klinis
│
├── hands-on/                          # 🔬 Berkas praktikum interaktif
│   ├── gut_microbiome_16s.ipynb       # Notebook utama Google Colab (Chopper + Kraken2 + Diversity)
│   ├── sample_manifest.tsv            # Manifest 12 sampel terstruktur (Vaginal vs Sesar)
│   ├── MetadataPublish.xlsx           # Berkas suplemen asli Angell et al. (2020) [CC BY 4.0]
│   └── expected_outputs/              # Contoh tabel hasil ringkas untuk validasi mandiri peserta
│
├── cheatsheet/                        # 📄 Lembar contekan baris perintah (CLI)
│   └── nanopore-16s-cheatsheet.md     # Command line terverifikasi (SeqKit, Chopper, Kraken2, Krona)
│
└── assessment/                        # 📝 Evaluasi pemahaman (Pre/Post-Test)
    ├── quiz.md                        # 12 soal pilihan ganda konsep teknis & kehati-hatian klinis
    └── answer-key.md                  # Kunci jawaban & penjelasan ilmiah mendalam
```

---

## ⚡ Memulai Praktikum (Google Colab)

Peserta tidak perlu menginstal software bioinformatika berat di laptop pribadi. Praktikum dirancang untuk berjalan mulus di cloud:

1. Buka panduan persiapan di [SETUP.md](SETUP.md).
2. Buka notebook praktikum melalui tautan [hands-on/gut_microbiome_16s.ipynb](hands-on/gut_microbiome_16s.ipynb) atau klik tombol **Open in Colab** di atas.
3. Jalankan sel secara berurutan. Sel 1.1 akan memasang `micromamba` dan seluruh perangkat lunak yang dibutuhkan secara otomatis dalam waktu < 2 menit tanpa perlu me-restart session.

---

## ⚖️ Lisensi & Hak Penggunaan (Attribution)

- **Kode & Notebook Praktikum**: Berlisensi di bawah [MIT License](LICENSE).
- **Materi Modul, Teks, & Kurikulum**: Berlisensi di bawah [Creative Commons Attribution 4.0 International (CC BY 4.0)](LICENSE).
- **Atribusi Dataset**: Data sekuensing mentah berasal dari BioProject PRJNA637202 dan berkas [`MetadataPublish.xlsx`](hands-on/MetadataPublish.xlsx) didistribusikan ulang sesuai izin lisensi terbuka [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) merujuk pada publikasi:
  > Angell, I. L., Nilsen, M., Lødrup Carlsen, K. C., Carlsen, K.-H., Hedlin, G., Jonassen, C. M., Marsland, B., Nordlund, B., Rehbinder, E. M., Saunders, C., Skjerven, H. O., Staff, A. C., Söderhäll, C., Vettukattil, R., & Rudi, K. (2020). *De novo species identification using 16S rRNA gene nanopore sequencing*. PeerJ, 8, e10029. https://doi.org/10.7717/peerj.10029
