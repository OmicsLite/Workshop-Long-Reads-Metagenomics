# 📄 Lembar Contekan (Cheatsheet) Metagenomik 16S Nanopore

Gunakan lembar contekan ini sebagai referensi sintaks baris perintah (*command line interface* / CLI) terverifikasi selama praktikum dan penelitian mandiri Anda.

---

## 1. Kendali Mutu & Statistik Data Mentah

### SeqKit (Rangkuman Statistik FASTQ)
```bash
# Menampilkan tabel statistik panjang dan jumlah read dalam format tabulasi (-T)
seqkit stats -T raw/*.fastq.gz

# Mengambil 5.000 reads pertama untuk demonstrasi cepat
seqkit head -n 5000 sample.fastq.gz | gzip -c > sample_5k.fastq.gz
```

### NanoPlot (Visualisasi Mutu Sekuens Panjang)
```bash
# Menghasilkan laporan mutu HTML interaktif dari file FASTQ
NanoPlot --fastq sample.fastq.gz \
         --outdir qc_report/ \
         --plots dot \
         --threads 2
```

---

## 2. Penyaringan Sekuens (Quality & Length Filtering)

### Chopper (Penyaring Terstandar NanoPack)
```bash
# Menyaring reads dengan panjang 1.000-1.700 bp dan rerata skor Phred >= Q10
gunzip -c raw_reads.fastq.gz | \
  chopper -q 10 -l 1000 --maxlength 1700 | \
  gzip -c > filtered_reads.fastq.gz
```
* `-q`: Ambang batas rata-rata skor Phred minimum ($Q$).
* `-l`: Panjang sekuens minimum (1.000 bp untuk amplikon V3–V9).
* `--maxlength`: Panjang sekuens maksimum (1.700 bp untuk menyingkirkan *concatemer*).

### Alternatif Cepat Menggunakan SeqKit
```bash
# Penyaringan langsung tanpa dekompresi pipa
seqkit seq -m 1000 -M 1700 -Q 10 raw_reads.fastq.gz -o filtered_reads.fastq.gz
```

---

## 3. Klasifikasi Taksonomi

### Kraken2 (Klasifikasi K-mer Cepat Tingkat Spesies & Genus)
```bash
# Klasifikasi reads terhadap database resmi ONT EPI2ME NCBI Targeted Loci 16S/18S
kraken2 --db database_16s \
        --threads 2 \
        --confidence 0.01 \
        --output sample.kraken.tsv \
        --report sample.k2report \
        filtered_reads.fastq.gz
```
* `--db`: Path menuju direktori database referensi (memuat `hash.k2d`, `opts.k2d`, `taxo.k2d`).
* `--confidence`: Ambang batas proporsi k-mer minimizer pendukung (`0.01` = 1%, optimal untuk data R9.4.1).
* `--report`: Output laporan ringkasan taksonomi hirarkis (`.k2report`).

### Parsing Laporan Kraken2 Tingkat Spesies (`S`) & Genus (`G`)
```bash
# Mengekstrak takson tingkat Spesies (rank == 'S')
awk -F'\t' '$4 == "S" {print $2 "\t" $6}' sample.k2report | sed 's/^[ \t]*//' > species_counts.tsv

# Mengekstrak takson tingkat Genus (rank == 'G')
awk -F'\t' '$4 == "G" {print $2 "\t" $6}' sample.k2report | sed 's/^[ \t]*//' > genus_counts.tsv
```

### Bracken (Opsi Pengayaan: Re-estimasi Bayesian 1.000 bp)
```bash
# Menjalankan estimasi kelimpahan spesies dengan model 1.000 bp
bracken -d database_16s \
        -i sample.k2report \
        -o sample.bracken_species.tsv \
        -r 1000 \
        -l S \
        -t 10
```
* `-r 1000`: Panjang rata-rata sekuens (≈1.000–1.140 bp).
* `-l S`: Tingkat taksonomi target (`S` untuk Spesies, `G` untuk Genus).
* `-t 10`: Ambang batas deteksi minimum reads.

### Emu (Opsi Lanjutan Berbasis Alignment + Expectation-Maximization)
```bash
# Menjalankan profil tingkat spesies menggunakan penjajaran minimap2 + algoritma EM
emu abundance sample.fastq \
              --db /path/to/emu_database \
              --threads 4 \
              --N 20 \
              --output-dir emu_results/
```
* `--N 20`: Menilai maksimal 20 secondary alignments per read (menghemat konsumsi RAM).

---

## 4. Visualisasi Krona Plot

### Pembuatan Berkas Krona HTML dari Laporan Kraken2
```bash
# 1. Konversi hierarki nama teks k2report menjadi format input Krona via Python script
# (Menghasilkan file teks dua kolom: [jumlah reads] \t [Path taksonomi])

# 2. Render diagram lingkaran interaktif bertingkat
ktImportText sample.krona.tsv -o sample.krona.html
```

---

## 5. Struktur Kolom Laporan Kraken2 (`.k2report`)
Setiap baris file `.k2report` memiliki struktur 6 kolom terpisah tabulasi:
1. **`percent`**: Persentase *reads* yang tercakup pada atau di bawah takson ini.
2. **`clade_reads`**: Jumlah total *reads* yang diklasifikasikan pada takson ini atau keturunannya.
3. **`direct_reads`**: Jumlah *reads* yang diklasifikasikan secara spesifik persis pada takson ini.
4. **`rank`**: Tingkat taksonomi (`U`=Unclassified, `R`=Root, `D`=Domain, `P`=Phylum, `C`=Class, `O`=Order, `F`=Family, `G`=Genus, `S`=Species).
5. **`taxid`**: ID Taksonomi (NCBI TaxID atau ID internal database referensi).
6. **`name`**: Nama ilmiah takson (dengan indentasi spasi yang menunjukkan kedalaman hirarki).

---

## 6. Patokan Cepat Indeks Diversity (Alpha & Beta)

| Indeks | Kategori | Rentang | Arah Baca |
|---|---|---|---|
| **Observed Genera** | Alpha (*Richness*) | `1 – ratusan` | Makin tinggi = makin banyak jenis |
| **Pielou Evenness ($J'$)** | Alpha (*Evenness*) | `0 – 1` | Mendekati `1` = makin merata |
| **Shannon ($H'$)** | Alpha (*Richness + Evenness*) | `0 – ~4+` | Makin tinggi = kaya & merata |
| **Simpson ($1 - D$)** | Alpha (Dominansi terbalik) | `0 – 1` | Mendekati `1` = tidak dimonopoli 1 takson |
| **Inverse Simpson ($1/D$)** | Alpha (*Effective taxa*) | `1 – S` | Jumlah efektif genus dominan yang merata |
| **Bray–Curtis** | Beta (berbasis kelimpahan %) | `0 – 1` | `0` = identik; `1` = berbeda total |
| **Jaccard** | Beta (ada / tidak ada) | `0 – 1` | `0` = daftar genus sama; `1` = berbeda total |

---

## 7. Struktur Folder Output Notebook (`results/`)

```text
results/
├── 01_qc/                      # SeqKit stats, qc_summary.tsv, NanoPlot HTML
├── 02_classification/          # classification_summary.tsv, reports/, per_read/
├── 03_abundance/               # tables/ (Genus, Spesies, LCA depth) & figures/
├── 04_krona/                   # html/ (Krona interaktif) & tsv/
├── 05_diversity/               # alpha/ (5 indeks + boxplot) & beta/ (Bray-Curtis, Jaccard, PCoA, PERMANOVA)
├── 06_differential_abundance/  # genus_group_tests.tsv (BH-FDR) & species_group_summary.tsv
└── run_manifest.json           # Jejak audit parameter, timestamp, & SHA-256 database
```
