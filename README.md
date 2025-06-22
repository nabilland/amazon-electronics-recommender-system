# Laporan Proyek Machine Learning - Nabilla Nurulita Dewi

## Project Domain

Seiring meningkatnya adopsi platform e-commerce, konsumen semakin dihadapkan pada tantangan information overload, yaitu kondisi ketika jumlah pilihan produk yang tersedia sangat besar hingga menyulitkan proses pencarian dan pengambilan keputusan [1].  Di platform besar seperti Amazon, ribuan produk elektronik ditampilkan setiap saat, namun pengguna tidak selalu mendapatkan dukungan sistematis dalam menemukan produk yang relevan atau serupa dengan kebutuhannya.

Tanpa adanya sistem rekomendasi yang efisien, pengguna harus menelusuri produk secara manual, yang dapat menyebabkan penurunan kepuasan, hilangnya peluang pembelian, hingga tingginya bounce rate [2]. Pendekatan berbasis collaborative filtering memang banyak digunakan, namun memiliki kelemahan seperti cold-start dan ketergantungan pada data interaksi pengguna [1].

Untuk mengatasi masalah tersebut, diperlukan sistem rekomendasi berbasis konten (Content-Based Filtering) yang mampu memberikan saran produk serupa dengan memanfaatkan informasi yang tersedia pada produk itu sendiri, seperti nama dan kategori. Pendekatan ini sangat cocok diterapkan dalam kondisi terbatasnya data pengguna dan dapat menjadi solusi awal yang efektif untuk meningkatkan pengalaman pengguna dalam menjelajahi katalog produk [3].

**References:**

[1] [A Systematic Review and Research Perspective on Recommender System](https://doi.org/10.1186/s40537-022-00592-5) 

[2] [Product Recommendation System a Comprehensive Review](https://doi.org/10.1088/1757-899X/1022/1/012021)

[3] [Recommendation Systems For E-commerce Systems An Overview](https://doi.org/10.1088/1742-6596/1897/1/012024)  

---

## Business Understanding

### Problem Statements

- Pengguna menghadapi kesulitan dalam menavigasi dan menemukan produk elektronik yang relevan di tengah banyaknya pilihan yang tersedia di platform e-commerce seperti Amazon.
- Ketiadaan data historis interaksi pengguna menyebabkan pendekatan berbasis collaborative filtering menjadi kurang optimal, terutama dalam menghadapi masalah cold-start dan sparsity.
- Diperlukan sistem yang dapat merekomendasikan produk serupa berdasarkan kemiripan konten, guna meningkatkan pengalaman pengguna, efisiensi pencarian, serta mendorong potensi konversi pembelian.

### Goals

- Mengembangkan sistem rekomendasi produk elektronik berbasis konten menggunakan data katalog produk Amazon.
- Menghasilkan daftar rekomendasi Top-5 produk yang paling relevan untuk setiap produk input berdasarkan kemiripan fitur.
- Mengevaluasi performa sistem menggunakan metrik alternatif, yaitu rata-rata rating dan hit rate, sebagai pengganti evaluasi berbasis interaksi pengguna yang tidak tersedia.
  
### Solution Statements

- Mengimplementasikan pendekatan Content-Based Filtering (CBF) dengan memanfaatkan informasi deskriptif seperti nama produk, kategori utama, dan subkategori.
- Melakukan ekstraksi fitur teks menggunakan TF-IDF Vectorizer untuk membangun representasi vektor dari setiap produk.
- Mengukur tingkat kemiripan antar produk menggunakan metrik Cosine Similarity untuk menghasilkan daftar rekomendasi produk yang paling relevan secara konten.

---

## Data Understanding

Dataset diambil dari Kaggle dengan judul: [Electronics Products from Amazon (10K Items)]([https://www.kaggle.com/datasets/mrsimple07/energy-consumption-prediction/data](https://www.kaggle.com/datasets/akeshkumarhp/electronics-products-amazon-10k-items/data).

### Informasi Dataset:

- **Jumlah data**: 9.600 baris × 10 kolom
- **Kondisi data**:
  - Missing value ditemukan pada kolom ratings, no_of_ratings, discount_price, dan actual_price.
  - Tidak ada duplikasi data
- **Sumber data**: [Electronics Products from Amazon (10K Items)](https://www.kaggle.com/datasets/akeshkumarhp/electronics-products-amazon-10k-items/data).

### Fitur Dataset:

| Fitur              | Deskripsi                                          |
|--------------------|----------------------------------------------------|
| name        | Nama produk elektronik.           |
| main_category        | Kategori utama tempat produk tersebut termasuk. |
| sub_category           | Subkategori yang memberikan klasifikasi lebih rinci terhadap produk |
| image      | Tautan ke gambar berkualitas tinggi yang merepresentasikan produk. |
| link          | Tautan referensi ke halaman produk di situs Amazon. |
| ratings          | Rata-rata rating yang diberikan oleh pelanggan Amazon terhadap produk. |
| no_of_ratings      | Jumlah rating yang diterima produk di Amazon. |
| discount_price    | Harga produk setelah mendapatkan diskon. |
| actual_price          | Harga asli atau harga yang direkomendasikan oleh produsen. |

---

## Data Preparation

- **Menghapus kolom yang tidak relevan**  
  Dilakukan penghapusan kolom `image`, `link`, dan `Unnamed: 0` karena tidak memberikan kontribusi terhadap sistem rekomendasi dan hanya menambah kompleksitas data.

- **Membersihkan dan mengonversi kolom harga**  
  Kolom `discount_price` dan `actual_price` dibersihkan dari simbol mata uang (₹) dan tanda koma, lalu dikonversi ke tipe numerik untuk memungkinkan analisis statistik dan evaluasi harga produk.

- **Mengonversi kolom rating dan jumlah rating ke numerik**  
  Kolom `ratings` dan `no_of_ratings` dikonversi ke tipe numerik agar dapat digunakan dalam evaluasi kualitas produk yang direkomendasikan.

- **Menangani nilai yang hilang (missing values)**  
  Missing values pada `ratings` dan `no_of_ratings` diisi dengan nilai 0 untuk mempertahankan integritas data dan memastikan setiap produk tetap dapat dianalisis.

- **Membentuk fitur gabungan untuk representasi teks**  
  Kolom `name`, `main_category`, dan `sub_category` digabung menjadi fitur baru `combined_features` yang akan digunakan sebagai input dalam proses tokenisasi dan pembentukan vektor pada tahap vectorization.

---

## Modeling

### Vectorization & Similarity

- Representasi teks dari setiap produk dalam fitur `combined_features` diubah menjadi vektor numerik menggunakan **TF-IDF Vectorizer**, dengan penghapusan *stopwords* untuk menjaga kualitas informasi.
- Hasil vektorisasi digunakan untuk menghitung **kemiripan antar produk** menggunakan metrik **Cosine Similarity**.
- Proses ini menghasilkan matriks berukuran **9600 x 9600** yang merepresentasikan derajat kemiripan antar semua pasangan produk dalam dataset.

### Top-N Recommendation

- Untuk setiap input produk, sistem mencari indeks produk yang sesuai berdasarkan nama (menggunakan pencocokan string parsial).
- Sistem kemudian memilih **5 produk teratas** dengan skor Cosine Similarity tertinggi, selain produk itu sendiri.
- Output berupa tabel yang menampilkan informasi produk: **nama**, **kategori utama**, **subkategori**, **rating**, dan **harga diskon**.

### Contoh Rekomendasi

- **Input**: \"headphones\"
- **Output**: 5 produk lain dalam kategori yang sama atau serupa, yang memiliki deskripsi paling relevan dan mirip secara konten.

| Index | Name                                                                 | Main Category       | Sub Category     | Ratings | Discount Price |
|-------|----------------------------------------------------------------------|---------------------|------------------|---------|----------------|
| 382   | boAt BassHeads 100 in-Ear Wired Headphones with Mic                 | tv, audio & cameras | All Electronics  | 4.1     | 399.0          |
| 77    | boAt BassHeads 900 On-Ear Wired Headphones with Mic                | tv, audio & cameras | All Electronics  | 4.2     | 599.0          |
| 280   | boAt Bassheads 100 Wired in Ear Earphones with Mic                 | tv, audio & cameras | All Electronics  | 4.1     | 399.0          |
| 209   | boAt Bassheads 105 Wired in Ear Earphones with Mic                 | tv, audio & cameras | All Electronics  | 4.0     | 449.0          |
| 848   | boAt Bassheads 100 Wired in Ear Earphones with Mic                 | tv, audio & cameras | All Electronics  | 4.1     | 399.0          |

---

## Evaluation

### Evaluation Metrics

Karena tidak tersedia data eksplisit berupa interaksi pengguna (seperti riwayat pembelian atau klik), maka evaluasi dilakukan menggunakan pendekatan berbasis konten dengan dua metrik alternatif berikut:

- **Average Rating**  
  Mengukur rata-rata nilai rating dari produk-produk yang direkomendasikan. Semakin tinggi nilainya, semakin baik kualitas produk yang disarankan oleh sistem.

- **Hit Rate (Top-5)**  
  Mengukur persentase keberhasilan sistem dalam menghasilkan rekomendasi yang tidak kosong untuk setiap input produk. Metrik ini menunjukkan cakupan dan ketersediaan rekomendasi dalam sistem.

### Evaluation Results

- **Rata-rata rating dari produk rekomendasi:** 4.02  
- **Hit Rate pada Top-5 rekomendasi (berdasarkan 100 sampel acak):** 100%

---

### Analisis Hasil

- Berdasarkan hasil evaluasi, sistem rekomendasi menghasilkan **rata-rata rating sebesar 4.02** pada produk-produk yang direkomendasikan. Angka ini menunjukkan bahwa rekomendasi yang diberikan cenderung mengarah pada produk dengan kualitas baik, sebagaimana tercermin dari penilaian pengguna.

- Selain itu, **hit rate sebesar 100%** pada 100 sampel acak menunjukkan bahwa sistem mampu memberikan rekomendasi untuk seluruh produk yang diuji. Hal ini mengindikasikan bahwa sistem memiliki **cakupan (coverage)** yang sangat baik dan dapat secara konsisten menyarankan produk serupa dalam katalog, tanpa kegagalan pencarian.
  
---

## Business Impact

✅ **Sistem rekomendasi mampu memberikan saran produk yang relevan dan berkualitas**, terbukti dari rata-rata rating rekomendasi sebesar 4.02. Ini menunjukkan bahwa sistem secara konsisten menyarankan produk-produk yang dinilai baik oleh pengguna.

✅ **Bagi platform Amazon**, sistem ini berperan penting dalam membantu pengguna menemukan produk alternatif secara instan, tanpa harus melakukan pencarian manual. Hal ini dapat meningkatkan user engagement dan memperbesar peluang pembelian impulsif.

✅ **Dengan hit rate 100%**, sistem menunjukkan kemampuan untuk memberikan rekomendasi untuk seluruh produk yang diuji, menunjukkan cakupan yang luas di seluruh katalog Amazon, termasuk untuk produk yang kurang populer.

✅ Berdasarkan temuan Roy & Dutta (2022), pendekatan Content-Based Filtering sangat sesuai untuk kasus seperti Amazon yang memiliki katalog besar namun tidak selalu memiliki data interaksi pengguna secara lengkap. Sistem ini dapat menjadi solusi awal yang efektif dalam membangun fondasi sistem rekomendasi, terutama untuk pengguna baru (*cold-start users*) maupun produk baru.


---

## Kesimpulan

Proyek ini berhasil membangun sebuah sistem rekomendasi produk elektronik berbasis konten menggunakan pendekatan Content-Based Filtering (CBF). Dengan mengandalkan representasi teks melalui TF-IDF Vectorizer dan pengukuran kemiripan menggunakan Cosine Similarity, sistem mampu menyarankan Top-5 produk yang paling relevan untuk setiap input. Hasil evaluasi menunjukkan bahwa sistem menghasilkan rekomendasi dengan rata-rata rating tinggi (4.02) dan cakupan penuh (hit rate 100%), bahkan tanpa mengandalkan data historis pengguna. Hal ini menegaskan bahwa pendekatan CBF sangat efektif dalam mengatasi keterbatasan data interaksi (*cold-start problem*), dan tetap mampu memberikan hasil yang berkualitas tinggi. Secara keseluruhan, sistem rekomendasi ini berpotensi dapat diintegrasikan ke dalam platform e-commerce berskala besar seperti Amazon, guna meningkatkan personalization, mempercepat proses pencarian, dan mendorong konversi pembelian melalui rekomendasi produk yang relevan. Pendekatan ini juga dapat menjadi pondasi awal untuk sistem rekomendasi hybrid di masa depan.
