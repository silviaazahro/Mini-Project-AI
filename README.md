# Mini Project: Books Dataset via Google Books API
1. Sumber Data
API yang digunakan: Google Books API
Alasan memilih:
Gratis dan API key mudah didapat lewat Google Cloud Console
Dokumentasi resmi jelas dan lengkap
Data yang tersedia kaya: judul, penulis, penerbit, tanggal terbit, rating, kategori, bahasa
Tidak ada proses approval manual yang lama — key langsung aktif setelah dibuat
Cara mendapatkan API key:
Buka console.cloud.google.com
Buat project baru
Masuk ke APIs & Services → Library, cari Books API, klik Enable
Masuk ke APIs & Services → Credentials → Create Credentials → API Key
(Opsional) Restrict key supaya hanya bisa dipakai untuk Books API
Key disimpan di file `.env`, tidak ditulis langsung di dalam kode
```
GOOGLE_BOOKS_API_KEY=isi_key_kamu_disini
```
---
2. Jumlah Data
Dataset akhir berisi ≥ 100 baris setelah dibersihkan
Satu kali panggilan API dibatasi maksimal ±20 hasil, sehingga API dipanggil berulang dengan beberapa kata kunci berbeda (`fiction`, `history`, `science`, `biography`, `technology`, `fantasy`, `romance`, `mystery`), lalu hasilnya digabung menjadi satu dataset
---
3. Struktur Kode (OOP)
Class `GoogleBooksAPI`
Atribut:
Atribut	Keterangan
`api_key`	Menyimpan API key, diambil dari file `.env`
`base_url`	Menyimpan alamat endpoint Google Books API
Method:
Method	Keterangan
`get_books(query, max_results, start_index)`	Memanggil API berdasarkan kata kunci pencarian, mengembalikan data buku dalam bentuk `list of dict`. Bagian pemanggilan API dibungkus `try-except` supaya program tidak berhenti total jika koneksi bermasalah

---
4. Keputusan Pembersihan Data (Data Cleaning)
Kolom	Keputusan	Alasan
`title`	Baris dibuang jika kosong	Judul adalah identitas utama buku; tanpa itu data tidak berguna
`authors`	Diisi "Tidak diketahui"	Penulis adalah identitas unik per buku; tidak valid ditebak pakai modus
`publisher`	Diisi "Tidak diketahui"	Sama seperti authors, sangat spesifik per buku
`categories`	Diisi "Tidak diketahui"	Kategori spesifik per buku, tidak bisa digeneralisasi dari buku lain
`language`	Diisi modus (nilai tersering)	Ada pola dominan bahasa dalam dataset, estimasi ini masuk akal
`page_count`	Diisi rata-rata (mean)	Kolom angka; mean adalah estimasi statistik yang wajar
`average_rating`	Diisi rata-rata (mean)	Kolom angka; sama seperti page_count
`published_date`	Diisi median	Median lebih tahan outlier dibanding mean untuk data tanggal
Data duplikat	Dibuang (kombinasi `title` + `authors` sama)	Buku yang sama bisa muncul dua kali karena kata kunci pencarian berbeda
Tipe data	`page_count` → int, `average_rating` → float, `published_date` → datetime	Supaya kolom bisa diproses sebagai angka/tanggal, bukan teks
---
5. Cara Menjalankan
```powershell
python -m venv venv
.\venv\Scripts\activate
pip install requests python-dotenv pandas
python main.py
```
Pastikan file `.env` berisi `GOOGLE_BOOKS_API_KEY` sudah dibuat, dan file `.gitignore` berisi `.env` agar API key tidak ikut ter-push ke GitHub.
