Tentu, ini adalah draf `README.md` yang lengkap dan detail, namun tetap terlihat sederhana dan bersih, berdasarkan ketiga file notebook yang Anda berikan.

-----

# Analisis Sentimen Berbasis Aspek (ABSA) pada Ulasan Karyawan Tokopedia

Proyek ini bertujuan untuk melakukan Analisis Sentimen Berbasis Aspek (ABSA) terhadap ulasan karyawan Tokopedia. Analisis ini dirancang untuk mengidentifikasi aspek-aspek kunci terkait kepuasan kerja berdasarkan teori Herzberg, mengukur sentimen terhadap aspek-aspek tersebut, dan menganalisis perubahannya sebelum dan sesudah periode akuisisi pada 15 Mei 2024.

## 🚀 Fitur Utama

  * **Pra-pemrosesan Data**: Membersihkan dan menyiapkan data ulasan mentah untuk analisis.
  * **Ekstraksi & Klasifikasi Aspek Otomatis**: Menggunakan TF-IDF dan model *Sentence Transformer* untuk mengidentifikasi kata kunci relevan dan mengklasifikasikannya ke dalam 13 aspek teori Herzberg (Faktor *Motivator* & *Hygiene*).
  * **Analisis Sentimen Tingkat Lanjut**: Menerapkan model ABSA (*Aspect-Based Sentiment Analysis*) dari Hugging Face (`yangheng/deberta-v3-base-absa-v1.1`) untuk menentukan sentimen (positif, negatif, netral) untuk setiap aspek yang teridentifikasi dalam kalimat.
  * **Analisis Komparatif**: Membandingkan perubahan sentimen sebelum dan sesudah tanggal akuisisi untuk mengukur dampaknya terhadap persepsi karyawan.
  * **Uji Signifikansi Statistik**: Menggunakan *two-proportion z-test* untuk memvalidasi apakah perubahan sentimen yang diamati signifikan secara statistik.
  * **Visualisasi Data**: Menghasilkan grafik batang dan *Priority Grid* untuk memvisualisasikan perubahan sentimen dan prioritas area perbaikan.

## 📂 Struktur Direktori

```
.
├── 📄 data_mentah/
│   └── export_1750496855742(tokped_review).csv  # File input mentah
│
├── 📓 1_data_pre-processing.ipynb
├── 📓 2_kamus_aspek.ipynb
├── 📓 3_analisis_sentimen.ipynb
│
├── 📊 hasil_analisis/
│   ├── tokopedia_reviews_date_cleaned.csv  # Output dari skrip 1
│   ├── kamus_aspek_v5.csv                 # Output dari skrip 2
│   ├── hasil_absa_batch_processing.csv    # Output utama dari skrip 3 (analisis sentimen)
│   ├── reviews_tokopedia.csv              # Agregasi data untuk analisis statistik
│   ├── all_aspect_stats.csv               # Hasil z-test lengkap
│   ├── top6_table.csv                     # 6 aspek dengan perubahan terbesar
│   ├── delta_bar.png                      # Grafik batang perubahan sentimen
│   └── priority_grid.png                  # Grafik Priority Grid
│
└── README.md                              # Anda sedang membaca ini
```

## ⚙️ Instalasi

Pastikan Anda memiliki Python 3.8+ terinstal. Kemudian, instal semua pustaka yang dibutuhkan dengan menjalankan perintah berikut:

```bash
pip install pandas scikit-learn spacy "sentence-transformers>=2.2.0" torch transformers nltk matplotlib scipy
```

Selanjutnya, unduh model bahasa Inggris untuk spaCy:

```bash
python -m spacy download en_core_web_sm
```

## 💡 Alur Kerja & Cara Menjalankan

Proyek ini terdiri dari tiga notebook utama yang harus dijalankan secara berurutan.

### Langkah 1: Pra-pemrosesan Data

Jalankan notebook `1_data_pre-processing.ipynb` untuk membersihkan data mentah.

  * **Input**: `data_mentah/export_1750496855742(tokped_review).csv`
  * **Proses**:
    1.  Memuat dataset.
    2.  Membuang kolom yang tidak relevan.
    3.  Membersihkan teks dari tag HTML, URL, dan karakter spesial.
    4.  Menghapus baris duplikat dan baris yang tidak memiliki konten ulasan.
    5.  Menstandarkan format kolom tanggal menjadi `DD-MM-YYYY`.
  * **Output**: `hasil_analisis/tokopedia_reviews_date_cleaned.csv`

### Langkah 2: Pembuatan Kamus Aspek

Jalankan notebook `2_kamus_aspek.ipynb` untuk menghasilkan daftar kata kunci yang telah diklasifikasikan ke dalam aspek Herzberg.

  * **Input**: `hasil_analisis/tokopedia_reviews_date_cleaned.csv` (atau file ulasan bersih lainnya).
  * **Proses**:
    1.  Mengekstraksi kandidat aspek (kata kunci) dari ulasan menggunakan TF-IDF.
    2.  Memfilter kata kunci, hanya menyisakan kata benda (NOUN) menggunakan spaCy.
    3.  Menggunakan model *Sentence Transformer* (`all-MiniLM-L6-v2`) untuk menghitung kemiripan kosinus (*cosine similarity*) antara setiap kata kunci dengan deskripsi dari 13 aspek teori Herzberg.
    4.  Setiap kata kunci diklasifikasikan ke aspek dengan skor kemiripan tertinggi.
  * **Output**: `hasil_analisis/kamus_aspek_v5.csv` (File ini krusial untuk langkah selanjutnya).

### Langkah 3: Analisis Sentimen & Interpretasi Hasil

Jalankan notebook `3_analisis_sentimen.ipynb` untuk melakukan analisis utama.

  * **Input**:
      * `hasil_analisis/tokopedia_reviews_date_cleaned.csv`
      * `hasil_analisis/kamus_aspek_v5.csv`
  * **Proses**:
    1.  **Pelabelan Sentimen**: Memecah ulasan menjadi kalimat, mendeteksi aspek di dalamnya menggunakan kamus, dan memberikan label sentimen (positif/negatif/netral) menggunakan model ABSA dari Hugging Face.
    2.  **Agregasi Data**: Mengelompokkan hasil sentimen per ulasan dan menandainya sebagai periode "pre" atau "post" akuisisi.
    3.  **Analisis Statistik & Visualisasi**: Melakukan z-test pada data agregat untuk mengukur signifikansi perubahan sentimen dan membuat plot `delta_bar.png` serta `priority_grid.png`.
  * **Output**: Seluruh file di dalam folder `hasil_analisis/`.

-----

## 📜 Detail Skrip

\<details\>
\<summary\>\<strong\>1. \<code\>data\_pre-processing.ipynb\</code\>\</strong\>\</summary\>
\<br\>
\<ul\>
\<li\>\<strong\>Tujuan\</strong\>: Membersihkan file CSV mentah dari data yang tidak relevan, duplikat, dan format yang tidak konsisten agar siap untuk dianalisis.\</li\>
\<li\>\<strong\>Input Utama\</strong\>: \<code\>export\_1750496855742(tokped\_review).csv\</code\>\</li\>
\<li\>\<strong\>Output Utama\</strong\>: \<code\>tokopedia\_reviews\_date\_cleaned.csv\</code\>\</li\>
\<li\>\<strong\>Proses Kunci\</strong\>:
\<ul\>
\<li\>Seleksi kolom: \<code\>Review Date\</code\>, \<code\>Review Pros\</code\>, \<code\>Review Cons\</code\>, \<code\>Review Rating Overall\</code\>.\</li\>
\<li\>Pembersihan teks dengan Regex untuk menghapus HTML, URL, dan karakter non-alfanumerik.\</li\>
\<li\>Penghapusan duplikat berdasarkan konten ulasan dan tanggal.\</li\>
\<li\>Konversi dan standardisasi kolom tanggal ke format \<code\>DD-MM-YYYY\</code\>.\</li\>
\</ul\>
\</li\>
\</ul\>
\</details\>

\<details\>
\<summary\>\<strong\>2. \<code\>kamus\_aspek.ipynb\</code\>\</strong\>\</summary\>
\<br\>
\<ul\>
\<li\>\<strong\>Tujuan\</strong\>: Membangun sebuah kamus yang memetakan kata-kata kunci dari ulasan ke dalam 13 aspek kepuasan kerja menurut teori Herzberg.\</li\>
\<li\>\<strong\>Input Utama\</strong\>: File ulasan yang sudah bersih (e.g., \<code\>tokopedia\_reviews\_date\_cleaned.csv\</code\>).\</li\>
\<li\>\<strong\>Output Utama\</strong\>: \<code\>kamus\_aspek\_v5.csv\</code\>\</li\>
\<li\>\<strong\>Proses Kunci\</strong\>:
\<ul\>
\<li\>\<strong\>Ekstraksi\</strong\>: Menggunakan \<code\>TfidfVectorizer\</code\> untuk menemukan term (1-gram & 2-gram) yang paling signifikan.\</li\>
\<li\>\<strong\>Filtering\</strong\>: Menggunakan \<code\>spaCy\</code\> untuk menyaring term, hanya mempertahankan yang merupakan kata benda (NOUN) untuk merepresentasikan aspek.\</li\>
\<li\>\<strong\>Klasifikasi Semantik\</strong\>: Menggunakan model \<code\>SentenceTransformer\</code\> untuk mencari aspek Herzberg (misal: 'Compensation', 'Work Itself') yang deskripsinya paling mirip secara semantik dengan setiap term yang telah diekstraksi.\</li\>
\</ul\>
\</li\>
\</ul\>
\</details\>

\<details\>
\<summary\>\<strong\>3. \<code\>analisis\_sentimen.ipynb\</code\>\</strong\>\</summary\>
\<br\>
\<ul\>
\<li\>\<strong\>Tujuan\</strong\>: Menganalisis sentimen untuk setiap aspek yang ditemukan dalam ulasan dan membandingkan hasilnya antara periode sebelum dan sesudah akuisisi.\</li\>
\<li\>\<strong\>Input Utama\</strong\>: \<code\>tokopedia\_reviews\_date\_cleaned.csv\</code\> dan \<code\>kamus\_aspek\_v5.csv\</code\>.\</li\>
\<li\>\<strong\>Output Utama\</strong\>: \<code\>hasil\_absa\_batch\_processing.csv\</code\>, \<code\>reviews\_tokopedia.csv\</code\>, dan berbagai file statistik serta visualisasi (\<code\>.png\</code\>, \<code\>.csv\</code\>).\</li\>
\<li\>\<strong\>Proses Kunci\</strong\>:
\<ul\>
\<li\>\<strong\>Analisis Sentimen (ABSA)\</strong\>: Menggunakan pipeline \<em\>text-classification\</em\> dari \<code\>transformers\</code\> dengan model \<code\>yangheng/deberta-v3-base-absa-v1.1\</code\>. Inputnya berupa pasangan "[Kalimat] [SEP] [Aspek]". Proses ini dijalankan secara batch di GPU untuk efisiensi.\</li\>
\<li\>\<strong\>Analisis Temporal\</strong\>: Membagi data menjadi dua periode ("pre" dan "post") berdasarkan tanggal akuisisi (15 Mei 2024).\</li\>
\<li\>\<strong\>Analisis Statistik\</strong\>: Menghitung proporsi sentimen positif untuk setiap aspek di kedua periode, lalu melakukan \<em\>two-proportion z-test\</em\> untuk memeriksa apakah perubahannya signifikan.\</li\>
\<li\>\<strong\>Visualisasi\</strong\>: Membuat grafik untuk memudahkan interpretasi hasil, seperti perubahan absolut sentimen (Δ Net-Score) dan *Priority Grid* yang memetakan perubahan terhadap kategori Herzberg (Motivator vs. Hygiene).\</li\>
\</ul\>
\</li\>
\</ul\>
\</details\>
