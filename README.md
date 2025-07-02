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

Tentu, ini adalah bagian "Detail Skrip" yang ditulis ulang menggunakan format Markdown standar tanpa tag HTML `<details>` yang mungkin menyebabkan error.

---

## 📜 Detail Skrip

### 1. `data_pre-processing.ipynb`

* **Tujuan**: Membersihkan file CSV mentah dari data yang tidak relevan, duplikat, dan format yang tidak konsisten agar siap untuk dianalisis.
* **Input Utama**: `export_1750496855742(tokped_review).csv`.
* **Output Utama**: `tokopedia_reviews_date_cleaned.csv`.
* **Proses Kunci**:
    * Seleksi kolom: `Review Date`, `Review Pros`, `Review Cons`, dan `Review Rating Overall`.
    * Pembersihan teks dengan Regex untuk menghapus HTML, URL, dan karakter non-alfanumerik.
    * Penghapusan duplikat berdasarkan konten ulasan dan tanggal.
    * Konversi dan standardisasi kolom tanggal ke format `DD-MM-YYYY`.

---

### 2. `kamus_aspek.ipynb`

* **Tujuan**: Membangun sebuah kamus yang memetakan kata-kata kunci dari ulasan ke dalam 13 aspek kepuasan kerja menurut teori Herzberg.
* **Input Utama**: File ulasan yang sudah bersih (contohnya `shopee_cleaned_reviews.csv`).
* **Output Utama**: `hasil_klasifikasi_dengan_deskripsi_aspek.csv` (dalam alur kerja utama menjadi `kamus_aspek_v5.csv`).
* **Proses Kunci**:
    * **Ekstraksi**: Menggunakan `TfidfVectorizer` untuk menemukan *term* (1-gram & 2-gram) yang paling signifikan.
    * **Filtering**: Menggunakan `spaCy` untuk menyaring *term*, hanya mempertahankan yang merupakan kata benda (NOUN) untuk merepresentasikan aspek.
    * **Klasifikasi Semantik**: Menggunakan model `SentenceTransformer` untuk mencari aspek Herzberg (misal: 'Compensation', 'Work Itself') yang deskripsinya paling mirip secara semantik dengan setiap *term* yang telah diekstraksi.

---

### 3. `analisis_sentimen.ipynb`

* **Tujuan**: Menganalisis sentimen untuk setiap aspek yang ditemukan dalam ulasan dan membandingkan hasilnya antara periode sebelum dan sesudah tanggal akuisisi (15 Mei 2024).
* **Input Utama**: `tokopedia_reviews_date_cleaned.csv` dan `kamus_aspek_v5.csv`.
* **Output Utama**: `hasil_absa_batch_processing.csv`, `reviews_tokopedia.csv`, serta berbagai file statistik dan visualisasi (`.png`, `.csv`).
* **Proses Kunci**:
    * **Analisis Sentimen (ABSA)**: Menggunakan *pipeline* `text-classification` dari pustaka `transformers` dengan model `yangheng/deberta-v3-base-absa-v1.1`. Inputnya berupa pasangan "[Kalimat] [SEP] [Aspek]". Proses ini dijalankan secara *batch* di GPU untuk efisiensi.
    * **Analisis Temporal**: Membagi data menjadi dua periode ("pre" dan "post") berdasarkan tanggal akuisisi.
    * **Analisis Statistik**: Menghitung proporsi sentimen positif untuk setiap aspek di kedua periode, lalu melakukan *two-proportion z-test* untuk memeriksa apakah perubahannya signifikan.
    * **Visualisasi**: Membuat grafik untuk memudahkan interpretasi hasil, seperti perubahan sentimen (digambarkan dalam `delta_bar.png`) dan *Priority Grid* (`priority_grid.png`) yang memetakan perubahan terhadap kategori Herzberg.
