---
title: Pengolahan Data NO₂ Menggunakan Aiven PostgreSQL dan KNIME

---

# Analisis Data NO₂ Menggunakan Aiven PostgreSQL dan KNIME

## 1. Pendahuluan

Pada tugas ini dilakukan proses pengolahan dan analisis data **Nitrogen Dioxide (NO₂)** yang diperoleh dari data penginderaan jauh.

Data tersebut disimpan terlebih dahulu pada cloud database menggunakan **Aiven PostgreSQL**. Setelah itu, data dihubungkan ke **KNIME Analytics Platform** untuk dilakukan analisis statistik menggunakan node **Statistics**.

Tahapan pengolahan data yang dilakukan adalah:

1. Menyiapkan dataset CSV.
2. Menyimpan data CSV ke Aiven PostgreSQL.
3. Menghubungkan Aiven PostgreSQL dengan KNIME.
4. Mengambil data menggunakan DB Query Reader.
5. Melakukan analisis statistik menggunakan node Statistics.
6. Menjelaskan setiap properti statistik yang dihasilkan.
7. Menganalisis hasil statistik data NO₂.



# 2. Informasi Dataset

Dataset yang digunakan merupakan data **Tropospheric NO₂ Column**.

Informasi dataset:

| Informasi | Keterangan |
|---|---|
| Parameter | Nitrogen Dioxide (NO₂) |
| Lokasi | Biak, Papua, Indonesia |
| Satelit | Sentinel-5P / TROPOMI |
| Sumber | Copernicus Data Space Ecosystem |
| Satuan | mol/m² |
| Jumlah data | 135 baris |
| Jumlah kolom | 10 kolom |

Data yang digunakan memiliki beberapa atribut statistik NO₂ seperti minimum, maksimum, mean, standar deviasi, median, p10, dan p90.


# 3. Penyimpanan Data pada Aiven PostgreSQL

Setelah dataset CSV diperoleh, data dipindahkan ke cloud database menggunakan **Aiven PostgreSQL**.

Database yang digunakan adalah:

- Database: `defaultdb`
- Schema: `public`
- Table: `no2_data`


## 3.1 Informasi Koneksi Database

Database PostgreSQL pada Aiven digunakan sebagai tempat penyimpanan dataset sebelum data diproses menggunakan KNIME.

Informasi koneksi yang digunakan pada KNIME:

- Hostname PostgreSQL Aiven
- Port PostgreSQL
- Database `defaultdb`
- Username `avnadmin`
- Password database

> Password tidak ditampilkan dalam dokumentasi untuk menjaga keamanan koneksi database.


## 3.2 Struktur Tabel

Tabel yang digunakan untuk menyimpan data NO₂ adalah:

```sql
no2_data
```

Struktur tabel dibuat menggunakan SQL berikut:

```sql
CREATE TABLE no2_data (
    date TIMESTAMP,
    min_value DOUBLE PRECISION,
    max_value DOUBLE PRECISION,
    mean_value DOUBLE PRECISION,
    stdev DOUBLE PRECISION,
    sample_count INTEGER,
    nodata_count INTEGER,
    median DOUBLE PRECISION,
    p10 DOUBLE PRECISION,
    p90 DOUBLE PRECISION
);
```

Struktur tersebut menyesuaikan dengan struktur kolom pada file CSV.


## 3.3 Proses Penyimpanan Data

Setelah tabel `no2_data` dibuat, seluruh data dari CSV dimasukkan ke dalam tabel PostgreSQL.

Sebanyak **135 baris data berhasil diproses** oleh PostgreSQL.

### Gambar 1 — Informasi koneksi Aiven PostgreSQL

![psd2](psd2.png)


*Gambar 1. Informasi koneksi database PostgreSQL pada Aiven.*

### Gambar 2 — Proses penyimpanan data

![psd1](psd1.png)


*Gambar 2. Proses penyimpanan 135 baris data NO₂ ke dalam tabel `no2_data` pada Aiven PostgreSQL.*

Pada bagian **Results** terlihat keterangan:

```text
135 rows
```

Hal tersebut menunjukkan bahwa sebanyak **135 baris data berhasil diproses dan dimasukkan ke dalam database**.

Dengan demikian, data NO₂ telah berhasil disimpan pada Aiven PostgreSQL dan siap digunakan pada tahap berikutnya.


# 4. Koneksi PostgreSQL dengan KNIME

Setelah data berhasil disimpan pada Aiven PostgreSQL, tahap berikutnya adalah menghubungkan database PostgreSQL dengan **KNIME Analytics Platform**.

Pada tahap ini digunakan node **PostgreSQL Connector** sebagai penghubung antara KNIME dan database Aiven.

Konfigurasi koneksi menggunakan:

- Hostname PostgreSQL Aiven
- Port PostgreSQL
- Database `defaultdb`
- Username `avnadmin`
- Password database

Setelah seluruh konfigurasi dimasukkan, koneksi diuji dengan menjalankan node **PostgreSQL Connector**.

Apabila koneksi berhasil, node PostgreSQL Connector akan menunjukkan status berhasil dan dapat digunakan untuk proses selanjutnya.

### Gambar 3 — Workflow KNIME

![psd3](psd3.png)


*Gambar 3. Workflow pengolahan data yang menghubungkan PostgreSQL Connector, DB Query Reader, dan Statistics pada KNIME.*

Workflow tersebut menunjukkan hubungan antara PostgreSQL Connector, DB Query Reader, dan Statistics dalam proses pengolahan data.


# 5. Mengambil Data Menggunakan DB Query Reader

Setelah koneksi PostgreSQL berhasil dibuat, tahap berikutnya adalah mengambil data dari tabel `no2_data`.

Untuk mengambil data tersebut digunakan node **DB Query Reader**.

Node **DB Query Reader** dihubungkan dengan node **PostgreSQL Connector** sehingga koneksi database dapat digunakan untuk menjalankan query SQL.

Alur proses:

```text
PostgreSQL Connector
        ↓
DB Query Reader
```

---

## 5.1 Query SQL

Query SQL yang digunakan untuk mengambil seluruh data adalah:

```sql
SELECT *
FROM "public"."no2_data";
```

Query tersebut digunakan untuk mengambil seluruh kolom dan seluruh baris dari tabel `no2_data` yang berada pada schema `public`.

Setelah query dijalankan, data berhasil dibaca oleh KNIME.

Hasil pembacaan menunjukkan bahwa terdapat:

- **135 baris data**
- **10 kolom**

Kolom yang berhasil dibaca adalah:

| No. | Nama Kolom |
|---:|---|
| 1 | `date` |
| 2 | `min_value` |
| 3 | `max_value` |
| 4 | `mean_value` |
| 5 | `stdev` |
| 6 | `sample_count` |
| 7 | `nodata_count` |
| 8 | `median` |
| 9 | `p10` |
| 10 | `p90` |

### Gambar 4 — Hasil DB Query Reader

![psd4](psd4.png)


*Gambar 4. Hasil pembacaan 135 baris dan 10 kolom data dari tabel `no2_data` menggunakan DB Query Reader pada KNIME.*

Pada hasil DB Query Reader terlihat:

- **Rows = 135**
- **Columns = 10**

Hal tersebut menunjukkan bahwa data yang tersimpan pada PostgreSQL berhasil ditarik ke dalam KNIME.

Data tersebut selanjutnya digunakan sebagai input untuk proses analisis statistik menggunakan node **Statistics**.



# 6. Analisis Statistik Menggunakan KNIME

Setelah data berhasil dibaca menggunakan **DB Query Reader**, data diteruskan ke node **Statistics** untuk melakukan analisis statistik.

Alur workflow yang digunakan adalah:

```text
PostgreSQL Connector
        ↓
DB Query Reader
        ↓
Statistics
```

Node **Statistics** digunakan untuk menghasilkan berbagai informasi statistik dari data numerik.

Properti statistik yang dibahas dalam tugas ini meliputi:

1. Column
2. Min
3. Mean
4. Median
5. Max
6. Std. Dev.
7. Variance
8. Skewness
9. Kurtosis
10. Overall Sum
11. Mean Absolute
12. No. Missing
13. No. +Infinity / No. +unlimited
14. No. -Infinity / No. -unlimited
15. Unique Values
16. Quantile


## 6.1 Hasil Analisis Menggunakan Node Statistics

Setelah node **Statistics** berhasil dieksekusi, KNIME menampilkan hasil analisis statistik dalam bentuk tabel.

### Gambar 5 — Hasil Statistics


![psd5](psd5.png)


*Gambar 5. Hasil analisis statistik data NO₂ menggunakan node Statistics pada KNIME.*

Berdasarkan hasil Statistics, KNIME menghasilkan analisis untuk beberapa kolom numerik, yaitu:

- `min_value`
- `max_value`
- `mean_value`
- `stdev`
- `sample_count`
- `nodata_count`
- `median`
- `p10`
- `p90`

Kolom `date` tidak digunakan sebagai statistik numerik karena merupakan data bertipe tanggal.


# 7. Penjelasan Setiap Properti Statistik

## 7.1 Column

**Column** menunjukkan nama kolom atau atribut yang sedang dianalisis oleh KNIME.

Pada dataset NO₂ terdapat beberapa kolom numerik, seperti:

- `min_value`
- `max_value`
- `mean_value`
- `stdev`
- `sample_count`
- `nodata_count`
- `median`
- `p10`
- `p90`

### Contoh

Misalkan terdapat tabel:

| Nama | Umur | Nilai |
|---|---:|---:|
| A | 20 | 80 |
| B | 21 | 90 |
| C | 20 | 85 |

Nama kolomnya adalah:

```text
Nama
Umur
Nilai
```

Jika kolom `Nilai` dianalisis, maka:

```text
Column = Nilai
```

Column tidak dihitung menggunakan rumus karena fungsinya adalah menunjukkan kolom yang sedang dianalisis.


# 7.2 Min

**Min** atau minimum merupakan nilai terkecil yang terdapat dalam suatu kolom.

### Cara Menghitung

Nilai minimum diperoleh dengan mencari nilai yang paling kecil dari seluruh data.

### Contoh

```text
Data = 2, 4, 6, 8, 10
```

Maka:

```text
Min = 2
```

### Pada Data NO₂

```text
Min = -1.72825 × 10⁻⁵ mol/m²
```

Nilai tersebut merupakan nilai NO₂ paling rendah dalam dataset.


# 7.3 Mean

**Mean** atau rata-rata digunakan untuk mengetahui nilai rata-rata dari seluruh data.

### Rumus

```text
Mean = Σx / n
```

Keterangan:

- Σx = jumlah seluruh nilai
- n = jumlah data

### Contoh

```text
Data = 2, 4, 6

Mean = (2 + 4 + 6) / 3
     = 12 / 3
     = 4
```

Jadi:

```text
Mean = 4
```

### Pada Data NO₂

```text
Mean = 3.03446 × 10⁻⁶ mol/m²
```

---

# 7.4 Median

**Median** merupakan nilai tengah setelah data diurutkan.

### Contoh Data Ganjil

```text
Data = 2, 4, 6, 8, 10

Median = 6
```

### Contoh Data Genap

```text
Data = 2, 4, 6, 8

Median = (4 + 6) / 2
       = 5
```

### Pada Data NO₂

```text
Median = 3.18589 × 10⁻⁶ mol/m²
```

Median digunakan untuk mengetahui nilai tengah data.


# 7.5 Max

**Max** atau maximum merupakan nilai terbesar yang terdapat dalam suatu kolom.

### Contoh

```text
Data = 2, 4, 6, 8, 10

Max = 10
```

### Pada Data NO₂

```text
Max = 1.94392 × 10⁻⁵ mol/m²
```

Nilai tersebut merupakan nilai NO₂ terbesar dalam dataset.


# 7.6 Std. Dev.

**Std. Dev.** atau Standard Deviation merupakan ukuran yang menunjukkan seberapa jauh data menyebar dari nilai rata-ratanya.

### Rumus

```text
s = √[Σ(x - x̄)² / (n - 1)]
```

### Contoh

Misalkan:

```text
Data = 2, 4, 6
```

Mean:

```text
Mean = (2 + 4 + 6) / 3
     = 4
```

Kemudian:

```text
s = √[((2-4)² + (4-4)² + (6-4)²) / (3-1)]

  = √[(4 + 0 + 4) / 2]

  = √4

  = 2
```

Jadi:

```text
Std. Dev. = 2
```

### Pada Data NO₂

```text
Std. Dev. = 6.64879 × 10⁻⁶
```


# 7.7 Variance

**Variance** merupakan ukuran penyebaran data.

Variance berhubungan dengan standar deviasi.

### Rumus

```text
Variance = (Std. Dev.)²
```

### Contoh

Jika:

```text
Std. Dev. = 2
```

Maka:

```text
Variance = 2²
         = 4
```

Jadi:

```text
Variance = 4
```

### Pada Data NO₂

```text
Variance = 4.42065 × 10⁻¹¹
```


# 7.8 Skewness

**Skewness** digunakan untuk mengetahui kemencengan distribusi data.

| Nilai Skewness | Interpretasi |
|---|---|
| Mendekati 0 | Relatif simetris |
| Positif | Cenderung menceng ke kanan |
| Negatif | Cenderung menceng ke kiri |

### Contoh

Misalkan:

```text
Data = 2, 4, 6
```

Data tersebut relatif simetris terhadap nilai tengah 4 sehingga:

```text
Skewness ≈ 0
```

### Pada Data NO₂

```text
Skewness = -0.01137
```

Nilai tersebut sangat dekat dengan 0 sehingga distribusi data NO₂ relatif simetris.


# 7.9 Kurtosis

**Kurtosis** digunakan untuk menggambarkan bentuk distribusi data, terutama tingkat keruncingan atau kerataan distribusi.

Pada hasil Statistics digunakan konsep **excess kurtosis**.

| Excess Kurtosis | Interpretasi |
|---|---|
| Mendekati 0 | Mendekati distribusi normal |
| Positif | Cenderung lebih runcing |
| Negatif | Cenderung lebih datar |

### Contoh

Misalkan:

```text
Data = 2, 4, 6, 8, 10
```

Untuk contoh tersebut, excess kurtosis bernilai sekitar:

```text
Kurtosis = -1.2
```

Nilai negatif menunjukkan distribusi lebih datar dibandingkan distribusi normal.

### Pada Data NO₂

```text
Kurtosis = -0.06953
```

Nilai tersebut sedikit negatif sehingga distribusi data sedikit lebih datar berdasarkan excess kurtosis.


# 7.10 Overall Sum

**Overall Sum** merupakan jumlah seluruh nilai dalam suatu kolom.

### Rumus

```text
Sum = x₁ + x₂ + x₃ + ... + xₙ
```

### Contoh

```text
Data = 2, 4, 6

Sum = 2 + 4 + 6
    = 12
```

Jadi:

```text
Overall Sum = 12
```

### Pada Data NO₂

```text
Overall Sum = 4.09652 × 10⁻⁴ mol/m²
```


# 7.11 Mean Absolute

**Mean Absolute** merupakan rata-rata nilai absolut dari seluruh data.

### Rumus

```text
Mean Absolute = Σ|x| / n
```

### Contoh

Misalkan:

```text
Data = -2, 4, -6
```

Nilai absolut:

```text
|-2| = 2
|4|  = 4
|-6| = 6
```

Kemudian:

```text
Mean Absolute = (2 + 4 + 6) / 3
              = 12 / 3
              = 4
```

Jadi:

```text
Mean Absolute = 4
```

### Pada Data NO₂

```text
Mean Absolute = 5.85066 × 10⁻⁶
```


# 7.12 No. Missing

**No. Missing** menunjukkan jumlah data yang kosong atau tidak tersedia.

### Contoh

Misalkan:

```text
Data = 2, 4, kosong, 8, 10
```

Terdapat satu nilai kosong.

Maka:

```text
No. Missing = 1
```

### Pada Dataset NO₂

```text
No. Missing = 0
```

Artinya tidak terdapat nilai yang hilang.


# 7.13 No. +Infinity / No. +unlimited

**No. +Infinity** atau **No. +unlimited** menunjukkan jumlah nilai positif tak hingga (`+Infinity`).

### Contoh

Misalkan:

```text
Data = 2, 4, +Infinity, 8
```

Maka:

```text
No. +Infinity = 1
```

Jika tidak terdapat nilai positif tak hingga:

```text
No. +Infinity = 0
```

### Pada Dataset NO₂

```text
No. +Infinity = 0
```

Artinya tidak terdapat nilai positif tak hingga.


# 7.14 No. -Infinity / No. -unlimited

**No. -Infinity** atau **No. -unlimited** menunjukkan jumlah nilai negatif tak hingga (`-Infinity`).

### Contoh

Misalkan:

```text
Data = 2, -Infinity, 6, 8
```

Maka:

```text
No. -Infinity = 1
```

Jika tidak terdapat nilai negatif tak hingga:

```text
No. -Infinity = 0
```

### Pada Dataset NO₂

```text
No. -Infinity = 0
```

Artinya tidak terdapat nilai negatif tak hingga.


# 7.15 Unique Values

**Unique Values** menunjukkan jumlah nilai yang berbeda dalam suatu kolom.

### Contoh

Misalkan:

```text
Data = 1, 1, 2, 2, 3
```

Nilai unik:

```text
1, 2, 3
```

Maka:

```text
Unique Values = 3
```

### Pada Dataset NO₂

Pada kolom `sample_count`, seluruh data memiliki nilai 1.

Sehingga:

```text
Unique Values = 1
```

Pada kolom `nodata_count`, seluruh data memiliki nilai 0.

Sehingga:

```text
Unique Values = 1
```


# 7.16 Quantile

**Quantile** digunakan untuk membagi data berdasarkan posisi tertentu setelah data diurutkan.

Pada analisis ini digunakan:

- Q25
- Q50
- Q75

### Contoh

Misalkan:

```text
Data = 2, 4, 6, 8, 10
```

Untuk contoh sederhana:

```text
Q25 = 4
Q50 = 6
Q75 = 8
```

Q50 sama dengan median.

### Pada Data NO₂

| Quantile | Nilai |
|---|---:|
| Q25 | -1.83471 × 10⁻⁶ |
| Q50 | 3.18589 × 10⁻⁶ |
| Q75 | 7.52493 × 10⁻⁶ |


# 8. Hasil Statistik Data NO₂

Berdasarkan hasil pengolahan data, statistik utama untuk nilai NO₂ adalah:

| Statistik | Nilai |
|---|---:|
| Jumlah Data | 135 |
| Minimum | -1.72825 × 10⁻⁵ |
| Maximum | 1.94392 × 10⁻⁵ |
| Mean | 3.03446 × 10⁻⁶ |
| Median | 3.18589 × 10⁻⁶ |
| Std. Deviation | 6.64879 × 10⁻⁶ |
| Variance | 4.42065 × 10⁻¹¹ |
| Skewness | -0.01137 |
| Kurtosis | -0.06953 |
| Overall Sum | 4.09652 × 10⁻⁴ |
| Mean Absolute | 5.85066 × 10⁻⁶ |
| No. Missing | 0 |
| No. +Infinity | 0 |
| No. -Infinity | 0 |


# 9. Hasil Statistik Kolom Pendukung

Selain kolom nilai NO₂, terdapat beberapa kolom pendukung yang ikut dianalisis menggunakan KNIME.

## 9.1 Kolom `stdev`

Kolom `stdev` memiliki nilai **0 pada seluruh 135 baris**.

| Statistik | Nilai |
|---|---:|
| Min | 0 |
| Max | 0 |
| Mean | 0 |
| Median | 0 |
| Std. Deviation | 0 |
| Variance | 0 |
| Overall Sum | 0 |
| No. Missing | 0 |

Hal ini menunjukkan bahwa setiap baris data memiliki nilai `stdev` sebesar 0.


## 9.2 Kolom `sample_count`

Kolom `sample_count` memiliki nilai **1 pada seluruh 135 baris**.

| Statistik | Nilai |
|---|---:|
| Min | 1 |
| Max | 1 |
| Mean | 1 |
| Median | 1 |
| Std. Deviation | 0 |
| Variance | 0 |
| Overall Sum | 135 |
| Unique Values | 1 |
| No. Missing | 0 |

Artinya setiap baris data memiliki satu sampel.


## 9.3 Kolom `nodata_count`

Kolom `nodata_count` memiliki nilai **0 pada seluruh 135 baris**.

| Statistik | Nilai |
|---|---:|
| Min | 0 |
| Max | 0 |
| Mean | 0 |
| Median | 0 |
| Std. Deviation | 0 |
| Variance | 0 |
| Overall Sum | 0 |
| Unique Values | 1 |
| No. Missing | 0 |

Artinya tidak terdapat data yang ditandai sebagai **NoData**.

# 10. Analisis Hasil Statistik

Berdasarkan hasil statistik, nilai rata-rata konsentrasi NO₂ adalah sekitar **3.03446 × 10⁻⁶ mol/m²**, sedangkan nilai median sebesar **3.18589 × 10⁻⁶ mol/m²**.

Nilai mean dan median yang cukup berdekatan menunjukkan bahwa pusat distribusi data relatif tidak jauh berbeda.

Nilai minimum sebesar **-1.72825 × 10⁻⁵ mol/m²** dan nilai maksimum sebesar **1.94392 × 10⁻⁵ mol/m²** menunjukkan adanya variasi nilai NO₂ selama periode pengamatan.

Standar deviasi sebesar **6.64879 × 10⁻⁶** menunjukkan bahwa nilai NO₂ memiliki penyebaran terhadap nilai rata-ratanya.

Nilai skewness sebesar **-0.01137** sangat dekat dengan nol. Hal ini menunjukkan bahwa distribusi data NO₂ relatif simetris dan tidak memiliki kemencengan yang kuat.

Nilai kurtosis sebesar **-0.06953** menunjukkan bahwa distribusi data sedikit lebih datar dibandingkan distribusi normal berdasarkan excess kurtosis.

Selain itu, jumlah data yang hilang adalah **0** dan tidak ditemukan nilai `+Infinity` maupun `-Infinity`.

# 11. Catatan Mengenai Nilai 0 pada KNIME

Pada tampilan KNIME, beberapa nilai NO₂ yang sangat kecil dapat terlihat sebagai:

```text
0
```

atau:

```text
-0
```

Hal tersebut terjadi karena angka NO₂ memiliki nilai yang sangat kecil sehingga tampilan tabel KNIME dapat melakukan pembulatan.

Sebagai contoh:

```text
3.03446 × 10⁻⁶
```

merupakan angka yang sangat kecil sehingga dapat terlihat sebagai `0` apabila jumlah angka desimal yang ditampilkan terbatas.

Sedangkan nilai:

```text
-1.72825 × 10⁻⁵
```

dapat terlihat sebagai `-0`.

Oleh karena itu, angka `0` atau `-0` pada tampilan KNIME tidak selalu berarti nilai asli data benar-benar nol.

Dalam laporan ini digunakan notasi ilmiah agar nilai statistik dapat ditampilkan dengan lebih jelas.


# 12. Penjelasan Nilai Negatif pada Data NO₂

Pada dataset terdapat beberapa nilai NO₂ yang bernilai negatif.

Contohnya nilai minimum:

```text
-1.72825 × 10⁻⁵ mol/m²
```

Nilai negatif pada data satelit tidak secara langsung berarti terdapat konsentrasi NO₂ negatif secara fisik.

Nilai tersebut dapat muncul sebagai bagian dari hasil pengukuran dan pemrosesan data penginderaan jauh.

Oleh karena itu, nilai negatif perlu dipahami sebagai bagian dari hasil pengukuran atau pemrosesan dataset dan tidak langsung dianggap sebagai konsentrasi fisik negatif.


# 13. Perbandingan Mean dan Median

Mean dan median merupakan dua ukuran yang digunakan untuk melihat pusat data.

| Ukuran | Nilai |
|---|---:|
| Mean | 3.03446 × 10⁻⁶ |
| Median | 3.18589 × 10⁻⁶ |

Nilai mean dan median relatif berdekatan.

Hal tersebut sesuai dengan nilai skewness yang juga sangat dekat dengan nol, yaitu **-0.01137**.

Dengan demikian, distribusi data NO₂ dapat dikatakan relatif simetris.

# 14. Analisis Penyebaran Data

Penyebaran data dapat dianalisis menggunakan standar deviasi dan variance.

Hasil yang diperoleh:

```text
Standard Deviation = 6.64879 × 10⁻⁶
Variance = 4.42065 × 10⁻¹¹
```

Standar deviasi menunjukkan adanya variasi nilai pengamatan terhadap nilai rata-rata.

Hal ini menunjukkan bahwa data NO₂ tidak seluruhnya berada pada nilai yang sama, tetapi memiliki penyebaran selama periode pengamatan.

# 15. Analisis Distribusi Data

Distribusi data dapat dianalisis menggunakan skewness dan kurtosis.

| Ukuran | Nilai | Interpretasi |
|---|---:|---|
| Skewness | -0.01137 | Relatif simetris |
| Kurtosis | -0.06953 | Sedikit lebih datar |

Skewness yang mendekati 0 menunjukkan bahwa distribusi data tidak memiliki kemencengan yang kuat.

Kurtosis yang sedikit negatif menunjukkan distribusi yang sedikit lebih datar berdasarkan excess kurtosis.


# 16. Analisis Data Kosong dan Nilai Tak Hingga

Hasil Statistics juga digunakan untuk memeriksa kualitas data.

| Pemeriksaan | Hasil |
|---|---:|
| Missing Values | 0 |
| Positive Infinity / +unlimited | 0 |
| Negative Infinity / -unlimited | 0 |

Tidak adanya missing value menunjukkan bahwa data yang dianalisis tidak memiliki nilai kosong.

Selain itu, tidak terdapat nilai positif maupun negatif tak hingga.

Dengan demikian, dataset tidak memiliki missing value maupun nilai infinity yang perlu ditangani pada proses analisis statistik.


# 17. Ringkasan Hasil Analisis

Secara keseluruhan, hasil analisis statistik menunjukkan:

1. Dataset memiliki **135 baris data**.
2. Dataset memiliki **10 kolom**.
3. Rata-rata nilai NO₂ adalah sekitar **3.03446 × 10⁻⁶ mol/m²**.
4. Median nilai NO₂ adalah sekitar **3.18589 × 10⁻⁶ mol/m²**.
5. Nilai minimum sekitar **-1.72825 × 10⁻⁵ mol/m²**.
6. Nilai maksimum sekitar **1.94392 × 10⁻⁵ mol/m²**.
7. Standar deviasi sekitar **6.64879 × 10⁻⁶**.
8. Variance sekitar **4.42065 × 10⁻¹¹**.
9. Skewness sebesar **-0.01137**, sehingga distribusi relatif simetris.
10. Kurtosis sebesar **-0.06953**, menunjukkan distribusi sedikit lebih datar.
11. Tidak terdapat missing value.
12. Tidak terdapat nilai `+Infinity` atau `+unlimited`.
13. Tidak terdapat nilai `-Infinity` atau `-unlimited`.
14. `sample_count` bernilai 1 untuk seluruh data.
15. `nodata_count` bernilai 0 untuk seluruh data.
16. Mean Absolute nilai NO₂ sekitar **5.85066 × 10⁻⁶**.
17. Q25 sebesar **-1.83471 × 10⁻⁶**.
18. Q50 sebesar **3.18589 × 10⁻⁶**.
19. Q75 sebesar **7.52493 × 10⁻⁶**.


# 18. Kesimpulan

Berdasarkan seluruh proses yang telah dilakukan, data NO₂ dari Biak, Papua berhasil diproses melalui beberapa tahapan, yaitu penyimpanan data CSV ke Aiven PostgreSQL, koneksi database dengan KNIME, pengambilan data menggunakan DB Query Reader, dan analisis statistik menggunakan node Statistics.

Sebanyak **135 baris data** berhasil disimpan pada Aiven PostgreSQL dan kemudian berhasil dibaca oleh KNIME. Data tersebut terdiri dari **10 kolom** yang berisi informasi tanggal serta beberapa nilai statistik NO₂.

Analisis menggunakan node Statistics menghasilkan berbagai ukuran statistik, seperti minimum, maksimum, mean, median, standar deviasi, variance, skewness, kurtosis, overall sum, mean absolute, missing values, infinity, unique values, dan quantile.

Rata-rata nilai NO₂ yang diperoleh adalah sekitar **3.03446 × 10⁻⁶ mol/m²**, sedangkan median sebesar **3.18589 × 10⁻⁶ mol/m²**.

Nilai mean dan median yang relatif berdekatan serta skewness sebesar **-0.01137** menunjukkan bahwa distribusi data relatif simetris.

Data memiliki variasi yang ditunjukkan oleh standar deviasi sebesar **6.64879 × 10⁻⁶** dan variance sebesar **4.42065 × 10⁻¹¹**.

Selain itu, tidak ditemukan missing value maupun nilai `+Infinity` dan `-Infinity`. Nilai `sample_count` juga bernilai 1 pada seluruh data, sedangkan `nodata_count` bernilai 0.

Dengan demikian, penggunaan **Aiven PostgreSQL dan KNIME Analytics Platform** berhasil digunakan untuk melakukan proses pengolahan data mulai dari penyimpanan data pada cloud, pengambilan data dari database, hingga analisis statistik secara terstruktur.

