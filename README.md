# 📊 Data Quality Validation & Cleaning Report

## Dataset: *Indonesia Reading Interest 2020–2023*

### 🔍 Latar Belakang

Dataset ini berisi informasi minat baca masyarakat Indonesia dari tahun 2020 hingga 2023. Variabel yang tersedia mencakup frekuensi membaca, jumlah bacaan per kuartal, durasi membaca harian, serta akses internet.

Untuk memastikan kualitas data, dilakukan proses **validasi data quality** menggunakan framework **[Great Expectations](https://greatexpectations.io/)**, diikuti dengan tahap **data cleaning** agar dataset siap digunakan untuk analisis lebih lanjut.

---

## ⚙️ Data Quality Validation dengan Great Expectations

Expectation Suite yang dibuat: **`indonesia_reading_interest_suite`**

### ✅ Daftar Aturan (Expectations)

1. **Year** harus dalam rentang **2020–2023**

   ```python
   gxe.ExpectColumnValuesToBeBetween(column="Year", min_value=2020, max_value=2023)
   ```

2. **Reading Frequency (RF)** hanya boleh **0–7 kali per minggu**

   ```python
   gxe.ExpectColumnValuesToBeBetween(column="RF", min_value=0, max_value=7)
   ```

3. **Number of Readings (NR)** tidak boleh `NULL`

   ```python
   gxe.ExpectColumnValuesToNotBeNull(column="NR")
   ```

4. **Daily Reading Duration (DRD)** harus **positif** dan maksimal **24 jam (1440 menit)**

   ```python
   gxe.ExpectColumnValuesToBeBetween(column="DRD", min_value=0, max_value=1440)
   ```

5. **Internet Access Frequency (IAF)** tidak boleh `NULL` untuk **tahun ≥ 2021**

   ```python
   gxe.ExpectColumnValuesToNotBeNull(column="IAF", row_condition="Year >= 2021")
   ```

---

## 🛑 Hasil Validasi Awal

* Semua rules di atas **FAIL** saat pertama kali dijalankan karena:

  * Dataset awal masih dalam format raw (kolom bercampur delimiter `;`).
  * Banyak missing values (`NaN`) pada kolom numerik dan internet usage.
  * Tipe data tidak sesuai (angka terbaca sebagai string).

Output ringkasan validasi awal:

```
❌ ExpectationConfiguration 1–5 : FAIL
Reason: {}
```

---

## 🧹 Data Cleaning

Langkah cleaning yang dilakukan:

1. Memisahkan kolom yang awalnya ter-merge dengan delimiter `;`.
2. Mengubah tipe data ke format numerik yang benar (`int64`, `float64`).
3. Menghapus atau mengisi missing values pada kolom tertentu.

### 📐 Hasil Setelah Cleaning

* **Shape dataset:** `(140, 9)`

* **Null values:**

  * `Internet Access Frequency per Week` → 35 missing
  * `Daily Internet Duration (in minutes)` → 35 missing
  * Semua kolom lain sudah **bersih** (0 null).

* **Data Types:**

  * `Year` → `int64`
  * Semua indikator kuantitatif → `float64`
  * `Provinsi`, `Category` → `object`

* **Sample Data Setelah Cleaning:**

  | Provinsi | Year | RF  | NR  | DRD   | IAF | DID   | Reading Interest | Category |
  | -------- | ---- | --- | --- | ----- | --- | ----- | ---------------- | -------- |
  | Aceh     | 2020 | 4.0 | 2.0 | 95.0  | NaN | NaN   | 54.68            | Moderate |
  | Aceh     | 2021 | 5.5 | 4.5 | 103.0 | 5.0 | 83.9  | 64.13            | High     |
  | Aceh     | 2022 | 5.0 | 5.5 | 94.3  | 5.5 | 123.4 | 65.85            | High     |

---

## 🎯 Kesimpulan

* Tahap validasi menunjukkan **kualitas data awal buruk** (semua aturan gagal).
* Setelah proses **data cleaning**, dataset sudah jauh lebih konsisten dan siap untuk analisis.
* Masih terdapat beberapa **missing values** pada kolom internet (35 baris), yang bisa ditangani lebih lanjut dengan imputasi atau analisis terpisah.

LAMPIRAN : 
1. LINK COLAB Data Quality Validation & Cleaning Report : https://colab.research.google.com/drive/1Xg1k2XqbhMPrqzqjc6_tk-J8crttvR7T?usp=sharing

