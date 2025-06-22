# Laporan Proyek Machine Learning - Nabilla Nurulita Dewi

## Project Domain

Dengan semakin meluasnya katalog film digital, pengguna kini menghadapi tantangan dalam menemukan film yang sesuai dengan preferensi mereka. Dalam dunia yang dipenuhi dengan pilihan, sistem rekomendasi muncul sebagai solusi krusial untuk menyederhanakan proses pengambilan keputusan dan meningkatkan kepuasan pengguna. [2]

Berdasarkan Joseph & Nair [2] dan [3], Content-Based Filtering terbukti efektif dalam mengatasi masalah cold-start dan data sparsity—dua masalah umum dalam sistem berbasis pengguna. Metode ini menganalisis fitur-fitur konten dari item, seperti deskripsi atau genre, untuk menyarankan item yang mirip.

Proyek ini membangun sistem rekomendasi film dengan pendekatan Content-Based Filtering menggunakan representasi embedding dari Sentence-BERT untuk mendapatkan pemahaman semantik yang lebih baik dari teks judul dan genre film. Sistem diuji pada dataset Movie dan dievaluasi dengan metrik evaluasi Top-N yang relevan.

**References:**

[1] [A Systematic Review and Research Perspective on Recommender System](https://doi.org/10.1186/s40537-022-00592-5)

[2] [A Comparative Study of Collaborative Movie Recommendation System. IEEE.](https://doi.org/10.1109/ICEARS53579.2022.9752015)

[3] [Model for Movie Recommendation System using Collaborative Filtering and Content-Based Filtering Techniques. IEEE.](https://doi.org/10.1109/CICTN64563.2025.10932600)

---

## Business Understanding

### Problem Statements

- Pengguna kesulitan menemukan film yang relevan di tengah ribuan pilihan yang tersedia di platform digital, menyebabkan pengalaman pengguna yang tidak optimal.
- Tidak semua pengguna memiliki riwayat interaksi atau preferensi historis, yang membuat pendekatan Collaborative Filtering menjadi kurang efektif (terutama pada kasus cold-start).
- Platform membutuhkan sistem yang dapat memberikan rekomendasi secara otomatis berdasarkan konten film itu sendiri, tanpa bergantung pada data pengguna.

### Goals

- Mengembangkan sistem rekomendasi film berbasis konten yang mampu menyarankan film serupa menggunakan informasi deskriptif dari film (judul dan genre).
- Menghasilkan rekomendasi Top-5 film yang relevan untuk setiap film input menggunakan pendekatan semantic text embedding.
- Mengevaluasi performa sistem menggunakan metrik Top-N (Precision@5, Recall@5, dan NDCG@5) guna menilai kualitas dan urutan relevansi hasil rekomendasi.

### Solution Statements

- Menggunakan Content-Based Filtering berbasis embedding kalimat dari SBERT.
- Merepresentasikan judul dan genre film sebagai teks gabungan.
- Menghitung kemiripan antar film menggunakan Cosine Similarity.
- Memberikan rekomendasi berdasarkan kedekatan semantik.

---

## Data Understanding

Dataset diambil dari Kaggle dengan judul: [Movielens dataset](https://www.kaggle.com/datasets/ayushimishra2809/movielens-dataset).

### Informasi Dataset:
**1️⃣ Dataset `movies.csv`**
- **Ukuran**: 10.329 baris × 3 kolom  
- **Fitur**:
  - `movieId`: ID unik untuk tiap film  
  - `title`: Judul film beserta tahun rilis  
  - `genres`: Genre film (dipisahkan oleh simbol `|`)

**2️⃣ Dataset `ratings.csv`**
- **Ukuran**: 105.339 baris × 4 kolom (`timestamp` tidak digunakan)  
- **Fitur**:
  - `userId`: ID unik pengguna  
  - `movieId`: ID film yang diberi rating  
  - `rating`: Skor penilaian (skala 0.5–5.0)  
  - `timestamp`: Timestamp penilaian (**tidak digunakan** dalam proyek ini)

### Kondisi Data
- **Missing Values**: Tidak ditemukan missing values pada kolom yang digunakan (`movieId`, `title`, `genres`, `rating`).  
- **Duplikasi**: Tidak terdapat baris duplikat pada dataset `movies.csv`.  
- **Outlier**: Ditemukan beberapa nilai ekstrem pada kolom `rating`, dengan nilai minimum 0.5 yang secara distribusi menyimpang dari rata-rata (mean = 3.51). Meskipun demikian, nilai tersebut masih valid secara konteks sistem rating.
Kalau kamu mau aku tambahkan bagian lain seperti Modeling, Evaluation, atau License,
---

## Data Preparation

Beberapa tahapan persiapan data dilakukan sebelum masuk ke proses embedding dan pemodelan:
- **Pembersihan teks**:
  - Dilakukan normalisasi teks: konversi ke huruf kecil, penghapusan tanda baca, dan penghapusan genre kosong (`(no genres listed)`) untuk memastikan kualitas embedding yang lebih baik.
  
- **Gabungan fitur konten**:
  - Kolom `title` dan `genres` dari dataset `movies.csv` digabungkan menjadi satu string teks sebagai representasi awal konten film.

- **Embedding dengan SBERT**:
  - Menggunakan model **`all-MiniLM-L6-v2`** dari **Sentence-BERT** untuk mentransformasikan teks gabungan menjadi vektor berdimensi 384. Representasi ini akan digunakan dalam perhitungan kemiripan antar film.
 
- **Filtering data**: 
  - Hanya menyertakan rating dengan nilai ≥ 4.0 sebagai indikator bahwa pengguna menyukai film tersebut.
  - Hanya pengguna yang telah memberi rating ≥ 4.0 pada minimal 6 film yang disertakan. Hal ini bertujuan untuk mengurangi noise dari pengguna pasif dan memastikan kualitas data untuk pelatihan serta evaluasi sistem rekomendasi.

---

## Modeling

### Sistem Rekomendasi Content-Based Filtering (CBF)

Pendekatan Content-Based Filtering merekomendasikan film berdasarkan kemiripan kontennya, bukan perilaku pengguna. Dalam proyek ini, fitur konten diambil dari kombinasi judul film dan genre, yang kemudian diproses menjadi representasi numerik menggunakan Sentence-BERT (SBERT) untuk memahami makna semantik dari deskripsi film.

### Algoritma Rekomendasi dan Cosine Similarity

- Setiap film direpresentasikan dalam bentuk embedding vektor berdimensi 384 menggunakan model pre-trained SBERT (all-MiniLM-L6-v2).
- Sistem memilih Top-5 film dengan skor kemiripan tertinggi terhadap film input, selain film itu sendiri.
- Hasil rekomendasi ditampilkan dalam bentuk tabel berisi movieId, judul, dan genre.

### Top-N Recommendation

- Untuk setiap film input, sistem mencari indeks film tersebut berdasarkan movieId.
- Sistem kemudian menghitung kemiripan embedding dengan semua film lain, lalu mengurutkannya secara menurun.
- Output berupa daftar 5 film teratas yang memiliki kemiripan konten tertinggi dengan film input, ditampilkan dalam format tabel yang menyajikan movieId dan judul film.

---

## Evaluation

### Evaluation Metrics

Untuk mengukur relevansi dan kualitas sistem rekomendasi berbasis konten, digunakan metrik evaluasi berikut:

- **Precision@5:** Mengukur proporsi item yang relevan dalam Top-5 hasil rekomendasi.
- **Recall@5:** Mengukur proporsi item relevan yang berhasil direkomendasikan dari seluruh item yang relevan.
- **NDCG@5:** Mengukur ranking relevansi item dalam rekomendasi, mempertimbangkan posisi kemunculan item yang relevan.

### Eksperimen Evaluasi
- Menggunakan 50 pengguna yang memiliki >5 rating ≥3.5
- Untuk tiap user: ambil 1 film yang disukai sebagai query, film lainnya dianggap ground truth
- Hasil rekomendasi dibandingkan dengan ground truth user tersebut

| Metrik              | Nilai rata-rata                                          |
|--------------------|----------------------------------------------------|
| Precision@5        | 0.0833     |
| Recall@5        | 0.0032 |
| NCDG@5           | 0.0905 |

---

### Analisis Hasil

Nilai Precision@5 yang mendekati 0.08 mengindikasikan bahwa, secara rata-rata, sekitar 8% dari hasil rekomendasi (Top-5) merupakan film yang juga disukai oleh pengguna berdasarkan data historis. Sementara itu, nilai Recall@5 yang rendah menunjukkan bahwa sistem hanya berhasil menemukan sebagian sangat kecil dari seluruh film relevan yang disukai pengguna. Hal ini dapat terjadi karena pendekatan Content-Based Filtering (CBF) tidak mempertimbangkan preferensi pengguna lain (tidak personal), dan hanya fokus pada konten item seperti judul dan genre.

Meskipun demikian, nilai NDCG@5 yang lebih tinggi dibanding Recall menunjukkan bahwa item yang relevan cenderung muncul di peringkat atas hasil rekomendasi, sehingga sistem masih memiliki kemampuan dalam mengurutkan rekomendasi secara semantik.

---

### Contoh Hasil Rekomendasi Sistem

| userId | Liked Movies | Top-5 Recommendations |
|--------|--------------|------------------------|
| 317 | One Fine Day (1996), I Know What You Did Last Summer (1997), Titanic (1997), Friday the 13th (1980), Outsiders, The (1983), Secret of NIMH, The (1982), Karate Kid, Part III, The (1989), Thirteenth Floor, The (1999), Live and Let Die (1973), Moonraker (1979), City Slickers (1991), A-Team, The (2010), Inception (2010), King's Speech, The (2010), Adjustment Bureau, The (2011), Horrible Bosses (2011), Help, The (2011), Descendants, The (2011), Extremely Loud and Incredibly Close (2011), One for the Money (2012), October Baby (2011), Lucky One, The (2012), Jumping the Broom (2011) | Thirteen (2003), 2012 (2009), Extracted (2012), In Darkness (2011), Young Adult (2011) |
| 298 | Faces (1968), Manhattan (1979), Buffalo '66 (a.k.a. Buffalo 66) (1998), Blue Velvet (1986), Ghostbusters II (1989), Man Bites Dog (C'est arrivé près de chez vous) (1992), Mirror, The (Zerkalo) (1975), Eraserhead (1977), Mulholland Drive (2001), Wild at Heart (1990), Naked Lunch (1991), Persona (1966), 3 Women (Three Women) (1977), Topo, El (1970), Holy Mountain, The (Montaña sagrada, La) (1973), Inland Empire (2006), House (Hausu) (1977), Dogtooth (Kynodontas) (2009) | Extracted (2012), [REC] (2007), 21 (2008), Wild (2014), Images (1972) |
---

## Business Impact

✅ Scalable untuk kasus cold-start — Content-Based Filtering (CBF) tetap dapat merekomendasikan item meskipun tidak tersedia data interaksi pengguna.

✅ Integrasi mudah dengan metadata film — Sistem dapat diperluas menggunakan sinopsis, rating, bahkan cast atau director sebagai fitur tambahan.

✅ Cocok untuk rekomendasi awal pengguna baru — Platform streaming seperti Netflix dan Disney+ dapat memanfaatkan pendekatan ini untuk memulai profil rekomendasi sebelum cukup data interaksi terkumpul.

✅ Potensial untuk dikombinasikan dalam model hybrid — CBF dapat digabung dengan Collaborative Filtering untuk meningkatkan personalisasi dan akurasi rekomendasi di tahap lanjutan.

---

## Kesimpulan

Proyek ini berhasil mengembangkan sistem rekomendasi film berbasis Content-Based Filtering menggunakan representasi semantik dari judul dan genre film melalui Sentence-BERT. Sistem memberikan rekomendasi berdasarkan kemiripan konten antar film, tanpa memerlukan data interaksi pengguna.

Evaluasi dengan metrik Top-N seperti Precision@5, Recall@5, dan NDCG@5 menunjukkan bahwa sistem mampu menghasilkan rekomendasi relevan meskipun belum sangat presisi. Hasil ini mencerminkan karakteristik CBF yang efektif dalam menangani cold-start problem, dan menunjukkan potensi sistem untuk dikembangkan menjadi sistem rekomendasi hybrid yang lebih adaptif dan personal di masa mendatang.
