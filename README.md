# Laporan Proyek Machine Learning - Shafly Khalifa Pamungkas

## Project Overview

Film merupakan salah satu bentuk hiburan yang sangat populer di seluruh dunia. Banyaknya jumlah film yang tersedia setiap tahunnya membuat pengguna sulit dalam memilih film yang sesuai dengan preferensi mereka. Untuk itu, sistem rekomendasi film sangat penting untuk membantu pengguna menemukan film yang relevan dengan selera mereka.

Salah satu pendekatan yang efektif untuk menyelesaikan masalah ini adalah dengan menggunakan Content-Based Filtering, yang merekomendasikan film berdasarkan kemiripan konten dengan film yang sudah disukai pengguna.

Sistem ini penting, karena terkadang pengguna tidak tahu judul apa yang serupa dengan film yang ia sukai, maka dari itu sistem ini hadir untuk menjawab permasalahan pengguna tersebut, di sisi perusahaan juga akan menguntungkan, karena dengan adanya sistem ini, maka perusahaan bisa selalu memastikan pengguna tetap  berada dalam layanan dan subscription mereka.

**Referensi:**

* F. Ricci, L. Rokach, and B. Shapira, "Introduction to Recommender Systems Handbook," Springer, 2011.
* [Kaggle Dataset: The Movies Dataset](https://www.kaggle.com/datasets/rounakbanik/the-movies-dataset)

## Business Understanding

### Problem Statements

* Bagaimana cara membantu pengguna menemukan film yang relevan dengan preferensi mereka tanpa harus mencarinya secara manual?
* Bagaimana membangun sistem rekomendasi yang dapat mengusulkan daftar film mirip berdasarkan film yang disukai sebelumnya?

### Goals

* Membangun sistem rekomendasi film menggunakan pendekatan Content-Based Filtering.
* Menyediakan rekomendasi Top-N film mirip berdasarkan masukan judul film tertentu dari pengguna.

### Solution Statements

* Pendekatan yang digunakan adalah Content-Based Filtering, yang akan menghitung kemiripan antar film berdasarkan fitur seperti genre, pemeran (cast), sutradara (crew), kata kunci (keywords), dan deskripsi (overview).

## Data Understanding

Dataset yang digunakan diambil dari Kaggle: [The Movies Dataset](https://www.kaggle.com/datasets/rounakbanik/the-movies-dataset)

Dataset terdiri dari beberapa file CSV, namun untuk proyek ini digunakan tiga file utama:

* `movies_metadata.csv`: berisi informasi umum tentang film
* `credits.csv`: berisi informasi pemeran dan kru
* `keywords.csv`: berisi kata kunci deskriptif film

Jumlah data:

* Jumlah total entri film: 45.000+ entri sebelum filtering
* Jumlah film setelah pembersihan dan penggabungan: 46.497 entri

Fitur-fitur penting:

* `id`: ID unik film
* `title`: Judul film
* `genres`: Genre film
* `overview`: Sinopsis film
* `cast`: Daftar pemeran utama
* `crew`: Daftar kru, termasuk sutradara
* `keywords`: Kata kunci dari metadata film

Selain itu untuk mengerti lebih dalam terkait data, saya melakukan visualisais untuk melihat genre terbanyak yang ada dalam dataset dengan menggunakan diagram batang horizontal, hasilnya genre drama adalah genre terbanyak.

## Data Preparation

### Teknik dan Alasan Data Preparation

1. **Konversi dan Filter ID:**
   ID film dikonversi ke integer, dan data dengan ID tidak valid dihapus. Ini dilakukan untuk memastikan proses penggabungan antar dataset berjalan lancar.

2. **Penggabungan Dataset:**
   Dataset `credits` dan `keywords` digabungkan dengan `movies_metadata` berdasarkan kolom `id`.

3. **Ekstraksi Informasi Penting:**

   * `genres`, `cast`, `crew`, dan `keywords` disimpan dalam bentuk list of strings.
   * Fungsi parsing digunakan untuk mengambil top 3 aktor dan hanya nama sutradara dari kolom `crew`.

4. **Pembuatan Fitur Teks Gabungan:**

   * Semua fitur teks (overview, genres, cast, crew, keywords) digabungkan menjadi satu kolom `soup` yang akan digunakan untuk representasi vektor.

## Modeling

Pendekatan yang digunakan adalah **Content-Based Filtering**, dengan representasi teks menggunakan CountVectorizer.

### Tahapan Model

1. **Vectorization:**

   * Menggunakan `CountVectorizer` untuk mengubah kolom `soup` menjadi matriks fitur (count\_matrix).

2. **Similarity Calculation:**

   * Awalnya, pendekatan Cosine Similarity sempat direncanakan untuk mengukur kesamaan antar film berdasarkan representasi vektor tersebut. Namun, pendekatan ini memerlukan perhitungan matriks kesamaan berukuran besar (n x n), yang menyebabkan penggunaan memori sangat tinggi pada Google Colab dan berujung pada kegagalan eksekusi (RAM out of memory).

  * Solusi Alternatif: K-Nearest Neighbors (KNN)
Sebagai alternatif, digunakan pendekatan K-Nearest Neighbors (KNN) menggunakan NearestNeighbors dari Scikit-learn. Algoritma ini mencari n film terdekat terhadap film yang diminta berdasarkan jarak kosinus (cosine distance) tanpa membangun seluruh matriks kesamaan sekaligus, sehingga jauh lebih hemat memori.

3. **Top-N Recommendation:**

   * Fungsi `get_recommendations(title, n)` mengembalikan N film teratas yang paling mirip dengan film yang dimasukkan.

### Alternatif Model

* Collaborative Filtering tidak diterapkan karena keterbatasan data eksplisit user-rating dan keterbatasan RAM.

### Output Contoh

```python
get_recommendations('The Dark Knight')
```

Output:

* Batman Begins
* The Dark Knight Rises
* ...

## Evaluation

Pendekatan yang digunakan dalam proyek ini adalah **Content-Based Filtering** yang merekomendasikan film berdasarkan kemiripan kontennya (genre, pemeran, kata kunci, dan deskripsi). Model ini tidak menggunakan data rating eksplisit dari pengguna, sehingga tidak melakukan prediksi rating secara langsung seperti pada pendekatan **Collaborative Filtering**. Oleh karena itu, metrik evaluasi seperti **Root Mean Square Error (RMSE)** atau **Mean Absolute Error (MAE)** tidak dapat digunakan secara langsung.

Sebagai gantinya, evaluasi sistem dilakukan dengan pendekatan berikut:

### 1. **Face Validity (Kelayakan Visual)**

Sistem diuji dengan memasukkan beberapa judul film populer, lalu diperiksa secara manual apakah film-film yang direkomendasikan memang memiliki kesamaan konteks dan genre. Misalnya:

```python
get_recommendations('The Dark Knight')
```

**Hasil:**
- Batman Begins
- The Dark Knight Rises
- Man of Steel  
(dll)

Hasil rekomendasi tersebut **relevan dan masuk akal**, menunjukkan bahwa sistem mampu menangkap kemiripan tematik antar film superhero.

### 2. **Top-N Precision (Evaluasi Manual)**

Dilakukan evaluasi kasar terhadap **10 film teratas** yang direkomendasikan untuk beberapa film. Jika dari 10 film yang direkomendasikan, 7 film dianggap relevan oleh penilai manusia, maka:

$$ 
\text{Precision@10} = \frac{7}{10} = 0.7 
$$


Evaluasi ini tentu bersifat subyektif dan terbatas, tetapi tetap memberi indikasi performa sistem secara praktis.

### 3. **Alasan Tidak Digunakan Cosine Similarity Full Matrix**

Awalnya sistem dirancang menggunakan **cosine similarity** antar vektor film. Namun, perhitungan matriks kesamaan penuh (n × n) menyebabkan konsumsi memori sangat tinggi, terutama di lingkungan Google Colab dengan keterbatasan RAM. Akibatnya, sistem gagal berjalan karena kehabisan memori.

Sebagai alternatif, digunakan algoritma **K-Nearest Neighbors (KNN)** dari Scikit-learn dengan metrik `cosine`, yang menghitung kemiripan antar film hanya saat dibutuhkan (lazy evaluation). Pendekatan ini jauh lebih efisien secara memori dan memberikan hasil serupa.

### 4. **Keterbatasan Evaluasi**

Beberapa keterbatasan sistem evaluasi ini antara lain:
- Tidak tersedia ground truth rating dari pengguna untuk evaluasi kuantitatif.
- Tidak ada informasi eksplisit tentang preferensi pengguna individu.
- Evaluasi bersifat manual dan subyektif.

---

## Kesimpulan Evaluasi

Meskipun tanpa metrik RMSE atau MAE, sistem Content-Based Filtering yang dibangun telah menunjukkan performa baik secara **kualitatif**. Rekomendasi yang dihasilkan akurat secara tematik dan logis, menunjukkan sistem mampu memahami konten dan konteks film dengan efektif.


