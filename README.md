# Analisis Performa Bisnis Kimia Farma (2020–2023)

Proyek ini adalah bagian dari Final Task untuk peran Big Data Analytics Intern. Fokusnya mengevaluasi kinerja bisnis Kimia Farma pada periode 2020–2023 dengan alur end-to-end: data mentah → pengolahan di BigQuery → tabel analisa → dashboard interaktif di Google Looker Studio.

## Gambaran Umum Proyek

Tujuan utama: menyatukan data transaksi, cabang, dan produk ke satu sumber tepercaya, lalu menyajikan metrik inti seperti penjualan bersih (nett\_sales), laba (nett\_profit), jumlah transaksi, jumlah pelanggan, dan rating agar pengambilan keputusan bisa cepat dan berbasis data.

## Alur Pengerjaan

1. **Impor data ke BigQuery**
   Empat berkas CSV dimuat ke dataset kimia\_farma:

   * kf\_final\_transaction.csv
   * kf\_product.csv
   * kf\_kantor\_cabang.csv
   * kf\_inventory.csv (sebagai konteks stok)
     Pengaturan impor: Auto-detect schema.

2. **Membangun tabel analisa (kf\_analysis)**
   Tabel dibentuk dengan SQL BigQuery (lihat file sql/kf\_analysis.sql) melalui LEFT JOIN transaksi ↔ cabang ↔ produk.
   Perhitungan utama:

   * disc\_rate = discount\_pct/100
   * persentase\_gross\_laba = 10–30% (tier berdasarkan actual\_price)
   * nett\_sales = actual\_price × (1 − disc\_rate)
   * nett\_profit = nett\_sales × persentase\_gross\_laba

3. **Visualisasi di Looker Studio**
   Sumber data: kimia\_farma.kf\_analysis.
   Komponen dashboard: KPI, filter global (Tahun, Provinsi, Kategori Cabang, Produk), tren YoY, Top 10 provinsi (transaksi dan penjualan bersih), peta profit, tabel transaksi, dan tabel gap rating cabang.

## Dataset yang Digunakan

* kf\_final\_transaction: detail transaksi (tanggal, harga, diskon, pelanggan, rating transaksi).
* kf\_product: identitas produk (id, nama atau kategori).
* kf\_kantor\_cabang: informasi cabang (id, nama, kota, provinsi, rating cabang).
* kf\_inventory: data stok per cabang (opsional untuk analisis lanjutan).

## Hasil dan Temuan dari Analisis

* **Kinerja keseluruhan**: Pada ringkasan menampilkan total penjualan bersih, laba, jumlah transaksi, jumlah pelanggan unik, dan rating rata-rata.
* **Tren tahunan**: pola pendapatan menunjukkan puncak pada 2022, penurunan pada 2021, dan koreksi di 2023.
* **Volume vs nilai**: tidak semua provinsi dengan transaksi tertinggi menghasilkan nilai penjualan tertinggi; ini menandakan variasi nilai belanja per transaksi atau mix produk.
* **Performa wilayah**: provinsi besar seperti Jawa Barat konsisten muncul sebagai kontributor utama pada transaksi dan penjualan bersih, serta menyumbang porsi laba yang besar pada peta profit.
* **Anomali layanan**: terdapat cabang dengan rating cabang bernilai 5 namun rating transaksi lebih rendah (sekitar 3). Daftar ini menjadi prioritas perbaikan proses seperti antrian, kecepatan layanan, dan ketersediaan item.
