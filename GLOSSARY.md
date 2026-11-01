# 📖 Kamus Istilah (Glossary)

Gunakan kamus istilah ini untuk memahami terminologi teknis bioinformatika, teknologi sekuensing Oxford Nanopore, dan analisis ekologi mikrobioma yang digunakan sepanjang workshop ini.

---

## A
* **Abundance (Kelimpahan)**: Jumlah atau proporsi takson mikroba tertentu yang terdeteksi di dalam suatu sampel dibandingkan dengan keseluruhan populasi sekuens mikroba.
* **Actinomycetota**: Nama filum bakteri resmi menurut ICNP 2021 (sebelumnya dikenal sebagai *Actinobacteria*), mencakup genus komensal usus penting seperti *Bifidobacterium*.
* **Alpha Diversity**: Keanekaragaman komunitas mikroba di dalam satu sampel tunggal, mencakup kekayaan takson (*richness*) dan kemerataan distribusi (*evenness*).
* **Amplicon Sequencing**: Metode sekuensing yang menargetkan satu penanda genetik spesifik (seperti gen 16S rRNA untuk bakteri) yang telah diamplikasi melalui reaksi PCR sebelum sekuensing.
* **ASV (Amplicon Sequence Variant)**: Unit taksonomi operasional beresolusi tinggi pada sekuensing *short-read* yang membedakan urutan DNA hingga ketelitian 1 nukleotida, namun kurang cocok untuk data Nanopore R9.4.1 karena laju error homopolimer indel.

## B
* **Bacillota**: Nama filum bakteri resmi menurut ICNP 2021 (sebelumnya dikenal sebagai *Firmicutes*), mencakup bakteri Gram-positif seperti *Lactobacillus*, *Clostridium*, dan *Faecalibacterium*.
* **Bacteroidota**: Nama filum bakteri resmi menurut ICNP 2021 (sebelumnya dikenal sebagai *Bacteroidetes*), mencakup bakteri Gram-negatif dominan usus seperti genus *Bacteroides* dan *Prevotella*.
* **Basecalling**: Proses komputasi berbasis deep learning (menggunakan program seperti *Dorado*) untuk menerjemahkan data deret waktu arus listrik mentah (`.pod5`) menjadi huruf basa nitrogen (`A`, `C`, `G`, `T`) dalam file format FASTQ.
* **Beta Diversity**: Perbedaan atau ketidaksamaan (*dissimilarity*) struktur komposisi komunitas mikroba antar-sampel atau antar-kelompok perlakuan.
* **Bray–Curtis Dissimilarity**: Metrik statistik non-euklidian yang mengukur tingkat perbedaan kelimpahan takson mikroba antara dua sampel (bernilai 0 untuk komunitas identik, dan 1 untuk komunitas yang sama sekali tidak berbagi takson).

## C
* **Chopper**: Perangkat lunak baris perintah berbasis Rust (bagian dari suite NanoPack2) yang dirancang untuk memotong dan menyaring *reads* sekuensing panjang berdasarkan ambang batas panjang dan rata-rata skor Phred.
* **Clade Reads**: Pada laporan Kraken2, jumlah *reads* yang diklasifikasikan pada takson tersebut ditambah seluruh reads yang diklasifikasikan pada cabang anak keturunannya di pohon taksonomi.
* **Compositional Data (Data Komposisional)**: Data proporsional yang terikat pada jumlah konstan (total = 100%). Sifat ini menyebabkan kelimpahan relatif suatu mikroba saling bergantung satu sama lain, sehingga peningkatan proporsi satu takson tidak selalu berarti peningkatan jumlah sel absolutnya.
* **Copy Number Variation (CNV) 16S**: Variasi jumlah salinan gen 16S rRNA di dalam genom bakteri (berkisar antara 1 hingga 15 salinan operon), yang dapat menyebabkan bias kelimpahan artifisial pada sekuensing amplikon.

## D
* **Direct Reads**: Pada laporan Kraken2, jumlah *reads* yang secara spesifik diklasifikasikan persis pada takson tersebut (tanpa menghitung reads pada cabang anak keturunannya).
* **Dorado**: Program *basecaller* resmi generasi terbaru dari Oxford Nanopore Technologies yang menggunakan arsitektur neural network modern untuk menghasilkan akurasi basecalling tinggi.
* **Dysbiosis (Disbiosis)**: Kondisi gangguan keseimbangan komposisi atau fungsionalitas komunitas mikroba tubuh yang sering kali berasosiasi dengan inflamasi atau keadaan patologis.

## E
* **Emu**: Perangkat lunak bioinformatika berbasis algoritma *Expectation-Maximization* (EM) dan penjajaran *semi-global* `minimap2` yang dirancang khusus untuk menghasilkan profil kelimpahan mikroba tingkat spesies yang sangat akurat dari data 16S Nanopore.

## F
* **False Discovery Rate (FDR)**: Proporsi kesalahan positif palsu (*false positive*) yang diharapkan di antara seluruh pengujian hipotesis yang dinyatakan signifikan; sering dikontrol menggunakan prosedur koreksi Benjamini–Hochberg (menghasilkan nilai $q$).
* **FASTQ**: Format teks standar penyimpanan data sekuensing biologis yang terdiri dari 4 baris per read: header identitas (`@`), urutan basa DNA, pembatas (`+`), dan skor kualitas mutu Phred dalam format ASCII.
* **Firmicutes/Bacteroidetes (F/B) Ratio**: Rasio kelimpahan historis antara dua filum dominan usus. Paradigma modern memandang rasio ini sebagai over-penyederhanaan yang tidak memadai sebagai biomarker klinis universal.

## K
* **Kitome (atau Splashome)**: Jejak kontaminasi DNA mikroba lingkungan berkonsentrasi rendah yang secara inheren terbawa oleh kit ekstraksi DNA, enzim polimerase PCR, atau air laboratorium.
* **K-mer**: Sub-sekuens DNA dengan panjang konstan ($k$) yang digunakan oleh algoritma klasifikasi cepat seperti Kraken2 untuk mencocokkan pola basa tanpa penjajaran penuh.
* **Kraken2**: Program klasifikasi taksonomi ultra-cepat berbasis pencocokan *minimizer* $k$-mer ke dalam tabel hash pohon taksonomi.
* **Krona Plot**: Alat visualisasi interaktif open-source yang menampilkan profil hierarki taksonomi mikroba dalam bentuk diagram lingkaran konsentris bertingkat (*multi-layered pie chart*).

## L
* **Lowest Common Ancestor (LCA)**: Titik simpul leluhur terdekat pada pohon filogenetika yang mencakup seluruh takson yang memiliki bukti sekuens bersama; digunakan oleh Kraken2 untuk menghindari kesalahan penugasan saat sekuens bersifat ambigu.

## M
* **Minimizer**: Sub-sekuens $k$-mer terkecil menurut urutan leksikografis kanonikal di dalam jendela geser sekuens DNA, digunakan oleh Kraken2 untuk mereduksi kebutuhan memori indeks referensi secara drastis.
* **Mock Community**: Campuran sel mikroba atau DNA terkarakterisasi dengan komposisi yang telah diketahui secara tepat; digunakan sebagai kontrol positif laboratorium untuk mengukur akurasi dan bias alur kerja.

## P
* **PCoA (Principal Coordinates Analysis)**: Metode reduksi dimensi (*ordination*) yang memproyeksikan matriks jarak non-euklidian (seperti Bray–Curtis) ke dalam sumbu-sumbu koordinat utama untuk memvisualisasikan pemisahan sampel.
* **PERMANOVA**: Uji statistik permutasi non-parametrik multivariat untuk menguji signifikansi perbedaan centroid komunitas mikroba antar-kelompok perlakuan.
* **Phred Quality Score ($Q$)**: Ukuran kualitas logaritmik akurasi pembacaan basa ($Q = -10 \log_{10} P_{error}$). Skala Phred+33 mengonversi skor $Q$ ke karakter ASCII dengan menambahkan konstanta 33.
* **Pseudomonadota**: Nama filum bakteri resmi menurut ICNP 2021 (sebelumnya dikenal sebagai *Proteobacteria*), mencakup bakteri Gram-negatif seperti *Escherichia*, *Klebsiella*, dan *Pseudomonas*.

## R
* **Rarefaction**: Prosedur penyesuaian (*subsampling*) acak tanpa pengembalian terhadap tabel hitungan mikroba untuk menyamakan kedalaman sekuensing antar-sampel sebelum membandingkan metrik keanekaragaman.

## S
* **Shannon Index ($H'$)**: Metrik Alpha Diversity yang mengukur ketidakpastian informasi dalam komunitas, mencerminkan kekayaan takson sekaligus kemerataan kelimpahannya.
* **Simpson Index ($1 - D$)**: Metrik Alpha Diversity yang merepresentasikan probabilitas bahwa dua *reads* yang diambil secara acak dari suatu sampel berasal dari takson mikroba yang berbeda.
* **16S rRNA Gen**: Gen penyandi subunit kecil RNA ribosom bakteri (~1.500 bp) yang tersusun atas daerah lestari universal dan 9 daerah hipervariabel (V1–V9), menjadi standar emas penanda taksonomi molekuler bakteri.
