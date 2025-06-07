# Laporan Proyek Machine Learning - Dea Yuliani Sabrina

## Project Overview

Rekomendasi film sangat penting untuk membantu pengguna menemukan film yang relevan dengan preferensi mereka.
Sistem rekomendasi ini dibangun dengan memanfaatkan dataset MovieLens, yang berisi rating dan metadata film.
Masalah ini harus diselesaikan untuk:
- Meningkatkan kepuasan pengguna (user satisfaction)
- Mempermudah pengguna menemukan film baru yang sesuai

**Referensi:** [Ricci, F., Rokach, L., & Shapira, B. (2015). Recommender Systems Handbook (2nd ed.). Springer.](https://doi.org/10.1007/978-1-4899-7637-6)

## Business Understanding

**Problem Statements**

1) Bagaimana cara merekomendasikan film yang sesuai dengan preferensi setiap pengguna secara otomatis?
2) Bagaimana memahami preferensi pengguna hanya berdasarkan film yang telah mereka tonton dan beri rating?
3) Bagaimana menyarankan film baru yang belum ditonton pengguna, namun relevan dengan minat mereka?
   
**Goals:**

1) Membangun sistem rekomendasi film yang mampu memberikan saran film secara otomatis dan personal untuk setiap pengguna.
2) Menggunakan riwayat rating film dari pengguna untuk memahami dan membentuk profil preferensi genre mereka.
3) Menghasilkan rekomendasi film yang belum ditonton user namun memiliki kemiripan genre tinggi dengan film yang mereka sukai sebelumnya.

**Solution Approach:**
- Collaborative Filtering: menggunakan matrix factorization (SVD)
- Content-Based Filtering: menggunakan cosine similarity antara metadata film (genre)

## Data Understanding

Dataset yang digunakan dalam proyek ini merupakan dataset tentang Movie. Dataset ini dapat diunduh di [Kaggle:MovieLens Latest Small](https://www.kaggle.com/datasets/grouplens/movielens-latest-small)

*Membaca File dataset dan Menampilkan Informasi Jumlah Data Unik*

![image](https://github.com/user-attachments/assets/8cf14ec5-2874-4b29-be59-f87db6ae626d)

1. Informasi dari Dataset movies.csv
- Ada 9.742 film unik yang tercatat dalam dataset movies. Setiap baris merepresentasikan satu film berdasarkan movieId.
- Total ada 20 genre berbeda (seperti Action, Drama, Comedy, dll). Genre di dataset biasanya disimpan dalam satu kolom dan dipisahkan dengan tanda |, misalnya Action|Adventure.

2. Informasi dari Dataset ratings.csv
- Sebanyak 610 pengguna unik telah memberikan rating.
- Dari 9742 film di dataset, 9724 film sudah pernah dirating oleh pengguna, artinya hanya 18 film belum mendapat rating.
- Terdapat 100.836 rating, yang merupakan jumlah semua rating dari seluruh user terhadap berbagai film.

3. Informasi dari Dataset tags.csv
- Hanya 58 user yang memberi tag (label/kata kunci deskriptif) terhadap film — lebih sedikit dibanding user yang memberi rating.
- Sebanyak 1.572 film telah diberi tag oleh pengguna. Sisanya tidak memiliki informasi tag.
- Terdapat 3.683 baris tag, artinya satu film bisa memiliki lebih dari satu tag, dan satu user bisa memberi tag ke beberapa film.

4. Informasi dari Dataset links.csv
- Semua 9742 film memiliki data IMDb (link ke ID IMDb) — tidak ada yang hilang. File ini biasanya digunakan untuk menghubungkan movieId ke ID eksternal seperti IMDb atau TMDb.

### Univariate Exploratory Data Analysis

*Variabel-Variabel di dataset*

| Dataset   | Variabel | Fungsi Utama |
|-------------|-----------|------------|
| links     | movieId, imdbId   | Hubungkan movieId ke data eksternal (IMDb) |
| movies       | movieId, title, genres    | Informasi konten film |
| ratings     | userId, movieId, rating, timestamp    | Interaksi user (untuk collaborative filtering) |
|tags| userId, movieId, tag, timestamp|Tag/kata kunci user (bisa untuk content-based)|

### ratings.csv

*Lihat Beberapa Data Teratas*

![image](https://github.com/user-attachments/assets/83c04843-d2a4-40af-83e0-fcb4560021a4)

Melihat 5 data teratas dari dataset rating.csv

*Cek Struktur Data*

![image](https://github.com/user-attachments/assets/f1e6649a-8fff-4852-9fd0-9627031658f5)

- Dataset terdiri dari 100.836 baris dan 4 kolom.
- Semua kolom tidak ada yang kosong (non-null).
- Kolom userId, movieId, dan timestamp bertipe integer, sedangkan rating adalah float.
- Kolom:
1. **userId:** ID unik pengguna.

2. **movieId:** ID unik film.

3. **rating:** Nilai rating yang diberikan (0.5 – 5.0).

4. **timestamp:** Waktu saat rating diberikan (dalam format UNIX time).

*Menghitung Jumlah Pengguna Unik dari Dataset Ratings*

![image](https://github.com/user-attachments/assets/5d610b54-1909-406f-9fc7-4d012fddf37b)

Total ada 610 pengguna berbeda yang memberikan rating terhadap film.

*Jumlah Pengguna yang Memberikan Rating Film*

![image](https://github.com/user-attachments/assets/5ff56adb-1add-480d-9f58-5f5fe9b2b121)

Ada 9.724 film berbeda yang menerima setidaknya satu rating.Ini menunjukkan sebaran data rating sudah cukup luas ke berbagai film.

*Mengecek Statistik deskriptif rating*

![image](https://github.com/user-attachments/assets/b2be7936-3d3e-4f88-9210-05b7afbe8449)

- Mayoritas rating ada di rentang 3.0–4.0.

- Rating cenderung positif (rata-rata 3.5).

- Tidak ada rating di bawah 0.5 atau di atas 5.0 → data valid.

*Visualisasi Distribusi Rating pada Dataset*

![image](https://github.com/user-attachments/assets/751df2f7-f7bc-4949-b535-60773874f715)

- Rating 4.0 paling banyak diberikan.

- Disusul 3.0, lalu 5.0 → pengguna lebih sering memberi nilai tinggi.

- Rating 0.5 dan 1.0 relatif jarang → menunjukkan bias pengguna terhadap hal-hal yang disukai.

*Top 10 Film dengan Rata-Rata Rating Tertinggi*

![image](https://github.com/user-attachments/assets/52e0304e-f327-4cea-870a-1b8e0d09eb75)

- 10 film ini punya rata-rata rating 5.0 → artinya setiap rating yang masuk nilainya maksimal.

- Kemungkinan film ini hanya punya 1 atau sedikit rating, jadi rata-ratanya belum mewakili banyak opini.

- Perlu dicek jumlah ratingnya untuk memastikan mereka benar-benar populer atau hanya disukai oleh 1–2 pengguna saja.

*10 Film dengan Jumlah Rating Terbanyak*

![image](https://github.com/user-attachments/assets/0d209c45-005c-4efa-93b6-dc141e6527c7)

- Ini adalah film-film paling sering dirating oleh user, jadi bisa disebut film paling populer di dataset.

- Misalnya, film dengan movieId = 356 memiliki 329 rating, berarti banyak user yang menonton dan menilainya.

*Film Terbaik Berdasarkan Rata-Rata Rating (Minimal 100 Rating)*

![image](https://github.com/user-attachments/assets/78a52df1-a117-42ac-8f32-53a5c0fc6eba)

- Ini adalah film dengan kualitas terbaik berdasarkan rating rata-rata, dengan syarat jumlah rating-nya ≥ 100.

- Jadi, film seperti movieId = 318 tidak hanya populer (317 rating), tapi juga sangat disukai (rating rata-rata 4.42).

### movies.csv

*Lihat Beberapa Data Teratas*

![image](https://github.com/user-attachments/assets/44c69ada-93dc-42cb-aa38-9613a04652ba)

Melihat 5 data teratas dari dataset movies.csv

*Cek Struktur Data*

![image](https://github.com/user-attachments/assets/1cf32b1c-3863-45d1-b4a0-20ea659aac2c)

- Dataset terdiri dari 9742 baris dan 3 kolom.
- Semua kolom tidak ada yang kosong (non-null).
- Kolom movieId bertipe integer, sedangkan title dan genres adalah string (object).
- Kolom:
1. **movieId:** ID unik film.

2. **title:** Judul film.

3. **genres:** Genre film.

*10 Genre Film Paling Sering Muncul*

![image](https://github.com/user-attachments/assets/725076b9-6c5c-40f3-aa30-395cb40bcb56)

- Genre Drama adalah yang paling umum, muncul dalam 4.361 film.

- Diikuti oleh Comedy, Thriller, dan Action.

- Ini dihitung dengan memecah isi kolom genres berdasarkan tanda |, lalu dijumlahkan total kemunculan genre.

*10 Tahun dengan Jumlah Film Terbanyak*

![image](https://github.com/user-attachments/assets/954b23c4-f273-4054-bbe3-68cba71ff9ba)

- Tahun diambil dari kolom title menggunakan regex \((\d{4})\) untuk menangkap angka tahun dalam tanda kurung.

- Jumlah film paling banyak di tahun 2009 (282 film).

- Setelah 2015 jumlah film mulai menurun — kemungkinan karena data film yang lebih baru belum lengkap di dataset ini.

## Data Preparation

### Gabung movies dan ratings

![image](https://github.com/user-attachments/assets/a6a9232e-c721-479b-8c6a-6c2523b9c4e6)

- Dataset baru movie_ratings sekarang mengandung semua kolom dari ratings dan juga title, genres, serta year dari movies.

- Ini membuat analisis lebih fleksibel: kita bisa filter berdasarkan genre, tahun, dll sekaligus melihat rating.

### Cek Missing Values dan Menanganinya

![image](https://github.com/user-attachments/assets/d781c3b8-1e93-4a56-bfd2-d55928dd7417)

- Hanya kolom year yang punya 18 missing values, karena tidak semua judul film memiliki tahun di title.

- Nanti bisa drop atau isi dengan strategi tertentu (misalnya dengan median atau kategori “unknown”).

![image](https://github.com/user-attachments/assets/b7f24dc9-a20b-4f67-9e34-e4ab8125c1e3)

Menghapus baris yang tidak memiliki tahun rilis karena bisa mengganggu analisis time series atau tren per tahun.

![image](https://github.com/user-attachments/assets/f2020edf-10db-4bf1-a845-dd216bea5af4)

Data sudah bersih dari Missing Value

### Ubah tahun jadi integer

![image](https://github.com/user-attachments/assets/54b355aa-7c32-4b5a-9377-77143a23547b)

Kode ini digunakan untuk mengubah tipe data kolom year menjadi integer (int).

![image](https://github.com/user-attachments/assets/cc87892b-e2a4-456c-bb67-268908a24278)

pada kolom year udah berubah data typenya menjadi integer dari float

### Buat kolom genre terpisah

![image](https://github.com/user-attachments/assets/34039752-c3ac-40ac-ab08-edcf9563dec7)

- Kolom genres sekarang adalah list, bukan string.

- Kolom-kolom tambahan di akhir (Action, Comedy, ..., Western) adalah hasil binarisasi.

- Berisi angka 0 dan 1 yang menunjukkan apakah film tersebut mengandung genre tersebut.

Contoh:

- Toy Story (1995) memiliki nilai 1 di kolom Adventure, Animation, Children, dll.

- Heat (1995) memiliki 1 di Action, Crime, dan Thriller.

## Modeling

### Content-Based Filtering dengan pendekatan User Profile Genre

Cara Kerja:

“Jika user menyukai film bergenre Action dan Sci-Fi, maka film lain dengan genre serupa akan lebih disukai.”

✅ Kelebihan:
- Tidak tergantung user lain: Hanya butuh data user itu sendiri → cocok untuk sistem individual.

- Bekerja baik untuk cold-start user: Karena memanfaatkan metadata (genre), model masih bisa memberikan rekomendasi walau user belum banyak memberi rating.

- Rekomendasi dapat dijelaskan: Misalnya: “Film ini direkomendasikan karena memiliki genre Action dan Sci-Fi yang Anda sukai.”

- Lebih stabil terhadap perubahan data: Tidak terlalu terpengaruh oleh fluktuasi data rating user lain.

❌ Kekurangan:
- Rekomendasi cenderung sempit: User hanya mendapat rekomendasi yang mirip dengan yang sudah ditonton → kurang bisa eksplorasi genre/film baru.

- Kualitas tergantung metadata: Jika informasi genre tidak lengkap atau tidak relevan, kualitas rekomendasi bisa buruk.

- Tidak menangkap selera kompleks: Tidak mempertimbangkan bagaimana user lain menilai film serupa.

![image](https://github.com/user-attachments/assets/bd2203a6-41a5-4792-b97e-273ef3609846)

- Output user_profiles adalah representasi minat setiap pengguna terhadap genre film dalam bentuk vektor.

- Nilai pada tabel adalah rata-rata kehadiran genre dalam film yang ditonton user.

- Semakin tinggi nilainya (mendekati 1), berarti user lebih sering menonton film dengan genre tersebut.

![image](https://github.com/user-attachments/assets/830aef92-7dbf-41fd-8adc-32307b964423)

- Ini adalah implementasi content-based filtering menggunakan genre sebagai fitur konten film.

- Rekomendasi diberikan berdasarkan kemiripan antara film dan preferensi user, bukan karena film itu populer atau disukai user lain (bukan collaborative).

### Content-Based Filtering (Skor Kemiripan → Top-N Rekomendasi)

![image](https://github.com/user-attachments/assets/02a2c410-ea69-4947-85a2-336ada2a37a8)

✅ Top-10 film yang belum ditonton oleh User ID 1.

✅ Prediksi ratingnya 5.0 untuk semua film (tinggi banget!).

✅ Artinya, model sangat yakin bahwa User ID 1 akan sangat menyukai film-film ini.


### Collaborative Filtering (Matrix Factorization – SVD)

Cara Kerja:

“Jika user A dan user B memiliki rating mirip untuk banyak film, maka film yang disukai B dan belum ditonton A bisa direkomendasikan ke A.”

✅ Kelebihan:
- Personalized: Rekomendasi lebih akurat karena memperhitungkan pola interaksi antar pengguna dan item.

- Tidak membutuhkan metadata film: Cukup menggunakan data userId, movieId, dan rating saja.

- Menemukan hubungan tersembunyi: Dapat mengenali preferensi user meskipun genre atau konten film tidak diketahui secara eksplisit.

- Skalabilitas tinggi dengan matrix factorization (SVD): Efisien dalam menangani data besar dengan teknik pemfaktoran.

❌ Kekurangan:
- Cold Start Problem:

- User baru: Tidak ada cukup data riwayat untuk merekomendasikan film.

- Item baru: Film yang belum pernah diberi rating tidak bisa direkomendasikan.

- Sparsity (Kerapatan rendah): Matrix user-item bisa sangat besar tapi banyak yang kosong → mempengaruhi akurasi model.

- Tidak menjelaskan alasan rekomendasi: Sulit dimengerti mengapa film tersebut direkomendasikan.

![image](https://github.com/user-attachments/assets/d3a262b7-e3ae-4149-8972-84e34c450eec)


Tujuan:

- *Menjamin kompatibilitas* antara NumPy dan library lain dalam proyek (misalnya, scikit-learn, pandas, atau surprise untuk collaborative filtering).
- Beberapa library mungkin *tidak kompatibel dengan versi NumPy terbaru, sehingga versi 1.24.4 dipilih untuk **menghindari error atau bug* saat menjalankan model rekomendasi atau matrix factorization.

![image](https://github.com/user-attachments/assets/66ef0819-37bb-489a-84c3-c6d168c9832f)

- Model SVD cukup stabil, dengan RMSE sekitar 0.87 dan MAE sekitar 0.67.

- Variasi hasil antar fold rendah (standar deviasi kecil), artinya model konsisten di berbagai subset data.

- Waktu pelatihan dan prediksi relatif cepat (beberapa detik dan kurang dari satu detik).

- Model sudah cukup baik untuk tugas prediksi rating film.

![image](https://github.com/user-attachments/assets/85de09a7-b43a-4ab8-80f7-7c51cfe5c8a2)

model memprediksi user 1 akan memberi rating 4.73 untuk movie 50 (dalam skala rating 0.5-5.0). Jadi, prediksi rating ini cukup tinggi dan menunjukkan bahwa user kemungkinan besar suka dengan film tersebut.

### Content-Based Filtering (Skor Kemiripan → Top-N Rekomendasi)

![image](https://github.com/user-attachments/assets/3b3324dd-1209-405a-a4ed-617cf1ca17bf)


✅ Output ini memberitahu kamu 10 film teratas yang paling mirip genre-nya dengan film favorit user.

✅ Film dengan skor tertinggi kemungkinan besar memiliki genre yang sama atau sangat mirip dengan film referensi.

✅ Ini berguna untuk merekomendasikan film serupa berdasarkan preferensi genre, tanpa melihat interaksi pengguna lain.

##  Evaluation

### Content-Based Filtering dengan pendekatan User Profile Genre

![image](https://github.com/user-attachments/assets/ba7746cd-b339-4734-a377-1435a25eda21)

✅ Model rekomendasi menyesuaikan genre film rekomendasi dengan genre yang sering ditonton user 1.

✅ Terlihat konsistensi: Top genre yang disukai user (Action, Adventure, Comedy, Drama, Thriller) juga muncul pada rekomendasi.

✅ Adventure muncul dominan (1.0), artinya model sangat yakin film ini cocok dengan preferensi user.

### Collaborative Filtering (Matrix Factorization – SVD)

![image](https://github.com/user-attachments/assets/c532f023-d847-4dbc-a73f-6104e59aa5be)

✅ Model SVD sudah berjalan baik (dengan RMSE ~0.87).

✅ Prediksi rating untuk film movieId=50 sangat tinggi (5.0), jadi ini kandidat rekomendasi yang kuat untuk user 1.

## recision@K, Recall@K, NDCG@K

![image](https://github.com/user-attachments/assets/7ce408e2-94c1-4066-9423-c1e8689e2bb9)


📌 Inti kode:

1️⃣ Hitung precision & recall pada 10 rekomendasi teratas.

2️⃣ Precision = berapa dari top-10 yang relevan.

3️⃣ Recall = proporsi film relevan yang berhasil direkomendasikan.

📌 Output 0.0 menunjukkan:

Artinya tidak ada film relevan (yang disukai user 1, rating ≥ 4) yang muncul di top-10 rekomendasi.


























