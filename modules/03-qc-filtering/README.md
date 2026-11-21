# Modul 03: Kendali Mutu & Penyaringan Sekuens (QC & Filtering)

## 🎯 Target Pembelajaran
Setelah menyelesaikan modul ini, peserta mampu:
1. Menjelaskan struktur 4 baris format file **FASTQ** dan hubungan matematis **Phred Quality Score ($Q$)** dengan probabilitas kesalahan ($P$).
2. Mengonversi karakter ASCII ke skor Phred pada skala standar Phred+33 secara akurat.
3. Menjalankan evaluasi kendali mutu *long-read* komprehensif menggunakan **NanoPlot** dan **SeqKit**.
4. Memilih parameter pembersihan yang tepat (panjang minimum, panjang maksimum, dan rata-rata skor Phred) menggunakan utilitas modern **Chopper** (atau **SeqKit**).
5. Memahami batasan filtering panjang dalam mendeteksi molekul *chimera* PCR.

> [!TIP]
> **⚡ Inti dalam 30 Detik**: Prinsip bioinformatika nomor 1 adalah **"Garbage In, Garbage Out"**. Sebelum menebak nama bakteri, kita wajib melihat mutu data mentah (`SeqKit` & `NanoPlot`), lalu menyaring sekuens yang terlalu pendek, terlalu panjang, atau terlalu buram menggunakan **`Chopper`** (`1.000–1.700 bp`, `Q ≥ 10`).

---

## 1. Memahami Struktur FASTQ & Skor Kualitas Phred

Data hasil basecalling Nanopore disimpan dalam format teks standar **FASTQ**. Setiap satu *read* biologis direpresentasikan oleh 4 baris berulang:

```text
@SRR11923954.1 1 length=1148
TGGCTCAGGATGAACGCTGGCGGCGTGCCTAACACATGCAAGTCGAACGAAGCA...
+
*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_...
```

1. **Baris 1 (`@`)**: Header identitas unik read (Run ID, read number, panjang, informasi flowcell).
2. **Baris 2**: Sekuens basa nitrogen yang teridentifikasi (`A`, `C`, `G`, `T`).
3. **Baris 3 (`+`)**: Baris pembatas (separator opsional dapat memuat ulang header).
4. **Baris 4**: Karakter ASCII yang mengodekan skor kualitas mutu (*Phred quality score*) untuk setiap basa di Baris 2.

### Definisi Matematis Phred Quality Score ($Q$)
Skor Phred ($Q$) adalah transformasi logaritmik dari probabilitas kesalahan identifikasi basa ($P_{error}$):

$$Q = -10 \cdot \log_{10}(P_{error}) \quad \Longleftrightarrow \quad P_{error} = 10^{-\frac{Q}{10}}$$

### Standar Pengodean Phred+33 (Sanger / Nanopore Standar)
Untuk menghemat ruang file, nilai numerik $Q$ disimpan sebagai satu karakter ASCII dengan rumus:

$$\text{ASCII Code} = Q + 33$$

| Phred Score ($Q$) | Akurasi Basa ($1 - P$) | Peluang Error ($P$) | Nilai ASCII ($Q+33$) | Karakter ASCII Phred+33 |
|:---:|:---:|:---:|:---:|:---:|
| **Q10** | 90.0% | 1 dalam 10 (10%) | $10 + 33 = 43$ | `+` |
| **Q12** | 93.7% | 1 dalam 16 (≈6.3%) | $12 + 33 = 45$ | `-` |
| **Q20** | 99.0% | 1 dalam 100 (1%) | $20 + 33 = 53$ | `5` |
| **Q30** | 99.9% | 1 dalam 1.000 (0.1%) | $30 + 33 = 63$ | `?` |
| **Q40** | 99.99% | 1 dalam 10.000 (0.01%) | $40 + 33 = 73$ | `I` |

> [!CAUTION]
> Jangan tertukar: karakter ASCII `?` mewakili **Q30** (ASCII 63), bukan Q20 (ASCII 53 = karakter `5`). Pada data Nanopore R9.4.1 (seperti PRJNA637202), sebagian besar karakter baris 4 berada di kisaran `+` s.d. `1` ($Q10$–$Q16$).

> [!TIP]
> **💡 Cara Cepat Menghafal Q-Score (Tanpa Menghitung Logaritma!)**:
> Setiap nilai $Q$ naik **+10**, peluang error turun **10× lipat**:
> - **Q10** = 1 salah dari 10 (akurasi 90%)
> - **Q20** = 1 salah dari 100 (akurasi 99%)
> - **Q30** = 1 salah dari 1.000 (akurasi 99,9%)

---

## 2. Pemeriksaan Mutu Menyeluruh dengan SeqKit & NanoPlot

Sebelum melakukan filtering, bioinformatikawan wajib memeriksa distribusi data awal:

### A. Statistik Cepat dengan `SeqKit`
`SeqKit` adalah program berbasis biner Go yang sangat cepat untuk merangkum file FASTA/FASTQ:
```bash
seqkit stats -T raw_data/*.fastq.gz
```
Perintah ini menghasilkan tabel berisi: jumlah total *reads* (`num_seqs`), total pasangan basa (`sum_len`), panjang minimum (`min_len`), panjang rerata (`avg_len`), dan panjang maksimum (`max_len`).

### B. Visualisasi Statistik Menyeluruh dengan `NanoPlot`
`NanoPlot` (bagian dari ekosistem NanoPack) membaca file FASTQ dan menghasilkan laporan HTML interaktif:
* **Histogram Distribusi Panjang Read**: Memeriksa apakah puncak amplikon berada di ≈1.140 bp (target V3–V9).
* **Plot Bivariate Panjang vs Kualitas (*Bivariate dot/hex plot*)**: Menunjukkan korelasi antara panjang fragmen dengan rata-rata skor Phred.
* **Kurva Kualitas Kumulatif**: Persentase data yang memiliki rata-rata kualitas $\ge Q10$, $\ge Q12$, atau $\ge Q15$.

---

## 3. Pemilihan Alat Filtering: Mengapa Menggunakan `Chopper`?

Pada data amplikon 16S, kita perlu menyaring:
1. **Reads Terlalu Pendek (<1.000 bp)**: Fragmen DNA terdegradasi, produk *primer-dimer*, atau amplifikasi non-spesifik.
2. **Reads Terlalu Panjang (>1.700 bp)**: Kemungkinan fusi dua fragmen molekul DNA (*concatemer*) atau molekul DNA non-target.
3. **Reads Rerata Kualitas Rendah (<Q10)**: Membawa terlalu banyak error per-basa yang memicu salah penempatan taksonomi.

### Evaluasi Tool: `Chopper` vs `Filtlong`
* **`Filtlong` (v0.2.1)**: Dirancang oleh Ryan Wick untuk *de novo genome assembly*, di mana algoritma memprioritaskan read terpanjang. Akibatnya, `filtlong` **tidak menyediakan argumen batas panjang maksimum (`--max_length`)**, dan argumen `--min_mean_q` dihitung sebagai persentase (0–100) bukan skor Phred.
* **`Chopper` (NanoPack2)**: Dikembangkan dalam bahasa Rust oleh Wouter De Coster sebagai penerus modern `NanoFilt`. `Chopper` mendukung *streaming* pipa cepat, hard length cutoff (`-l` dan `--maxlength`), serta ambang kualitas Phred (`-q`).

### Perintah Standar Chopper di Workshop:
```bash
gunzip -c input.fastq.gz | chopper -q 10 -l 1000 --maxlength 1700 | gzip -c > filtered.fastq.gz
```

*Alternatif dengan SeqKit (tanpa instalasi tambahan):*
```bash
seqkit seq -m 1000 -M 1700 -Q 10 input.fastq.gz -o filtered.fastq.gz
```

---

## 4. Apakah Filter Panjang Mampu Membuang Molekul Chimera?

> [!WARNING]
> **Mitos Umum**: "Filtering panjang 1.000–1.700 bp otomatis membersihkan chimera."
> **Fakta Ilmiah**: Selama siklus PCR amplikon, polimerase DNA yang terhenti di tengah jalan dapat berfungsi sebagai *primer* pada molekul cetakan dari spesies bakteri lain pada siklus berikutnya. Produk hibrida ini disebut **PCR Chimera**. Banyak molekul chimera memiliki **panjang yang persis normal (≈1.140–1.500 bp)** sehingga lolos 100% dari penyaringan ukuran panjang!
> Deteksi chimera sejati membutuhkan algoritma pendeteksi bimer/multimer (seperti `vsearch --uchime_denovo`) atau model *Expectation-Maximization* berbasis referensi (`Emu`).

---

## 5. Ringkasan Modul
- Format FASTQ menyimpan ID sekuens, basa DNA, dan kualitas per-basa Phred+33.
- Nilai $Q10$ setara dengan akurasi 90%, $Q20$ setara 99%, dan $Q30$ setara 99.9%.
- `NanoPlot` dan `SeqKit` memberikan gambaran menyeluruh statistik kualitas data mentah.
- `Chopper` bekerja seperti **ayakan saringan biji kopi**: hanya meloloskan sekuens dengan ukuran pas (1.000–1.700 bp) dan mutu memadai ($Q \ge 10$).
- Filter panjang tidak dapat mendeteksi chimera berukuran normal; deteksi chimera memerlukan pendekatan algoritmik bioinformatika lanjutan.

### ✅ Cek Paham Cepat (1 Menit)
1. Tanpa melihat rumus, berapakah akurasi persentase untuk skor **Q20** dan **Q30**?
2. Jika kita sudah memfilter panjang ke 1.000–1.700 bp, apakah sampel kita dijamin 100% bebas *PCR chimera*? Mengapa?
