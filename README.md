# Analisis Sentimen Berbasis Aspek (ABSA) pada Ulasan Karyawan

Project ini bertujuan untuk melakukan Analisis Sentimen Berbasis Aspek (ABSA) terhadap ulasan karyawan. Analisis ini dirancang untuk mengidentifikasi aspek-aspek kunci terkait kepuasan kerja berdasarkan teori Herzberg, mengukur sentimen terhadap aspek-aspek tersebut, dan melakukan analisis komparatif.

Metodologi utama dalam analisis ini adalah perhitungan **Net-Score** (proporsi sentimen positif dikurangi proporsi negatif) untuk setiap aspek. Perubahan **(Δ) Net-Score** dianalisis antara dua periode waktu (sebelum dan sesudah 1 Juli 2023) untuk mengukur dampak peristiwa atau perubahan kebijakan. Hasilnya divisualisasikan dalam sebuah **Priority Grid** untuk memberikan rekomendasi strategis yang dapat ditindaklanjuti.

## 🚀 Fitur Utama

  * **Pra-pemrosesan Data**: Membersihkan dan menyiapkan data ulasan mentah untuk analisis.
  * **Ekstraksi & Klasifikasi Aspek Otomatis**: Menggunakan TF-IDF dan model *Sentence Transformer* untuk mengidentifikasi kata kunci relevan dan mengklasifikasikannya ke dalam 13 aspek teori Herzberg.
  * **Analisis Sentimen Tingkat Lanjut**: Menerapkan model ABSA dari Hugging Face (`yangheng/deberta-v3-base-absa-v1.1`) untuk menentukan sentimen (positif, negatif, netral) pada setiap aspek yang teridentifikasi.
  * **Analisis Komparatif Berbasis Net-Score**: Membandingkan perubahan sentimen menggunakan metrik **Δ Net-Score** sebelum dan sesudah tanggal 1 Juli 2023.
  * **Uji Signifikansi Statistik**: Menggunakan *two-proportion z-test* untuk memvalidasi apakah perubahan proporsi sentimen positif signifikan secara statistik.
  * **Visualisasi Strategis**: Menghasilkan grafik batang Δ Net-Score dan sebuah **Priority Grid** yang memetakan perubahan terhadap kategori aspek (Motivator vs. Hygiene) untuk menentukan area prioritas (Fix, Nurture, Maintain, Monitor).
  * **Pembuatan Tabel Laporan**: Menghasilkan tabel-tabel spesifik (seperti Tabel 5.3 dan 5.4) yang siap digunakan untuk laporan penelitian.

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
│   ├── hasil_absa_batch_processing.csv    # Hasil mentah analisis sentimen
│   ├── hasil_aggregasi.csv                # Data agregat per periode
│   ├── hasil_kalkulasi.csv                # Hasil kalkulasi Net-Score dan z-test
│   ├── tabel_5.3.csv                      # Tabel Δ Proporsi Positif
│   ├── tabel_5.4.csv                      # Tabel Uji Signifikansi
│   ├── delta_bar.png                      # Grafik batang perubahan Net-Score
│   └── priority_grid.png                  # Grafik Priority Grid
│
└── README.md                              # sedang dibaca
```

## ⚙️ Instalasi

Pastikan Anda memiliki Python 3.8+ terinstal. Kemudian, instal semua pustaka yang dibutuhkan dengan menjalankan perintah berikut:

```bash
pip install pandas scikit-learn spacy "sentence-transformers>=2.2.0" torch transformers nltk matplotlib scipy statsmodels adjustText
```

Selanjutnya, unduh model bahasa Inggris untuk spaCy:

```bash
python -m spacy download en_core_web_sm
```

## 💡 Alur Kerja & Cara Menjalankan

Project ini terdiri dari tiga notebook utama yang harus dijalankan secara berurutan.

### Langkah 1: Pra-pemrosesan Data

Jalankan notebook `1_data_pre-processing.ipynb` untuk membersihkan data mentah.

  * **Input**: `data_mentah/export_1750496855742(tokped_review).csv`
  * **Output**: `hasil_analisis/tokopedia_reviews_date_cleaned.csv`

### Langkah 2: Pembuatan Kamus Aspek

Jalankan notebook `2_kamus_aspek.ipynb` untuk menghasilkan daftar kata kunci yang telah diklasifikasikan ke dalam aspek Herzberg.

  * **Input**: File ulasan yang sudah bersih (e.g., `shopee_cleaned_reviews.csv`).
  * **Output**: `hasil_analisis/hasil_klasifikasi_dengan_deskripsi_aspek.csv` (dapat diubah namanya menjadi `kamus_aspek_v5.csv` untuk langkah selanjutnya).

### Langkah 3: Analisis Sentimen & Interpretasi Hasil

Jalankan notebook `3_analisis_sentimen.ipynb` untuk melakukan analisis utama. Notebook ini terbagi menjadi beberapa bagian:

  * **Input**:
      * `hasil_analisis/tokopedia_reviews_date_cleaned.csv`
      * `hasil_analisis/kamus_aspek_v5.csv`
  * **Proses & Output**:
    1.  **Bagian A (Pelabelan Sentimen)**: Melakukan pelabelan sentimen berbasis aspek pada seluruh ulasan. Outputnya adalah `hasil_absa_batch_processing.csv`.
    2.  **Bagian B (Analisis)**: Melakukan agregasi, kalkulasi statistik, dan visualisasi berdasarkan hasil pelabelan.
          * Menghasilkan `hasil_aggregasi.csv` dan `hasil_kalkulasi.csv`.
          * Menghasilkan tabel laporan `tabel_5.3.csv` dan `tabel_5.4.csv`.
          * Menampilkan visualisasi **Bar Chart Δ Net-Score** dan **Priority Grid**.

-----

## 📜 Detail Skrip

### 1\. `1_data_pre-processing.ipynb`

  * **Tujuan**: Membersihkan file CSV mentah dari data yang tidak relevan, duplikat, dan format yang tidak konsisten agar siap untuk dianalisis.
  * **Input Utama**: `export_1750496855742(tokped_review).csv`.
  * **Output Utama**: `tokopedia_reviews_date_cleaned.csv`.
  * **Proses Kunci**: Seleksi kolom, pembersihan teks, penghapusan duplikat, dan standardisasi format tanggal.

### 2\. `2_kamus_aspek.ipynb`

  * **Tujuan**: Membangun sebuah kamus yang memetakan kata-kata kunci (termasuk sinonimnya) dari ulasan ke dalam 13 aspek kepuasan kerja menurut teori Herzberg.
  * **Input Utama**: File ulasan yang sudah bersih (e.g., `shopee_cleaned_reviews.csv`).
  * **Output Utama**: `hasil_klasifikasi_dengan_deskripsi_aspek.csv`.
  * **Proses Kunci**:
      * **Ekstraksi**: Menggunakan `TfidfVectorizer` untuk menemukan *term* yang signifikan.
      * **Filtering**: Menggunakan `spaCy` untuk menyaring *term* berdasarkan kelas kata (misalnya, hanya kata benda).
      * **Klasifikasi Semantik**: Menggunakan model `SentenceTransformer` untuk mengklasifikasikan setiap *term* ke aspek Herzberg dengan kemiripan semantik tertinggi.

### 3\. `3_analisis_sentimen.ipynb` (Updated)

  * **Tujuan**: Menganalisis sentimen untuk setiap aspek, menghitung perubahan **Net-Score** antar periode, dan memvisualisasikannya dalam **Priority Grid** untuk rekomendasi strategis.
  * **Input Utama**: `tokopedia_reviews_date_cleaned.csv` dan `kamus_aspek_v5.csv`.
  * **Output Utama**: `hasil_absa_batch_processing.csv`, `hasil_aggregasi.csv`, `hasil_kalkulasi.csv`, `tabel_5.3.csv`, `tabel_5.4.csv`, dan visualisasi plot.
  * **Proses Kunci**:
    1.  **Pelabelan Sentimen (ABSA)**: Menggunakan model `yangheng/deberta-v3-base-absa-v1.1` untuk memberikan label sentimen (positif, negatif, netral) pada setiap aspek yang terdeteksi dalam ulasan.
    2.  **Analisis Temporal**: Membagi data menjadi dua periode ("sebelum" dan "sesudah") berdasarkan tanggal **1 Juli 2023**.
    3.  **Kalkulasi Metrik**: Menghitung **Net-Score** (`(Positif - Negatif) / Total`) untuk setiap aspek di kedua periode, lalu menghitung perubahannya (**Δ Net-Score**).
    4.  **Uji Statistik**: Melakukan *two-proportion z-test* pada proporsi sentimen positif untuk menguji signifikansi perubahan (`p-value`).
    5.  **Visualisasi**:
          * Membuat **grafik batang** untuk menunjukkan Δ Net-Score per aspek.
          * Membuat **Priority Grid** yang memetakan Δ Net-Score (sumbu X) terhadap Kategori Herzberg (Motivator/Hygiene, sumbu Y) untuk mengidentifikasi area prioritas.
