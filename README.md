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

Dataset yang digunakan diambil dari Kaggle: [The Movies Dataset](https://www.kaggle.com/datasets/rounakbanik/the-movies-dataset).Dataset ini berisi file-file metadata untuk semua 45.000 film yang tercantum dalam Dataset MovieLens. Dataset ini terdiri dari film-film yang dirilis pada atau sebelum Juli 2017. Poin data meliputi pemeran, kru, kata kunci plot, anggaran, pendapatan, poster, tanggal rilis, bahasa, perusahaan produksi, negara, jumlah vote TMDB, dan rata-rata vote.

Dataset ini juga memiliki file yang berisi 26 juta peringkat dari 270.000 pengguna untuk semua 45.000 film. Peringkat diberikan dalam skala 1-5 dan diperoleh dari situs web resmi GroupLens.

Dataset sendiri terdiri dari beberapa file CSV, diantaranya :
1. `movies_metadata.csv`
Berisi informasi deskriptif (metadata) tentang film sebagai berikut :
* Adult	               :  Apakah film ini untuk dewasa (True/False)
* belongs_to_collection :	Nama koleksi/franchise (jika ada)
* budget                :	Anggaran produksi film (dalam USD)
* genres	               : Genre film (dalam format JSON string)
* homepage              :	Situs resmi film
* id                    :	ID unik untuk film
* imdb_id               :	ID IMDb film
* original_language     :	Bahasa asli film
* original_title        :	Judul asli film
* overview             :	Ringkasan cerita (deskripsi film)
* popularity           :	Skor popularitas berdasarkan aktivitas pengguna
* poster_path          :	Path gambar poster film
* production_companies :	Daftar perusahaan produksi (JSON string)
* production_countries :	Negara produksi (JSON string)
* release_date         :	Tanggal rilis
* revenue              :	Pendapatan film (USD)
* runtime              :	Durasi film (menit)
* spoken_languages     :	Bahasa yang digunakan dalam film
* status               :	Status film (Released, Post Production, dll.)
* tagline              :	Tagline atau slogan film
* title                :	Judul film
* video                :	Apakah film ini berupa video (True/False)
* vote_average         :	Rata-rata skor rating pengguna
* vote_count           :	Jumlah rating pengguna

2. `ratings.csv`
Berisi data rating pengguna terhadap film sebagai berikut :
* userId                :	ID pengguna
* movieId               :	ID film
* rating                :	Nilai rating (skala 0.5–5.0)
* timestamp             :	Waktu pemberian rating (format UNIX)

3. `credits.csv`
Berisi informasi aktor dan kru dalam film sebagai berikut :
* id                    :	ID film (sama dengan movies_metadata)
* cast                  :	Daftar aktor (format JSON string)
* crew                  :	Daftar kru film (format JSON string)

4. `keywords.csv`
Berisi kata kunci relevan yang menggambarkan isi film sebagai berikut :
* id                    :	ID film
* keywords              :	Daftar keyword (format JSON string)

5. `links.csv`
Menghubungkan movieId dari file ratings.csv ke id di TMDB dan IMDb yang berisi sebagai berikut :
* movieId               :	ID film dari ratings.csv
*  imdbId                :	ID film versi IMDb
*  tmdbId                :	ID film versi TMDB (movies_metadata.csv)

Sebenarnya ada dua file .csv lain, yaitu links_small.csv dan ratings_small.csv, tetapi itu hanyalah versi kecil dari links dan ratings, atau versi dengan data yang lebih sedikit, sehingga dua dataset ini tidak akan kita gunakan.
  
Dari 5 file csv yang ada, kita hanya memakai 3 file karena kita memakai model Content-Based Filtering. 3 file tersebut yaitu :

* `movies_metadata.csv`: berisi informasi umum tentang film
* `credits.csv`: berisi informasi pemeran dan kru
* `keywords.csv`: berisi kata kunci deskriptif film

Berikut jumlah data dan tipe data dari masing-masing file :

**Pada file `movies_metadata.csv`** berikut rinciannya :
  * adult                  : 45466 (Object) 
  * belongs_to_collection  : 4494  (Object)
  * budget                 : 45466 (Object)
  * genres                 : 45466 (Object)
  * homepage               : 7782  (Object)
  * id                     : 45466 (Object) 
  * imdb_id                : 45449 (Object)
  * original_language      : 45455 (Object)
  * original_title         : 45466 (Object)
  * overview               : 44512 (Object)
  * popularity             : 45461 (Object)
  * poster_path            : 45080 (Object)
  * production_companies   : 45463 (Object)
  * production_countries   : 45463 (Object)
  * release_date           : 45379 (Object)
  * revenue                : 45460 (float64)
  * runtime                : 45203 (float64)
  * spoken_languages       : 45460 (Object)
  * status                 : 45379 (Object)
  * tagline                : 20412 (Object)
  * title                  : 45460 (Object)
  * video                  : 45460 (Object)
  * vote_average           : 45460 (float64)
  * vote_count             : 45460 (float64)

Terlihat kondisi data pada file `movies_metadata.csv` masih banyak missing value, dan untuk tipe datanya berupa Object sebanyak 20 kolom dan float64 sebanyak 4 kolom.

**Pada file `credits.csv`** berikut rinciannya:
  * cast    : 45476 (Object)
  * crew    : 45476 (Object)
  * id      : 45476 (int64)

Terlihat kondisi  data pada file `credits.csv` tidak ada missing value, dan untuk tipe daatanya berupa Object sebanyak 2 kolom dan int64 sebanyak 1 kolom.

**Pada file `keywords.csv`** berikut  rinciannya:
  * id        : 46419 (int64)
  * keywords  : 46419 (Object)

Terlihat kondisi data  pada file `keywords.csv` tidak ada missing value, dan untuk tipe datanya berupa Object sebanyak 1 kolom dan int64 sebanyak 1 kolom.

Fitur-fitur penting untuk pemodelan:

* `id`: ID unik film
* `title`: Judul film
* `genres`: Genre film
* `overview`: Sinopsis film
* `cast`: Daftar pemeran utama
* `crew`: Daftar kru, termasuk sutradara
* `keywords`: Kata kunci dari metadata film

Selain itu untuk mengerti lebih dalam terkait data, saya melakukan visualisasi untuk melihat genre terbanyak yang ada dalam dataset dengan menggunakan diagram batang horizontal, hasilnya genre drama adalah genre terbanyak.

## Data Preparation

### Teknik dan Alasan Data Preparation

1. **Konversi dan Filter ID,serta Penanganan Duplikasi Data :**
   ID film dikonversi ke integer, dan data dengan ID tidak valid dihapus. Selain itu, kita juga membersihkan data dari duplikasi yang ada, khususnya pada kolom `id` dengan metode
   
   ```python
   drop_duplicates(subset='id')
   ```

   Ini dilakukan untuk memastikan proses penggabungan antar dataset berjalan lancar.

2. **Penggabungan Dataset:**
   Dataset `credits` dan `keywords` digabungkan dengan `movies_metadata` berdasarkan kolom `id` untuk membuat semua dataset yang penting dan digunakan menjadi satu.

3. **Ekstraksi Informasi Penting:**

   * `genres`, `cast`, `crew`, dan `keywords` disimpan dalam bentuk list of strings.
   * Fungsi parsing digunakan untuk mengambil top 3 aktor dan hanya nama sutradara dari kolom `crew`.
   * Ini dilakukan untuk memilih data data yang penting dan memastikan data agar bisa dilatih dalam model tanpa masalah.

4. **Ubah Format JSON Ke List:**
   * Membuat fungsi untuk mengubah format JSON menjadi List pada masing-masing kolom `genre`, `cast`,`crew`, `record`

5. **Pembersihan Missing Value:**
   * Setelah penggabungan dan ekstraksi informasi penting, terdapat beberapa missing value pada dataset gabungan ini.
   * Missing value pada kolom `overview` diisi dengan string kosong(''). Ini dilakukan karena `overview` sendiri adalah penjelasan terkait deskripsi film, lalu data yang missing lumayan banyak, +- 1000 data, sehingga data missing value diatasi dengan string kosong('')
   * Missing value pada kolom `title` diatasi dengan drop missing value. Ini dilakukan karena `title` sendiri hanya memiliki sekitar 4 missing value, sehingga bisa di drop dan tidak akan mempengaruhi model.

6. **Pembuatan Fitur Teks Gabungan:**

   * Semua fitur teks (overview, genres, cast, crew, keywords) digabungkan menjadi satu kolom `soup` yang akan digunakan untuk representasi vektor dengan mengubah list menjadi string dan dipisah spasi.

8. **Reset Index & Pembersihan Duplikasi Data:**
   * Reset  Index menggunakan .reset_index(), ini dtiujukan agar kita dapat mencari rekomendasi berdasarkan judul
   * Drop Duplicate menggunakan .drop_duplicates().Ini digunakan untuk memastikan tidak ada yg duplikat dan bisa mempengaruhi model dengan tidak baik.
   * Proses ini dilakukaan saat tahap Modelling.

## Modeling

Pendekatan yang digunakan adalah **Content-Based Filtering**, dengan representasi teks menggunakan CountVectorizer.

### Tahapan Model

1. **Vectorization:**

   * Menggunakan `CountVectorizer` untuk mengubah kolom `soup` menjadi matriks fitur (count\_matrix).
   * Parameter yang digunakan adalah `stop_words='english'`, ini digunakan untuk menghapus kata kata umum dalam Bahasa Inggris yang tidak memberi informasi penting ( Stop Words)
   * Parameter lain merupakan default parameter, seperti `lowercase = True` untuk konversi semua karakter menjadi huruf kecil sebelum Tokenizing, lalu  ada juga default parameter  `max_feature:None` artinya semua fitur digunakan, lalu `ngrame_range=(1,1)` yang artinya hanya unigram yang akan di ekstraksi.

2. **Similarity Calculation:**

   * Awalnya, pendekatan Cosine Similarity sempat direncanakan untuk mengukur kesamaan antar film berdasarkan representasi vektor tersebut. Namun, pendekatan ini memerlukan perhitungan matriks kesamaan berukuran besar (n x n), yang menyebabkan penggunaan memori sangat tinggi pada Google Colab dan berujung pada kegagalan eksekusi (RAM out of memory).

  * Solusi Alternatif: K-Nearest Neighbors (KNN)
Sebagai alternatif, digunakan pendekatan K-Nearest Neighbors (KNN) menggunakan NearestNeighbors dari Scikit-learn. Algoritma ini mencari n film terdekat terhadap film yang diminta berdasarkan jarak kosinus (cosine distance) tanpa membangun seluruh matriks kesamaan sekaligus, sehingga jauh lebih hemat memori.

  * Paremeter model KNN yang digunakan adalah `metric ='cosine'` untuk menghitung jarak kosinus antar vektor, lalu `algorithm : 'brute'`ini digunakan untuk pencarian tetangga terdekat menggunakan brute-force.Lalu Parameter lainnya  adalah `n_neighbors=` (ditentukan saat pemanggilan fungsi), dan parameter default `n_jobs=None`Ini untuk jumlah core, default 'None' artinya hanya gunakan 1 core.

3. **Fungsi Rekomendasi:**
  ```python
def rekomendasi_film(title, n=10):
    idx = indices[title]
    film_vector = count_matrix[idx]
    distances, indices_knn = model_knn.kneighbors(film_vector, n_neighbors=n+1)

    hasil = meta_filtered.iloc[indices_knn[0][1:]][['title', 'genres']]
    return hasil.reset_index(drop=True)
```

- Fungsi `rekomendasi_film` akan mengembalikan **Top-N rekomendasi** film berdasarkan input judul.

4. **Top-N Recommendation:**

Fungsi utama yang digunakan untuk menghasilkan rekomendasi adalah `rekomendasi_film(title, n=10)`.
Fungsi ini menggunakan model **K-Nearest Neighbors (KNN)** berdasarkan representasi konten (dari `CountVectorizer`) untuk mencari **N film yang paling mirip** dengan film input.

Output dari fungsi ini mencakup:

* **Judul film yang direkomendasikan**
* **Genre masing-masing film**

Contoh penggunaan:

```python
rekomendasi_film('The Dark Knight')
```

Contoh hasil:

| No | Judul Film                                | Genre                          |
| -- | ----------------------------------------- | ------------------------------ |
| 1  | The Dark Knight Rises                     | Action, Crime, Drama, Thriller |
| 2  | Batman: Under the Red Hood                | Action, Animation              |
| 3  | Batman Returns                            | Action, Fantasy                |
| 4  | Batman Forever                            | Action, Crime, Fantasy         |
| 5  | Batman: The Dark Knight Returns, Part 1   | Action, Animation              |
| 6  | Batman Begins                             | Action, Crime, Drama           |
| 7  | Batman & Bill                             | Documentary                    |
| 8  | Batman Unmasked: The Psychology of the... | Documentary                    |
| 9  | Batman                                    | Fantasy, Action                |
| 10 | Going Straight                            | Drama, Crime                   |

---

## Evaluation

Model yang dikembangkan menggunakan pendekatan **Content-Based Filtering**, yang memberikan rekomendasi berdasarkan kemiripan konten (genre, pemeran, kata kunci, dan deskripsi film). Karena model ini tidak menggunakan data eksplisit seperti rating pengguna, maka metrik evaluasi konvensional seperti **RMSE** atau **MAE** tidak relevan untuk digunakan.

Sebagai gantinya, digunakan metrik berbasis relevansi seperti:

### 🎯 Precision@N

Precision@N mengukur proporsi dari N rekomendasi teratas yang relevan. Rekomendasi dianggap relevan jika memiliki setidaknya satu genre yang sama dengan genre film acuan.

**Contoh:**

Misal pengguna memilih film `The Dark Knight` dengan genre: `Action`, `Crime`, `Drama`.

Jika sistem merekomendasikan 10 film, dan 7 di antaranya memiliki minimal satu genre yang sama, maka:


$$ 
\text{Precision@10} = \frac{7}{10} = 0.7 
$$

Bisa kita lihat pada kode, untuk sistem rekomendasi film "The Dark Knight" Mendapat 100% Precision, sedangkan "Toy Story"Mendapat 80% Precision
## Kesimpulan Evaluasi

Meskipun tanpa metrik RMSE atau MAE, sistem Content-Based Filtering yang dibangun telah menunjukkan performa baik secara **kualitatif**. Rekomendasi yang dihasilkan akurat secara tematik dan logis, menunjukkan sistem mampu memahami konten dan konteks film dengan efektif.


