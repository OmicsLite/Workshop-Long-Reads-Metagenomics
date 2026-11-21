# 📝 Kuis Pemahaman (Pre/Post-Test)

Selesaikan 12 pertanyaan pilihan ganda berikut untuk menguji pemahaman Anda mengenai teknologi sekuensing Nanopore, analisis 16S amplikon, ekologi mikrobioma, dan interpretasi klinis yang bertanggung jawab.

---

### Soal 1
Teknologi sekuensing Oxford Nanopore (ONT) mengidentifikasi urutan basa nitrogen secara fisik berdasarkan fluktuasi apa?
* (A) Pancaran intensitas cahaya fluoresensi pewarna nukleotida
* (B) Perubahan arus ionik akibat molekul DNA menghalangi rongga penyempitan pori protein
* (C) Pelepasan ion hidrogen ($H^+$) yang mengubah keasaman (pH) larutan
* (D) Hamburan emisi sinar-X dari molekul polimerase yang sedang bergerak

### Soal 2
Dalam format file FASTQ dengan pengodean standar Phred+33, berapakah nilai skor Phred teoretis untuk karakter ASCII `?` pada baris keempat?
* (A) Q10 (Akurasi 90%)
* (B) Q20 (Akurasi 99%)
* (C) Q30 (Akurasi 99.9%)
* (D) Q40 (Akurasi 99.99%)

### Soal 3
Berapakah probabilitas kesalahan identifikasi basa ($P_{error}$) untuk suatu nukleotida yang memiliki Phred Quality Score sebesar Q20?
* (A) 10% (1 dalam 10)
* (B) 1% (1 dalam 100)
* (C) 0.1% (1 dalam 1.000)
* (D) 0.01% (1 dalam 10.000)

### Soal 4
Gen 16S rRNA sangat ideal digunakan sebagai penanda taksonomi molekuler bakteri karena memiliki karakteristik khusus, yaitu...
* (A) Menyandi enzim metabolisme lemak di dalam lisosom bakteri
* (B) Memiliki ukuran sangat pendek (<100 bp) sehingga murah disekuensing
* (C) Tersusun atas kombinasi wilayah lestari (*conserved*) universal dan 9 wilayah hipervariabel (V1–V9)
* (D) Hanya dimiliki oleh bakteri patogen penyebab penyakit infeksi usus

### Soal 5
Pada praktikum hands-on menggunakan dataset kohort feses bayi PRJNA637202 (Angell et al., 2020), mengapa kita mengatur batas penyaringan panjang read pada rentang 1.000–1.700 bp?
* (A) Karena mesin Google Colab hanya sanggup memproses file berukuran tepat 1.000 bp
* (B) Karena desain primer PCR laboratorium basah pada studi tersebut menargetkan wilayah V3–V9 (≈1.140 bp), bukan V1–V9 (≈1.500 bp)
* (C) Karena sekuens di luar rentang tersebut adalah DNA inang manusia
* (D) Untuk mempercepat proses download database k-mer Kraken2

### Soal 6
Manakah pernyataan yang BENAR mengenai kemampuan filter ukuran panjang sekuens terhadap keberadaan molekul PCR *chimera*?
* (A) Filter panjang 1.000–1.700 bp otomatis membersihkan 100% molekul chimera
* (B) Molekul chimera tidak pernah terbentuk pada sekuensing amplikon Nanopore
* (C) Banyak molekul PCR chimera memiliki panjang normal (≈1.140–1.500 bp) sehingga lolos dari penyaringan ukuran panjang dan memerlukan deteksi algoritmik
* (D) Chimera hanya terbentuk jika sampel terkontaminasi oleh sel eukariota

### Soal 7
Bagaimanakah prinsip kerja utama algoritma penugasan taksonomi pada program `Kraken2`?
* (A) Melakukan penyejajaran sekuens global Needleman-Wunsch terhadap genom lengkap
* (B) Mencocokkan potongan sub-sekuens $k$-mer minimizer secara cepat dan menugaskan takson berdasarkan Lowest Common Ancestor (LCA)
* (C) Merakit (*assembly*) reads menjadi contig utuh sebelum klasifikasi
* (D) Memprediksi struktur 3 dimensi molekul RNA ribosom

### Soal 8
Mengapa pada alur praktikum primer ini kita memprioritaskan analisis hitungan clade reads langsung dari laporan Kraken2 daripada memaksakan penataan ulang probabilistik Bracken?
* (A) Karena program Bracken berbayar dan tidak open-source
* (B) Karena clade reads Kraken2 mempertahankan transparansi hirarki pohon taksonomi dan penugasan LCA secara jujur tanpa redistribusi probabilistik yang rentan memaksakan reads ambigu di bawah profil error indel Nanopore
* (C) Karena Bracken hanya dapat digunakan untuk data virus dan jamur
* (D) Karena Bracken membutuhkan GPU berkekuatan tinggi

### Soal 9
Jika seorang peneliti ingin melengkapi hasil *k-mer* `Kraken2` dengan perangkat lunak khusus 16S bacaan panjang yang menggabungkan penjajaran `minimap2` dan algoritma *Expectation-Maximization* (EM) untuk meredam error indel Nanopore, alat manakah yang paling tepat?
* (A) Emu (Curry et al., 2022)
* (B) BWA-MEM
* (C) FastQC
* (D) SPAdes

### Soal 10
Mengapa kita perlu melakukan prosedur *Rarefaction* (penyamaan kedalaman sekuensing) sebelum menghitung metrik Alpha Diversity seperti Shannon Index?
* (A) Untuk menghapus sekuens yang membawa mutasi titik
* (B) Karena perbedaan jumlah total reads antar-sampel dapat menciptakan ilusi bahwa sampel yang lebih dalam memiliki keanekaragaman genus yang lebih kaya secara artifisial
* (C) Untuk mengubah format data FASTQ menjadi FASTA
* (D) Agar diagram lingkaran Krona Plot dapat berubah warna

### Soal 11
Berdasarkan aturan tata nama filum bakteri resmi ICNP 2021, apakah nama ilmiah baru yang menggantikan filum *Firmicutes* dan *Bacteroidetes*?
* (A) *Proteobacteria* dan *Actinobacteria*
* (B) *Bacillota* dan *Bacteroidota*
* (C) *Clostridia* dan *Bacteroidia*
* (D) *Enterobacteriaceae* dan *Pseudomonadaceae*

### Soal 12
Bila hasil analisis menunjukkan kelimpahan relatif genus *Bifidobacterium* terukur 70% pada Sampel Usus Bayi X dan 35% pada Sampel Usus Bayi Y, kesimpulan manakah yang PALING ILMIAH dan bertanggung jawab?
* (A) Sampel X pasti memiliki jumlah sel fisik *Bifidobacterium* dua kali lipat lebih banyak daripada Sampel Y
* (B) Bayi pada Sampel X dipastikan menderita infeksi bakteri aktif
* (C) Data 16S bersifat komposisional (kelimpahan relatif); persentase yang lebih tinggi mencerminkan proporsi sekuens di dalam pustaka, bukan konsentrasi sel absolut
* (D) Sekuensing Nanopore pada Sampel Y dipastikan mengalami kegagalan teknis
