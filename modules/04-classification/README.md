# Modul 04: Klasifikasi Taksonomi: Kraken2 & Database NCBI Targeted Loci (Spesies & Genus)

## 🎯 Target Pembelajaran
Setelah menyelesaikan modul ini, peserta mampu:
1. Menjelaskan mekanisme klasifikasi taksonomi berbasis pencocokan $k$-mer minimizer dan penugasan *Lowest Common Ancestor* (LCA) pada **Kraken2**.
2. Memahami arsitektur database resmi **NCBI Targeted Loci 16S/18S** dari Oxford Nanopore Technologies (ONT EPI2ME Labs) dan keunggulannya dalam menyediakan taksonomi tingkat **Spesies** (`rank = S`).
3. Membedakan secara kritis antara **Keberadaan Label Spesies** pada database dengan **Kepastian Biologis Identifikasi Spesies** pada gen 16S rRNA.
4. Memahami struktur file model distribusi k-mer Bracken (`database1000mers.kmer_distrib`) dan pertimbangan metodologis penggunaannya.
5. Mengenali peran komplementer perangkat lunak berbasis penjajaran dan algoritma *Expectation-Maximization* (**`Emu`**) untuk penentuan spesies pada data Nanopore 16S.

> [!TIP]
> **⚡ Inti dalam 30 Detik**: **Kraken2** adalah "mesin pencari super cepat" yang memotong sekuens DNA menjadi potongan kata pendek ($k$-mer) lalu mencocokkannya ke kamus resmi **NCBI Targeted Loci** (~8,6 MB). Jika buktinya unik, ia melabeli hingga **Spesies**; jika ambigu di antara beberapa spesies dekat, ia bersikap jujur dengan naik ke **Genus/Famili (LCA)**.

---

## 1. Algoritma Kraken2: Pencocokan Minimizer $k$-mer & LCA

Alih-alih melakukan penyejajaran sekuens penuh (*alignment*) yang memakan waktu komputasi besar, **Kraken2** menggunakan indeks $k$-mer terkompresi berbasis tabel *hash*:

```mermaid
flowchart TD
    Read["Sequencing Read Nanopore (~1.140 bp)"] --> Kmers["Ekstraksi k-mer (k=35) & Minimizer"]
    Kmers --> DB["Pencocokan ke Indeks NCBI Targeted Loci (hash.k2d)"]
    DB --> Tree["Pemetaan ke Pohon Taksonomi NCBI (taxo.k2d)"]
    Tree --> LCA{"Apakah k-mer unik pada satu takson?"}
    LCA -->|Unik / Konsisten| Spec["Ditempatkan pada cabang Spesies (rank S)"]
    LCA -->|Ambigus / Berbagi Bukti| Ances["Ditempatkan pada Lowest Common Ancestor (LCA)<br/>(Tingkat Genus, Famili, dsb.)"]
```

### Prinsip Lowest Common Ancestor (LCA)
Jika sekumpulan $k$-mer sepanjang 35 bp ditemukan identik pada beberapa spesies berkerabat dekat (misal di antara spesies dalam genus *Bacteroides* atau famili *Enterobacteriaceae*), Kraken2 tidak akan menebak secara sembarangan, melainkan menaikkan penugasan ke simpul leluhur bersama terdekat (*Lowest Common Ancestor* / LCA).

> [!TIP]
> **💡 Analogi Sederhana (Detektif Kamus yang Jujur)**: Bayangkan Anda menemukan sobekan halaman bertuliskan *"berkaki empat, berkumis, suka ikan"*. Karena cocok dengan kucing kampung maupun kucing Persia, detektif yang jujur akan melapor: *"Ini keluarga Kucing (Genus)"*—bukan asal menebak rasnya! Itulah cara kerja **LCA** pada Kraken2.

### Parameter Confidence Threshold (`--confidence 0.01`)
Secara default (`0.0`), satu kecocokan $k$-mer minimizer saja sudah dapat melabeli suatu takson. Pada data Nanopore R9.4.1 (akurasi ~90%, peluang 35-mer bebas error $(0{,}90)^{35} \approx 2{,}5\%$), kita menerapkan ambang seimbang:
```bash
kraken2 --db database_16s --confidence 0.01 --threads 2 --report sample.k2report input.fastq
```
Flag `--confidence 0.01` mensyaratkan minimal 1% dari seluruh $k$-mer informatif pada *read* tersebut harus konsisten mendukung cabang taksonomi terkait—efektif menyaring penugasan acak (*false positive*) tanpa membuang sinyal biologis R9.4.1.

---

## 2. Mengenal Database NCBI Targeted Loci 16S/18S (ONT EPI2ME)

Dalam alur kerja resmi Oxford Nanopore Technologies (EPI2ME Labs `wf-16s` dan `wf-metagenomics`), ONT menyediakan paket database Kraken2 siap pakai berbasis **NCBI Targeted Loci**:

* **Sumber Sekuens**: NCBI RefSeq Targeted Loci Project (kurasi gen ribosomal 16S prokariota dan 18S eukariota dari *type material* / strain tipe terverifikasi).
* **Format & Arsip**: `ncbi_targeted_loci_kraken2.tar.gz` (Ukuran unduhan sangat ringkas: **~8.63 MB**).
* **Pohon Taksonomi**: Menggunakan hirarki taksonomi resmi NCBI (NCBI Taxonomy IDs), sehingga seluruh entri terpetakan secara terstruktur dari Domain hingga ke **Spesies (`rank = S`)**.
* **Komponen Database**:
  - `hash.k2d`: Tabel hash pencocokan minimizer k-mer.
  - `opts.k2d`: Parameter panjang $k$ dan $m$ minimizer saat kompilasi.
  - `taxo.k2d`: Nodus, relasi induk-anak, dan nama taksonomi NCBI.
  - `database1000mers.kmer_distrib`: Model distribusi k-mer 1.000 bp untuk panjang bacaan ~1 kb.

### Pilihan Database 16S Lain untuk Kraken2 (Riset Mandiri)
Selain `ncbi_16s_18s`, beberapa alternatif indeks 16S yang umum dipakai bersama `Kraken2` antara lain:

| Database | Perkiraan Ukuran | Keunggulan Utama | Kapan Cocok Dipakai? |
|---|---|---|---|
| **NCBI Targeted Loci 16S/18S** *(dipakai di sini)* | ~8,6 MB | Sangat ringan, taksonomi NCBI hingga Spesies (`S`), resmi ONT EPI2ME | Workshop Colab & eksplorasi dual-rank cepat |
| **SILVA 138 / 138.1** | ~112–297 MB | Cakupan bakteri klinis & lingkungan sangat luas | Profil tingkat **Genus** pada sampel klinis/lingkungan |
| **RDP** *(Ribosomal Database Project)* | ~20–30 MB | Kurasi ketat prokariota, ringan | Pembanding cepat tingkat **Genus** |
| **Greengenes2** (`Greengenes2_plus` EPI2ME) | ~162 MB | Selaras dengan pohon genom utuh (*Web of Life*); populer di QIIME2 | Riset mikrobioma usus yang disandingkan dengan *shotgun* |

*(Hindari memuat database shotgun besar seperti `Standard` ~70 GB pada Google Colab gratis ber-RAM 12 GB.)*

---

## 3. Evaluasi Kritis: Kapan Identifikasi Spesies 16S Dapat Dipercaya?

Meskipun laporan Kraken2 kini menampilkan baris bertanda **`S` (Species)** seperti *Bifidobacterium longum*, *Bacteroides fragilis*, atau *Escherichia coli*, peneliti dan klinisi wajib memahami batasan biologisnya:

```text
Hirarki Keyakinan Identifikasi pada 16S rRNA Standar:
[ Level Genus ]   --> Tinggi (>90% kasus klinis; batas kemiripan ~94,5%)
[ Level Spesies ] --> Moderat hingga Tinggi (~65–83%; batas ~98,65%, bergantung klad takson & kualitas bacaan)
[ Level Galur ]   --> Tidak Memadai untuk Strain Typing Rutin (membutuhkan analisis kopi operon akurasi tinggi atau WGS/shotgun)
```

> **Landasan Pustaka**: Konsensus batas resolusi ini mengacu pada review diagnostik klinis **Janda & Abbott (2007)** (*J. Clin. Microbiol.*, [doi:10.1128/JCM.01228-07](https://doi.org/10.1128/JCM.01228-07); identifikasi >90% pada Genus vs ~65–83% pada Spesies), ambang batas kemiripan sekuens **Yarza et al. (2014)** (*Nat. Rev. Microbiol.*, [doi:10.1038/nrmicro3330](https://doi.org/10.1038/nrmicro3330); ~94,5% Genus) dan **Kim et al. (2014)** (*IJSEM*, [doi:10.1099/ijs.0.059774-0](https://doi.org/10.1099/ijs.0.059774-0); 98,65% Spesies), serta evaluasi resolusi spesies–galur *full-length* 16S oleh **Johnson et al. (2019)** (*Nat. Commun.*, [doi:10.1038/s41467-019-13036-1](https://doi.org/10.1038/s41467-019-13036-1)). Meski *full-length* 16S berakurasi sangat tinggi secara teori dapat membedakan sebagian galur lewat pola variasi antarkopi operon, pada alur *single-read* R9.4.1 + `Kraken2` maupun *strain typing* klinis (mis. virulensi/AMR yang berada di luar gen 16S), analisis galur tetap memerlukan **WGS atau *shotgun* metagenomics**.

### Empat Peringatan Ilmiah Penting:
1. **Spesies Bersekuens 16S Identik**:
   - Sekuens gen 16S rRNA dari *Escherichia coli* dan *Shigella flexneri* memiliki kemiripan >99% (seringkali identik 100%). Panggilan spesies pada kelompok ini bersifat indikatif dan tidak boleh dijadikan dasar diagnosis medis definitif.
   - Anggota kompleks *Bacillus cereus* sensu lato (*B. anthracis*, *B. cereus*, *B. thuringiensis*) memiliki gen 16S yang nyaris indistinguishable.
2. **Keterbatasan Amplikon Parsial V3–V9**:
   - Dataset praktikum kita (PRJNA637202) mencakup ~1.140 bp (V3–V9). Sekitar 350 bp di wilayah 5' (V1–V2) tidak teramplifikasi oleh primer wet-lab. Beberapa spesies bakteri justru memiliki variasi diagnostik pembeda di wilayah V1–V2.
3. **Variasi Jumlah Salinan Gen (*Copy Number Variation* / CNV)**:
   - Jumlah operon gen 16S bervariasi antarspesies (1 hingga 15 kopi). Sinyal kelimpahan sekuens mencerminkan jumlah kopi gen, bukan jumlah sel fisik secara langsung.
4. **Strategi Pelaporan Dual-Rank**:
   - Di notebook praktikum, kita menganalisis data pada **Tingkat Spesies** (untuk mengeksplorasi takson dominan) sekaligus menyediakan rekapitulasi pada **Tingkat Genus** sebagai kontrol analitis yang kokoh.

---

## 4. Alur `Kraken2 → Bracken` & Peran Komplementer `Emu`

Di banyak pipeline metagenomik, `Kraken2` sering dipasangkan dengan **Bracken** (*Bayesian Reestimation of Abundance with Kraken*). Tugas Bracken adalah mengambil *read* yang tertahan di tingkat atas (Family/Genus) oleh LCA, lalu membaginya ulang secara probabilistik ke spesies di bawahnya memakai file `database1000mers.kmer_distrib`. Pada praktikum ini kita sengaja memakai laporan `Kraken2` langsung tanpa Bracken agar perilaku LCA tetap transparan dan tidak memperbesar *false positive* akibat error *indel* R9.4.1.

Sementara itu, untuk profil tingkat spesies pada bacaan panjang Nanopore, banyak pipeline riset menggunakan pendekatan berbasis *alignment* (penjajaran sekuens penuh) alih-alih *k-mer* semata—misalnya mode `minimap2` pada **ONT EPI2ME (`wf-16s`)** atau **`Emu`** (Curry et al., 2022, *Nature Methods*):

* **Prinsip Kerja Emu**: Menggunakan penjajaran *semi-global* `minimap2` terhadap database gabungan rrnDB + NCBI, kemudian memecahkan ambiguitas multi-mapping antarspesies dekat menggunakan algoritma iteratif *Expectation-Maximization* (EM).
* **Catatan Kritis**: Tidak ada satu "standar emas" tunggal di bioinformatika 16S Nanopore—performa `Emu`, `minimap2`, maupun `Kraken2/Bracken` sangat dipengaruhi oleh kimia *flow cell* (R9.4.1 vs R10.4.1 Q20+) serta kelengkapan database rujukannya.
* **Perbandingan Praktis**:

| Pendekatan | Kecepatan | Resolusi Utama | Cocok Digunakan Saat... |
|---|---|---|---|
| **Kraken2 + NCBI Targeted Loci** *(Alur Utama Workshop)* | Sangat cepat (<1 detik/sampel) | Genus (sangat kuat) & Spesies terarah (*putative*) | Eksplorasi cepat, workshop di Google Colab, screening awal |
| **Kraken2 + Bracken (1.000 bp)** *(Pengayaan)* | Sangat cepat | Re-estimasi kelimpahan Bayesian ke tingkat Spesies/Genus | Ingin mendistribusikan ulang read LCA secara probabilistik (waspadai *over-assignment* pada R9.4.1) |
| **Alignment + EM (`Emu` / `minimap2` `wf-16s`)** *(Opsi Lanjutan)* | Sedang–Lambat (butuh RAM/CPU lebih besar) | Spesies lebih selektif (meredam error *indel* & *false positive*) | Analisis mendalam tingkat spesies di server lokal/HPC dengan validasi database |

---

## 5. Ringkasan Modul
- Kraken2 memetakan reads secara instan menggunakan pencocokan minimizer k-mer dan penugasan LCA.
- Database **NCBI Targeted Loci 16S/18S** dari ONT EPI2ME Labs berukuran sangat ringkas (~8.6 MB) dan mendukung identifikasi hingga tingkat **Spesies** (`rank = S`).
- Penugasan taksonomi tingkat spesies pada 16S harus diinterpretasikan secara bertanggung jawab dengan menyadari adanya spesies berkerabat dekat yang memiliki sekuens 16S identik.
- Analisis komparatif pada tingkat Genus tetap disediakan sebagai kontrol metodologis yang saling melengkapi.

### ✅ Cek Paham Cepat (1 Menit)
1. Jika sebuah *read* cocok dengan *Bacteroides fragilis* sekaligus *Bacteroides ovatus*, ke tingkat manakah Kraken2 LCA akan menempatkannya?
2. Mengapa label spesies *Escherichia coli* pada hasil 16S rRNA tidak boleh langsung disimpulkan pasti bukan *Shigella*?
