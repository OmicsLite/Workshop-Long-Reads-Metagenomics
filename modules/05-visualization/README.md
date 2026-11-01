# Modul 05: Visualisasi, Ekologi Komunitas & Analisis Diversity (Tingkat Spesies)

## 🎯 Target Pembelajaran
Setelah menyelesaikan modul ini, peserta mampu:
1. Memahami konsep ekologi komunitas mikroba: **Alpha Diversity** (keanekaragaman di dalam sampel) dan **Beta Diversity** (perbedaan komposisi antar-sampel) pada **tingkat Spesies** (dengan kontrol komparatif Genus).
2. Menghitung dan menginterpretasikan metrik keanekaragaman: **Observed Species**, **Shannon Index ($H'$)**, dan **Simpson Index ($1-D$)**.
3. Menjelaskan perlunya standardisasi kedalaman sampling melalui **Rarefaction** sebelum membandingkan keragaman spesies antar-sampel.
4. Menghitung jarak ketidaksamaan komposisi spesies menggunakan **Bray–Curtis Dissimilarity** dan memvisualisasikannya via **Principal Coordinates Analysis (PCoA)**.
5. Menguji signifikansi perbedaan struktur komunitas antar-kelompok (persalinan Vaginal vs Sesar) dengan **PERMANOVA** serta mengoreksi pengujian hipotesis multipel dengan **Benjamini–Hochberg False Discovery Rate (FDR)**.
6. Mengoperasikan visualisasi radial bertingkat interaktif menggunakan **Krona Plot** berbasis taksonomi NCBI.

---

## 1. Visualisasi Interaktif Komposisi Taksonomi dengan Krona Plot

Menyajikan data ribuan baris taksonomi dalam bentuk tabel teks sangat sulit dipahami oleh klinisi. **Krona Plot** menyediakan visualisasi bagan lingkaran radial bertingkat (*multi-layered pie chart*):

* **Lingkaran Terdalam**: Mewakili domain dan filum (mis. *Bacteroidota*, *Bacillota*, *Actinomycetota*).
* **Lingkaran Terluar**: Merinci hingga ke tingkat genus dan **spesies**.
* **Fitur Interaktif**: Pengguna dapat mengklik sektor tertentu untuk memperbesar (*zoom-in*), menelusuri proporsi spesies dominan, dan mencari takson tertentu secara instan di peramban web.

### Mengapa Menggunakan `ktImportText`?
Karena file laporan Kraken2 (`.k2report`) memuat struktur hirarki indentasi teks yang lengkap dari Domain hingga Spesies, kita mengekstrak jalur nama taksonomi langsung menjadi format input `ktImportText`. Pendekatan ini memastikan diagram Krona ter-render 100% konsisten dengan pohon taksonomi NCBI tanpa ketergantungan pada file dump eksternal.

---

## 2. Standardisasi Kedalaman Sekuensing: Mengapa Perlu Rarefaction?

Dalam percobaan sekuensing, jumlah *reads* yang diperoleh per sampel hampir selalu berbeda-beda (mis. Sampel V01 menghasilkan ~36.000 *reads*, sedangkan Sampel C06 menghasilkan ~26.000 *reads*).

```text
Masalah Kedalaman Tidak Rata:
Sampel A (36.000 reads) = Mendeteksi 45 spesies (peluang menemukan spesies langka lebih tinggi)
Sampel B (26.000 reads) = Mendeteksi 30 spesies (bukan karena kurang kaya, tapi kurang disampling!)
```

Untuk membandingkan kekayaan spesies secara adil, kita melakukan **Rarefaction**:
- Mengambil subsampel acak (*sampling without replacement*) dari setiap sampel hingga mencapai kedalaman konstan yang sama ($N = \text{kedalaman terkecil}$).
- Pada notebook kita, ini dihitung secara matematis menggunakan distribusi hipergeometrik multivariat (`multivariate_hypergeometric`) dengan *seed* acak tetap (`SEED = 42`) untuk menjamin reprodusibilitas 100%.

---

## 3. Alpha Diversity: Mengukur Keragaman Spesies di Dalam Sampel

Alpha diversity mengukur keanekaragaman komunitas mikroba pada satu ekosistem sampel:

```mermaid
flowchart LR
    A["Alpha Diversity (Spesies)"] --> B["Observed Species (Richness)<br/>Jumlah spesies unik yang terdeteksi"]
    A --> C["Shannon Index (H')<br/>Menggabungkan Richness & Evenness (kemerataan)"]
    A --> D["Simpson Diversity (1 - D)<br/>Probabilitas dua read acak berasal dari spesies berbeda"]
```

### Rumus Matematis Shannon Index ($H'$)
$$H' = -\sum_{i=1}^{S} p_i \ln(p_i)$$
di mana $p_i$ adalah proporsi relatif spesies ke-$i$. Nilai $H'$ yang tinggi menunjukkan komunitas yang tidak hanya memiliki banyak jenis spesies, melainkan kelimpahannya juga merata (tidak dimonopoli oleh satu spesies dominan saja).

---

## 4. Beta Diversity: Perbandingan Komposisi Spesies Antar-Kelompok

Beta diversity mengukur tingkat ketidaksamaan (*dissimilarity*) struktur komunitas antar-sampel atau antar-kelompok perlakuan klinis.

### A. Bray–Curtis Dissimilarity
Metrik ketidaksamaan yang mengukur perbedaan kelimpahan spesies antara dua sampel ($j$ dan $k$):

$$BC_{jk} = \frac{\sum_{i} |C_{ij} - C_{ik}|}{\sum_{i} (C_{ij} + C_{ik})}$$

* Nilai **0**: Kedua sampel memiliki komposisi dan kelimpahan spesies yang identik persis.
* Nilai **1**: Kedua sampel tidak berbagi satu pun spesies yang sama (*completely disjoint*).

### B. Principal Coordinates Analysis (PCoA)
PCoA adalah teknik reduksi dimensi (*ordination*) yang memproyeksikan matriks jarak Bray–Curtis ke dalam ruang 2 dimensi (PCoA1 dan PCoA2):
- Sampel yang posisinya berdekatan pada plot memiliki komunitas spesies yang mirip.
- Persentase pada sumbu menunjukkan proporsi variasi data yang dapat dijelaskan oleh sumbu koordinat tersebut.

---

## 5. Uji Statistik Komparatif: PERMANOVA & Koreksi Benjamini–Hochberg (FDR)

### A. PERMANOVA (Permutational Multivariate Analysis of Variance)
Untuk menguji secara formal apakah kelompok bayi persalinan **Vaginal** memiliki struktur komunitas spesies usus yang berbeda signifikan secara statistik dari kelompok **Sesar**:
- PERMANOVA menghitung rasio variansi antar-kelompok terhadap variansi di dalam kelompok (nilai **Pseudo-$F$**).
- Nilai signifikansi ($p$-value) diperoleh bukan dari asumsi distribusi normal, melainkan dengan mengacak label sampel (*permutation test*, 999 kali permutasi).

### B. Koreksi Benjamini–Hochberg untuk Pengujian Spesies Ganda
Saat menguji apakah kelimpahan setiap spesies berbeda antar kedua kelompok, kita melakukan uji statistik independen (Mann–Whitney U) pada puluhan hingga ratusan spesies secara bersamaan.
- Jika kita menguji 50 spesies dengan ambang $\alpha = 0.05$, kita diekspektasikan mendapatkan temuan positif palsu (*false discovery*) murni karena kebetulan acak.
- **Koreksi Benjamini–Hochberg (BH)** mengontrol *False Discovery Rate* (FDR), mengubah $p$-value menjadi **$q$-value**. Spesies dianggap berbeda bermakna jika nilai $q < 0.05$.

---

## 6. Ringkasan Modul
- Krona Plot merender hierarki taksonomi dari Domain hingga Spesies dalam diagram lingkaran interaktif.
- Standardisasi kedalaman via Rarefaction wajib dilakukan sebelum membandingkan kekayaan spesies.
- Alpha diversity mengukur keanekaragaman internal sampel, sedangkan Beta diversity (Bray–Curtis PCoA) memvisualisasikan pemisahan profil spesies antar-kelompok persalinan.
- PERMANOVA dan koreksi FDR Benjamini–Hochberg memberikan landasan inferensi statistik yang kokoh untuk mengidentifikasi spesies yang berbeda bermakna antar-kelompok.
