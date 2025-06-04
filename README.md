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

1) Bagaimana merekomendasikan film yang relevan bagi pengguna?

2) Bagaimana memanfaatkan data interaksi pengguna dan metadata film untuk meningkatkan relevansi rekomendasi?

**Goals:**

1) Membangun sistem rekomendasi film menggunakan collaborative filtering dan content-based filtering.

2) Membandingkan performa kedua pendekatan untuk melihat mana yang lebih baik.


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
- Kolom:
1. **userId:** ID unik pengguna.

2. **movieId:** ID unik film.

3. **rating:** Nilai rating yang diberikan (0.5 – 5.0).

4. **timestamp:** Waktu saat rating diberikan (dalam format UNIX time).



