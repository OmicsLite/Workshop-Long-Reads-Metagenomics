# Modul 05: Visualisasi, Ekologi Komunitas & Analisis Diversity (Genus & Spesies)

## 🎯 Target Pembelajaran
Setelah menyelesaikan modul ini, peserta mampu:
1. Memahami konsep ekologi komunitas mikroba: **Alpha Diversity** (keanekaragaman di dalam sampel) dan **Beta Diversity** (perbedaan komposisi antar-sampel) pada tingkat **Genus** (kedalaman tinggi untuk statistik) dan eksplorasi **Spesies**.
2. Menghitung dan menginterpretasikan metrik keanekaragaman: **Observed Taxa**, **Shannon Index ($H'$)**, dan **Simpson Index ($1-D$)**.
3. Menjelaskan perlunya standardisasi kedalaman sampling melalui **Rarefaction** sebelum membandingkan keragaman antar-sampel.
4. Menghitung jarak ketidaksamaan komposisi menggunakan **Bray–Curtis Dissimilarity** dan memvisualisasikannya via **Principal Coordinates Analysis (PCoA)**.
5. Menguji signifikansi perbedaan struktur komunitas antar-kelompok (persalinan Vaginal vs Sesar) dengan **PERMANOVA** serta mengoreksi pengujian hipotesis multipel dengan **Benjamini–Hochberg False Discovery Rate (FDR)**.
6. Mengoperasikan visualisasi radial bertingkat interaktif menggunakan **Krona Plot** berbasis taksonomi NCBI.

> [!TIP]
> **⚡ Inti dalam 30 Detik**: Jangan takut dengan istilah statistik! Ingat 4 gambar besar saja:
> - **Krona Plot** = diagram lingkaran interaktif untuk *melihat isi satu sampel*.
> - **Rarefaction** = *menyamakan jumlah tarikan jaring* agar perbandingan antar-sampel adil.
> - **Alpha Diversity** = seberapa *beragam isi di dalam satu sampel* (kaya jenis & merata).
> - **Beta Diversity + PCoA + PERMANOVA** = *peta jarak* untuk melihat apakah kelompok Vaginal dan Sesar benar-benar terpisah!

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

Untuk membandingkan kekayaan takson secara adil, kita melakukan **Rarefaction**:
- Mengambil subsampel acak (*sampling without replacement*) dari setiap sampel hingga mencapai kedalaman konstan yang sama ($N = \text{kedalaman terkecil}$).
- Pada notebook kita, ini dihitung secara matematis menggunakan distribusi hipergeometrik multivariat (`multivariate_hypergeometric`) dengan *seed* acak tetap (`SEED = 42`) untuk menjamin reprodusibilitas 100%.
- **Catatan Praktikum (Genus vs Spesies)**: Karena algoritma LCA Kraken2 pada data R9.4.1 menempatkan ribuan *reads* per sampel di tingkat **Genus** dan hanya sebagian kecil di tingkat Spesies, langkah rarefaction & uji ekologi komunitas dijalankan pada matriks **Genus** agar kedalaman sampling tetap tinggi dan bebas bias, sedangkan eksplorasi **Spesies** disajikan secara paralel pada grafik komposisi & Krona Plot.

> [!TIP]
> **💡 Analogi Sederhana (Menjaring Ikan di Dua Kolam)**: Jika Anda menjaring 100 kali di Kolam A dan hanya 10 kali di Kolam B, wajar bila Kolam A tampak punya lebih banyak jenis ikan! **Rarefaction** menyamakan jumlah tarikan jaring menjadi sama-sama 10 kali agar perbandingan kekayaan jenis benar-benar adil.

---

## 3. Alpha Diversity: Mengukur Keragaman di Dalam Satu Sampel

Alpha diversity mengukur keanekaragaman komunitas mikroba pada satu ekosistem sampel menggunakan **5 indeks yang semuanya searah** (makin tinggi angka = makin beragam/merata):

```mermaid
flowchart LR
    A["Alpha Diversity (Genus)"] --> B["Observed Genera<br/>Kekayaan murni (Richness)"]
    A --> C["Pielou Evenness (J')<br/>Kemerataan murni (0–1)"]
    A --> D["Shannon Index (H')<br/>Gabungan Richness & Evenness"]
    A --> E["Simpson (1 - D)<br/>Peka terhadap takson dominan"]
    A --> F["Inverse Simpson (1 / D)<br/>Jumlah efektif genus dominan"]
```

### Rumus Matematis Kunci
$$H' = -\sum_{i=1}^{S} p_i \ln(p_i) \qquad J' = \frac{H'}{\ln(S)} \qquad \text{Inverse Simpson} = \frac{1}{\sum_{i=1}^{S} p_i^2}$$
di mana $p_i$ adalah proporsi relatif takson ke-$i$ dan $S$ adalah jumlah takson unik (*Observed*).

> [!TIP]
> **💡 Analogi Sederhana (Dua Keranjang Buah)**: Keranjang A dan B sama-sama berisi **5 jenis buah** (*Observed* = 5). Namun Keranjang A isinya 96 apel + 1 jeruk + 1 mangga + 1 pisang + 1 anggur (*Pielou* rendah $\rightarrow$ monopoli), sedangkan Keranjang B isinya 20 masing-masing buah (*Pielou* = 1,0 dan *Inverse Simpson* = 5,0)!

### 📏 *Rule of Thumb* & Justifikasi 5 Indeks Alpha Diversity
| Indeks | Rentang | Jika Tinggi | Jika Rendah | Justifikasi Ilmiah |
|---|---|---|---|---|
| **Observed Genera** | `1 – ratusan` | Banyak jenis berbeda hadir (bayi 3 bln wajar 15–40; dewasa 80–150+) | Sedikit jenis terdeteksi | Kekayaan murni (*Richness*) |
| **Pielou Evenness ($J'$)** | `0 – 1` | Mendekati `1`: porsi antar-bakteri sangat merata | Mendekati `0`: 1–2 bakteri memonopoli sampel | Kemerataan murni (*Evenness*) terpisah dari jumlah jenis |
| **Shannon ($H'$)** | `0 – ~4+` | Kaya jenis **dan** merata (`> 2,5` tinggi; `1–2` moderat/bayi) | Miskin jenis **atau** dimonopoli 1–2 takson (`< 1,0`) | Standar paling populer di jurnal klinis |
| **Simpson ($1-D$)** | `0 – 1` | Mendekati `1` (`0,7–0,9`): tidak ada monopoli | Mendekati `0` (`< 0,4`): didominasi 1 takson | Peka bakteri dominan (*awas di jurnal: `Simpson D` berlawanan arah!*) |
| **Inverse Simpson ($1/D$)** | `1 – S` | Makin besar = makin banyak genus dominan setara | Mendekati `1`: hanya 1 genus dominan | Tidak cepat jenuh di 0,99 & punya arti fisik langsung (*effective number of taxa*) |

> [!NOTE]
> **Kenapa `Chao1` & `Faith's PD` tidak kita paksakan di sini?** `Chao1` mengandalkan jumlah *singleton* (takson dengan tepat 1 read), sedangkan kita sudah memfilter *noise* `< 10` read di matriks Genus sehingga *singleton*-nya nol. Adapun `Faith's PD` membutuhkan pohon filogenetik dari *multiple sequence alignment*, yang tidak dihasilkan oleh klasifikasi $k$-mer `Kraken2`.
>
> **Hati-hati Mitos "Makin Tinggi Makin Sehat"**: Pada bayi ASI usia 3 bulan, Alpha Diversity yang rendah–moderat justru sering **fisiologis & sehat** karena usus idealnya didominasi *Bifidobacterium* (sama halnya dengan mikrobioma vagina sehat yang didominasi *Lactobacillus*). Selain itu, dua kelompok bisa memiliki **Alpha Diversity sama**, namun **Beta Diversity berbeda jauh** jika jenis bakterinya berlainan total!

---

## 4. Beta Diversity: Perbandingan Komposisi Antar-Kelompok

Beta diversity mengukur tingkat ketidaksamaan (*dissimilarity*) struktur komunitas antar-sampel atau antar-kelompok perlakuan klinis.

### A. Bray–Curtis (Kelimpahan %) vs Jaccard (Ada / Tidak Ada)
1. **Bray–Curtis Dissimilarity** — memperhitungkan **proporsi/kelimpahan** tiap takson antara dua sampel ($j$ dan $k$):

$$BC_{jk} = \frac{\sum_{i} |C_{ij} - C_{ik}|}{\sum_{i} (C_{ij} + C_{ik})}$$

2. **Jaccard Dissimilarity** — hanya melihat **ada atau tidak adanya (*presence/absence*)** takson, memberi bobot setara pada bakteri langka maupun dominan.

* Nilai **0**: Kedua sampel identik persis.
* Nilai **1**: Kedua sampel sama sekali tidak berbagi satu pun takson (*completely disjoint*).
* **Kenapa menghitung keduanya?** Jika **Bray–Curtis tinggi tetapi Jaccard rendah**, artinya daftar jenis bakterinya sebenarnya banyak yang sama, tetapi **porsi (%) bakteri dominannya berubah drastis**! *(Catatan untuk pembaca jurnal: metrik **Weighted / Unweighted UniFrac** memiliki prinsip serupa, namun menambahkan jarak evolusi dari pohon filogenetik).*

### B. Principal Coordinates Analysis (PCoA)
PCoA adalah teknik reduksi dimensi (*ordination*) yang memproyeksikan matriks jarak Bray–Curtis ke dalam ruang 2 dimensi (PCoA1 dan PCoA2):
- Sampel yang posisinya berdekatan pada plot memiliki komunitas mikroba yang mirip.
- Persentase pada sumbu menunjukkan proporsi variasi data yang dapat dijelaskan oleh sumbu koordinat tersebut.

---

## 5. Uji Statistik Komparatif: PERMANOVA & Koreksi Benjamini–Hochberg (FDR)

### A. PERMANOVA (Permutational Multivariate Analysis of Variance)
Untuk menguji secara formal apakah kelompok bayi persalinan **Vaginal** memiliki struktur komunitas usus yang berbeda signifikan secara statistik dari kelompok **Sesar** (baik pada jarak Bray–Curtis maupun Jaccard):
- PERMANOVA menghitung rasio variansi antar-kelompok terhadap variansi di dalam kelompok (nilai **Pseudo-$F$**).
- Nilai signifikansi ($p$-value) diperoleh bukan dari asumsi distribusi normal, melainkan dengan mengacak label sampel (*permutation test*, 999 kali permutasi).

### B. Koreksi Benjamini–Hochberg untuk Pengujian Takson Ganda
Saat menguji apakah kelimpahan setiap genus/takson berbeda antar kedua kelompok, kita melakukan uji statistik independen (Mann–Whitney U) pada puluhan takson secara bersamaan.
- Jika kita menguji 50 takson dengan ambang $\alpha = 0.05$, kita diekspektasikan mendapatkan temuan positif palsu (*false discovery*) murni karena kebetulan acak.
- **Koreksi Benjamini–Hochberg (BH)** mengontrol *False Discovery Rate* (FDR), mengubah $p$-value menjadi **$q$-value**. Takson dianggap berbeda bermakna jika nilai $q < 0.05$.

---

## 6. Ringkasan Modul
- Krona Plot merender hierarki taksonomi dari Domain hingga Spesies dalam diagram lingkaran interaktif.
- Standardisasi kedalaman via Rarefaction wajib dilakukan sebelum membandingkan kekayaan takson.
- Alpha diversity mengukur keanekaragaman internal sampel, sedangkan Beta diversity (Bray–Curtis PCoA — bayangkan seperti **peta jarak antar-kota**) memvisualisasikan pemisahan profil mikrobioma antar-kelompok persalinan.
- PERMANOVA dan koreksi FDR Benjamini–Hochberg memberikan landasan inferensi statistik yang kokoh agar kita tidak tertipu oleh temuan positif palsu akibat kebetulan acak.

### ✅ Cek Paham Cepat (1 Menit)
1. Jika dua sampel memiliki nilai **Bray–Curtis Dissimilarity = 0,05**, apakah komposisi mikrobioma keduanya sangat mirip atau sangat berbeda?
2. Mengapa saat menguji 50 takson sekaligus kita wajib memakai nilai **$q$-value (FDR)**, bukan sekadar $p < 0,05$ mentah?
