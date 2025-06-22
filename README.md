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

- **Jumlah data**: 10.329 baris × 3 kolom (movies dataset) & 105.339 baris × 3 kolom (ratings dataset)
- **Kondisi data**:
  - Tidak ditemukan missing value
  - Tidak ada duplikasi data
- **Sumber data**: [Movielens dataset](https://www.kaggle.com/datasets/ayushimishra2809/movielens-dataset).

### Fitur Dataset:

| Fitur              | Deskripsi                                          |
|--------------------|----------------------------------------------------|
| movieId        | ID unik film           |
| title        | Judul film |
| genres           | Genre film |
| rating      | Nilai rating (0.5-5.0 |

---

## Data Preparation

- **Gabungan fitur**: Menggabungkan title dan genres menjadi text untuk proses embedding.
- **Pembersihan teks**: Menghapus genre kosong & normalisasi teks untuk meningkatkan kualitas embedding
- **Embedding dengan SBERT**: Menggunakan model all-MiniLM-L6-v2 untuk menghasilkan representasi vektor berdimensi 384 untuk setiap film

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
| Precision@5        | 0.0680     |
| Recall@5        | 0.0097 |
| NCDG@5           | 0.0815 |

---

### Analisis Hasil

Berdasarkan hasil evaluasi, model cukup baik dalam mengurutkan film yang mirip, tetapi keterbatasan konten deskriptif (hanya judul & genre) membuat relevansi personal masih rendah.
 
---

## Business Impact

✅ Scalable untuk cold-start user: CBF bisa digunakan meskipun tidak ada data interaksi.
✅ Mudah diintegrasikan dengan metadata film: Dapat dipadukan dengan rating, genre, sinopsis.
✅ Bermanfaat untuk rekomendasi awal pengguna baru di platform streaming seperti Netflix atau Disney+.
✅ Bisa dikombinasikan dengan Collaborative Filtering untuk hasil yang lebih personal (hybrid model).

---

## Kesimpulan

Proyek ini berhasil membangun sistem rekomendasi film menggunakan pendekatan Content-Based Filtering dengan Sentence-BERT. Dengan memanfaatkan representasi semantik dari judul dan genre film, sistem mampu memberikan rekomendasi berdasarkan kemiripan konten. Evaluasi menggunakan Precision@5, Recall@5, dan NDCG@5 menunjukkan bahwa meskipun model belum sangat akurat, pendekatan ini relevan untuk cold-start problem dan bisa menjadi fondasi sistem rekomendasi hybrid di masa depan.
