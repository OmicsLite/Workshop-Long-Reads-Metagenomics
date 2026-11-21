# 🔑 Kunci Jawaban & Penjelasan Kuis

Gunakan dokumen ini untuk mencocokkan jawaban Anda dan memahami landasan ilmiah serta metodologis dari masing-masing soal evaluasi.

---

### Soal 1: (B) Perubahan arus ionik akibat molekul DNA menghalangi rongga penyempitan pori protein
* **Penjelasan**: Oxford Nanopore mengukur arus listrik kontinu yang melintasi pori protein tertanam di membran sintetis. Saat untai tunggal DNA meluncur melalui rongga pembacaan pori, kombinasi basa nukleotida merintangi aliran ion elektrolit sehingga menimbulkan fluktuasi arus listrik khas yang kemudian diterjemahkan oleh basecaller neural network menjadi urutan basa.

### Soal 2: (C) Q30 (Akurasi 99.9%)
* **Penjelasan**: Berdasarkan rumus standar Phred+33: $\text{ASCII Code} = Q + 33$. Karakter ASCII `?` memiliki nomor desimal 63, sehingga $Q = 63 - 33 = 30$. Nilai Q30 setara dengan akurasi 99.9% (probabilitas error $P = 10^{-3} = 0.001$). Sementara itu, Q20 diwakili oleh karakter `5` ($20 + 33 = 53$).

### Soal 3: (B) 1% (1 dalam 100)
* **Penjelasan**: Rumus probabilitas error Phred adalah $P = 10^{-Q/10}$. Untuk $Q = 20$, $P = 10^{-20/10} = 10^{-2} = 0.01$ atau 1%. Akurasi teoretis pembacaannya adalah $1 - 0.01 = 0.99$ (99%).

### Soal 4: (C) Tersusun atas kombinasi wilayah lestari (*conserved*) universal dan 9 wilayah hipervariabel (V1–V9)
* **Penjelasan**: Gen 16S rRNA ideal sebagai biomarker filogenetik prokariota karena wilayah lestarinya universal pada seluruh bakteri (memungkinkan penempelan primer PCR umum), sedangkan wilayah hipervariabelnya (V1 hingga V9) menyimpan divergensi urutan basa khas yang membedakan takson.

### Soal 5: (B) Karena desain primer PCR laboratorium basah pada studi tersebut menargetkan wilayah V3–V9 (≈1.140 bp), bukan V1–V9 (≈1.500 bp)
* **Penjelasan**: Publikasi asal dataset PRJNA637202 (Angell et al., 2020, *PeerJ*) merancang primer PCR amplikon untuk mengamplifikasi segmen V3 hingga V9 yang berukuran rata-rata ≈1.140 bp. Memahami desain primer laboratorium basah mencegah kita menyetel batas filter yang salah (seperti 1.300 bp yang akan membuang >90% data valid).

### Soal 6: (C) Banyak molekul PCR chimera memiliki panjang normal (≈1.140–1.500 bp) sehingga lolos dari penyaringan ukuran panjang dan memerlukan deteksi algoritmik
* **Penjelasan**: Molekul chimera terjadi akibat pemanjangan primer yang terhenti pada satu molekul DNA cetakan lalu menempel pada cetakan dari spesies lain pada siklus berikutnya. Produk hibrida ini memiliki panjang amplikon yang normal sehingga tidak dapat dibedakan hanya dengan memotong ukuran panjang.

### Soal 7: (B) Mencocokkan potongan sub-sekuens $k$-mer minimizer secara cepat dan menugaskan takson berdasarkan Lowest Common Ancestor (LCA)
* **Penjelasan**: Kraken2 mengekstrak $k$-mer informatif dari setiap read dan mencocokkannya ke tabel hash database referensi. Jika bukti k-mer ambigu antara beberapa takson berkerabat, Kraken2 menugaskan klasifikasi pada simpul leluhur bersama terdekat (*Lowest Common Ancestor* / LCA) untuk menghindari penugasan positif palsu.

### Soal 8: (B) Karena clade reads Kraken2 mempertahankan transparansi hirarki pohon taksonomi dan penugasan LCA secara jujur tanpa redistribusi probabilistik yang rentan memaksakan reads ambigu di bawah profil error indel Nanopore
* **Penjelasan**: Meskipun database NCBI Targeted Loci yang kita gunakan menyediakan model k-mer 1.000 bp (`database1000mers.kmer_distrib`), pendekatan edukasi terbaik adalah mempertahankan transparansi laporan Kraken2 (`clade reads`). Penataan ulang Bayesian Bracken mengasumsikan seluruh reads pasti berasal dari takson target dan mendistribusikan ulang reads dari simpul tingkat atas ke tingkat bawah, yang dapat memperbesar risiko penugasan keliru (*over-assignment*) pada data bacaan panjang ber-error indel. Mengambil hitungan langsung dari laporan Kraken2 adalah pendekatan yang transparan, defensibel, dan mudah diverifikasi.

### Soal 9: (A) Emu (Curry et al., 2022)
* **Penjelasan**: `Emu` (*Nature Methods*, 2022) menggabungkan penjajaran semi-global `minimap2` dengan algoritma iteratif *Expectation-Maximization* (EM) yang memodelkan profil error bacaan panjang Nanopore untuk estimasi kelimpahan tingkat spesies. Selain `Emu`, pendekatan berbasis *alignment* lain seperti mode `minimap2` pada ONT EPI2ME (`wf-16s`) juga banyak dipakai; pemilihan alat bergantung pada kimia *flow cell* (R9.4.1 vs R10.4.1) serta kelengkapan database rujukan.

### Soal 10: (B) Karena perbedaan jumlah total reads antar-sampel dapat menciptakan ilusi bahwa sampel yang lebih dalam memiliki keanekaragaman genus yang lebih kaya secara artifisial
* **Penjelasan**: Sampel dengan jumlah reads lebih banyak memiliki peluang statistik lebih tinggi untuk menemukan takson langka. Rarefaction menyamakan kedalaman sampling ke batas terkecil melalui penarikan acak tanpa pengembalian (*sampling without replacement*) agar perbandingan kekayaan dan kemerataan mikroba antar-kelompok menjadi adil.

### Soal 11: (B) *Bacillota* dan *Bacteroidota*
* **Penjelasan**: Berdasarkan pembaruan resmi ICNP tahun 2021 (Oren & Garrity, 2021, *IJSEM*), akhiran nama filum diselaraskan: *Firmicutes* menjadi *Bacillota*, *Bacteroidetes* menjadi *Bacteroidota*, *Proteobacteria* menjadi *Pseudomonadota*, dan *Actinobacteria* menjadi *Actinomycetota*.

### Soal 12: (C) Data 16S bersifat komposisional (kelimpahan relatif); persentase yang lebih tinggi mencerminkan proporsi sekuens di dalam pustaka, bukan konsentrasi sel absolut
* **Penjelasan**: Sekuensing amplikon menghasilkan data komposisional (proporsi dari 100%). Perubahan persentase suatu mikroba dapat disebabkan oleh perubahan kelimpahan takson lain dalam ekosistem, bukan selalu karena penambahan sel fisik absolut takson tersebut. Selain itu, variasi salinan gen 16S (*copy number variation*) dan bias ekstraksi turut memengaruhi sinyal relatif.
