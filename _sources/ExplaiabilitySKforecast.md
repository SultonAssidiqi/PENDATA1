# Model Explainability dengan Skforecast

Referensi: [SKforecast](https://skforecast.org/0.15.1/user_guides/explainability.html#permutation-feature-importance)
Notebook Colab: [Akses di sini](https://colab.research.google.com/drive/1o1pshJwLPAUynOabkmg-23f15_228mFw?usp=sharing)

---

## 1. Gambaran Umum Prediksi

Dokumen ini membahas pembangunan model untuk memperkirakan **total kebutuhan listrik harian** (Demand) di negara bagian Victoria, Australia. Model memanfaatkan dua sumber informasi: riwayat konsumsi listrik pada hari-hari sebelumnya, serta data suhu udara harian (Temperature) sebagai faktor eksternal.

Fokus utama notebook ini bukan hanya menghasilkan nilai prediksi, melainkan **memahami alasan di balik setiap prediksi yang dihasilkan** — pendekatan ini dikenal sebagai *model explainability*. Setelah model dilatih dan prediksi dihasilkan, analisis lanjutan dilakukan untuk menjawab pertanyaan-pertanyaan seperti: fitur mana yang paling dominan? Apakah suhu yang lebih tinggi mendorong peningkatan atau justru penurunan prediksi konsumsi listrik? Faktor apa yang paling memengaruhi prediksi pada hari tertentu?

Tiga metode explainability digunakan secara bersamaan: **SHAP values**, **permutation feature importance**, dan **partial dependence plots**.

---

## 2. Struktur Data Training — Input dan Output

Sebelum proses pelatihan dimulai, skforecast terlebih dahulu mengonversi data time series mentah ke dalam format tabular yang dapat diproses oleh estimator berbasis machine learning. Proses transformasi ini dilakukan melalui metode `create_train_X_y()`.

**Input (X_train)** terdiri dari kolom-kolom berikut:

| Kolom | Deskripsi |
|---|---|
| lag_1 | Nilai Demand 1 hari sebelumnya |
| lag_2 | Nilai Demand 2 hari sebelumnya |
| lag_3 | Nilai Demand 3 hari sebelumnya |
| lag_4 | Nilai Demand 4 hari sebelumnya |
| lag_5 | Nilai Demand 5 hari sebelumnya |
| lag_6 | Nilai Demand 6 hari sebelumnya |
| lag_7 | Nilai Demand 7 hari sebelumnya |
| Temperature | Suhu rata-rata harian (variabel eksogen) |

**Output (y_train)** adalah:

| Kolom | Deskripsi |
|---|---|
| y | Nilai Demand pada hari yang diprediksi |

---

## 3. Konsep Lag

Lag merupakan teknik untuk mengubah data time series menjadi fitur yang kompatibel dengan algoritma machine learning. Secara sederhana, lag ke-*n* dari suatu kolom merepresentasikan **nilai kolom tersebut pada *n* langkah waktu sebelumnya**.

Sebagai ilustrasi, dengan data harian dan konfigurasi `lags=7`:

| Tanggal | Demand (aktual) | lag_1 | lag_2 | lag_3 | ... | lag_7 |
|---|---|---|---|---|---|---|
| 7 Jan 2012 | 200.693 | 205.338 | 211.066 | 213.792 | ... | 82.531 |
| 8 Jan 2012 | 200.061 | 200.693 | 205.338 | 211.066 | ... | 227.778 |

Nilai lag_1 pada 7 Januari merupakan Demand tanggal 6 Januari, lag_2 adalah tanggal 5 Januari, dan seterusnya hingga lag_7 yang mencerminkan kondisi tujuh hari sebelumnya.

Penggunaan lag didasari oleh asumsi bahwa **konsumsi listrik hari ini memiliki keterkaitan erat dengan pola konsumsi pada hari-hari sebelumnya**.

---

## Implementasi Notebook

### Cell 1 — Instalasi Library

```python
!pip install shap lightgbm skforecast
```

Perintah ini menginstal tiga library utama yang diperlukan:
- **shap** — digunakan untuk menghitung dan memvisualisasikan SHAP values sebagai alat explainability.
- **lightgbm** — framework gradient boosting berbasis pohon yang berperan sebagai model regresi internal.
- **skforecast** — library forecasting time series yang memungkinkan penggunaan estimator scikit-learn untuk prediksi multi-step.
- Library lainnya telah tersedia secara bawaan di lingkungan Colab.

---

### Cell 2 — Inisialisasi JavaScript SHAP

```python
from IPython.display import display, HTML
shap.initjs()
```

Fungsi `shap.initjs()` memuat komponen JavaScript yang dibutuhkan SHAP untuk merender visualisasi interaktif seperti force plot di dalam notebook Colab.

---

### Cell 3 — Import Library

```python
import pandas as pd
import matplotlib.pyplot as plt
import shap
from sklearn.inspection import permutation_importance
from sklearn.inspection import PartialDependenceDisplay
from lightgbm import LGBMRegressor
from skforecast.datasets import fetch_dataset
from skforecast.recursive import ForecasterRecursive
```

Seluruh library yang digunakan sepanjang notebook diimpor di sini:
- `pandas` dan `matplotlib` untuk manipulasi data dan visualisasi.
- `shap` untuk analisis SHAP values.
- `permutation_importance` dan `PartialDependenceDisplay` dari `sklearn.inspection` untuk dua metode explainability tambahan.
- `LGBMRegressor` sebagai estimator regresi.
- `fetch_dataset` untuk mengunduh dataset contoh dari repositori skforecast.
- `ForecasterRecursive` sebagai kelas forecaster utama.

---

### Cell 4 — Unduh Dataset

```python
data = fetch_dataset(name="vic_electricity")
data.head(3)
```

Dataset `vic_electricity` berisi catatan konsumsi listrik harian di Victoria, Australia, dengan kolom utama `Demand` (dalam MW) dan `Temperature` (suhu rata-rata di Melbourne). Perintah `data.head(3)` digunakan untuk memverifikasi tiga baris pertama dataset.

---

### Cell 5 — Agregasi ke Frekuensi Harian

```python
data = data.resample('D').agg({'Demand': 'sum', 'Temperature': 'mean'})
data.head(3)
```

Data asli memiliki resolusi setengah jam (30 menit). Cell ini melakukan resampling ke frekuensi harian (`'D'`) dengan ketentuan:
- `Demand` dijumlahkan (`sum`) untuk memperoleh total konsumsi per hari.
- `Temperature` dirata-ratakan (`mean`) sebagai representasi suhu harian.

---

### Cell 6 — Pembagian Data Train dan Test

```python
data_train = data.loc[: '2014-12-21']
data_test  = data.loc['2014-12-22':]
```

Dataset dibagi berdasarkan tanggal menjadi dua bagian:
- `data_train` — data dari awal hingga 21 Desember 2014, digunakan saat pelatihan model.
- `data_test` — data mulai 22 Desember 2014, dipakai untuk evaluasi dan prediksi.

Pemisahan berbasis waktu ini adalah standar dalam time series forecasting untuk menghindari kebocoran data (*data leakage*) dari masa depan.

---

### Cell 7 — Membangun dan Melatih Forecaster

```python
forecaster = ForecasterRecursive(
                 estimator = LGBMRegressor(random_state=123, verbose=-1),
                 lags      = 7
             )

forecaster.fit(
    y    = data_train['Demand'],
    exog = data_train['Temperature']
)
```

Model forecasting rekursif dibuat menggunakan `ForecasterRecursive` dengan konfigurasi:
- **estimator** — `LGBMRegressor` dengan `random_state=123` agar hasil dapat direproduksi.
- **lags=7** — model menggunakan tujuh nilai terakhir (`lag_1` hingga `lag_7`) sebagai fitur, merepresentasikan satu minggu ke belakang.

Metode `forecaster.fit()` melatih model dengan `Demand` sebagai target dan `Temperature` sebagai variabel eksogen.

---

### Cell 8 — Feature Importances dari Model

```python
forecaster.get_feature_importances()
```

Metode `get_feature_importances()` mengambil nilai kepentingan fitur dari estimator internal. Metode ini mengakses atribut `feature_importances_` (untuk model pohon) atau `coef_` (untuk model linear), lalu menampilkan kontribusi masing-masing fitur terhadap hasil prediksi.

---

### Cell 9 — Ekstraksi Matriks Training

```python
X_train, y_train = forecaster.create_train_X_y(
                       y    = data_train['Demand'],
                       exog = data_train['Temperature']
                   )

display(X_train.head(3))
display(y_train.head(3))
```

`create_train_X_y()` mengekstrak matriks input (`X_train`) dan target (`y_train`) yang digunakan oleh estimator internal saat pelatihan. `X_train` memuat kolom `lag_1` hingga `lag_7` beserta `Temperature`, sementara `y_train` berisi nilai `Demand` yang menjadi sasaran prediksi. Matriks ini dibutuhkan secara eksplisit untuk perhitungan SHAP values pada langkah berikutnya.

---

### Cell 10 — Pembuatan SHAP Explainer dan Perhitungan SHAP Values

```python
explainer = shap.TreeExplainer(forecaster.estimator)
shap_values = explainer.shap_values(X_train)
```

Tahap ini mencakup pembuatan explainer dan komputasi SHAP values dari data training:
- `shap.TreeExplainer` dipilih karena estimator yang digunakan adalah model berbasis pohon (`LGBMRegressor`).
- `forecaster.estimator` digunakan untuk mengakses model regresi internal dari forecaster.
- `explainer.shap_values(X_train)` menghitung kontribusi setiap fitur terhadap masing-masing prediksi individual di seluruh data training.

---

### Cell 11 — SHAP Summary Plot (Bar)

```python
shap.summary_plot(shap_values, X_train, plot_type="bar")
```

Visualisasi ini menampilkan ringkasan dalam bentuk bar chart. Panjang setiap bar mencerminkan rata-rata nilai absolut SHAP dari suatu fitur di seluruh data training, sehingga memberikan gambaran global tentang fitur mana yang paling signifikan bagi model.

---

### Cell 12 — SHAP Summary Plot (Beeswarm)

```python
shap.summary_plot(shap_values, X_train)
```

Berbeda dari bar chart, plot beeswarm (default) menampilkan distribusi SHAP values setiap fitur di seluruh observasi. Warna titik mencerminkan nilai fitur (merah = tinggi, biru = rendah), memperlihatkan arah dan besaran pengaruh nilai fitur terhadap prediksi.

---

### Cell 13 — Force Plot: Satu Observasi dari Training Set

```python
plot = shap.force_plot(explainer.expected_value, shap_values[0, :], X_train.iloc[0, :])
html_content = f"""
<html>
<head>{shap.getjs()}</head>
<body>{plot.html()}</body>
</html>
"""
display(HTML(html_content))
```

Force plot ditampilkan untuk satu observasi pertama (indeks 0) dari data training. Visualisasi lokal ini memperlihatkan bagaimana masing-masing fitur mendorong prediksi ke atas atau ke bawah dari nilai baseline (`explainer.expected_value`).

Karena force plot memerlukan JavaScript untuk dirender, visualisasi dibungkus secara manual menggunakan `shap.getjs()` dan `display(HTML(...))` agar tampil dengan benar di lingkungan Colab.

---

### Cell 14 — Force Plot: Beberapa Observasi Sekaligus

```python
plot = shap.force_plot(explainer.expected_value, shap_values[:200, :], X_train.iloc[:200, :])
html_content = f"""
<html>
<head>{shap.getjs()}</head>
<body>{plot.html()}</body>
</html>
"""
display(HTML(html_content))
```

Sama seperti cell sebelumnya, namun kali ini force plot mencakup 200 observasi pertama secara bersamaan. Hasilnya disebut *stacked force plot* — visualisasi interaktif yang memungkinkan eksplorasi pola kontribusi fitur secara menyeluruh di banyak prediksi dalam satu tampilan.

---

### Cell 15 — SHAP Dependence Plot

```python
fig, ax = plt.subplots(figsize=(7, 4))
shap.dependence_plot("Temperature", shap_values, X_train, ax=ax)
```

Dependence plot untuk fitur `Temperature` memperlihatkan hubungan antara nilai suhu dengan SHAP value-nya — seberapa besar dan ke arah mana suhu memengaruhi prediksi konsumsi listrik. SHAP secara otomatis menentukan fitur lain untuk pewarnaan berdasarkan interaksi terkuat yang terdeteksi.

---

### Cell 16 — Prediksi

```python
predictions = forecaster.predict(steps=10, exog=data_test['Temperature'])
predictions
```

Model menghasilkan prediksi 10 langkah ke depan (`steps=10`) menggunakan variabel eksogen suhu dari data test. Prediksi dimulai dari tanggal 22 Desember 2014, sesuai dengan batas pembagian data yang telah ditentukan sebelumnya.

---

### Cell 17 — Matriks Input untuk Prediksi

```python
X_predict = forecaster.create_predict_X(steps=10, exog=data_test['Temperature'])
X_predict
```

`create_predict_X()` menghasilkan matriks input yang dipakai forecaster saat melakukan prediksi — bukan berasal dari data training, melainkan dari data yang akan diprediksi. Matriks ini diperlukan untuk menghitung SHAP values pada hasil prediksi secara terpisah dari data historis.

---

### Cell 18 — Force Plot untuk Hasil Prediksi

```python
predicted_date = '2014-12-22'
iloc_predicted_date = X_predict.index.get_loc(predicted_date)
shap_values = explainer.shap_values(X_predict)

plot = shap.force_plot(
    explainer.expected_value,
    shap_values[iloc_predicted_date, :],
    X_predict.iloc[iloc_predicted_date, :]
)

html_content = f"""
<html>
<head>{shap.getjs()}</head>
<body>{plot.html()}</body>
</html>
"""
display(HTML(html_content))
```

Bagian ini menjelaskan prediksi untuk tanggal 22 Desember 2014 menggunakan force plot:
- `X_predict.index.get_loc(predicted_date)` — mencari posisi integer dari tanggal yang dimaksud.
- `explainer.shap_values(X_predict)` — menghitung SHAP values berdasarkan matriks prediksi.
- Force plot kemudian ditampilkan khusus untuk baris prediksi tanggal tersebut, sehingga dapat diketahui fitur mana yang paling berperan pada hari itu.

---

### Cell 21 — Permutation Feature Importance

```python
X_train, y_train = forecaster.create_train_X_y(
                       y    = data_train['Demand'],
                       exog = data_train['Temperature']
                   )

r = permutation_importance(
        estimator    = forecaster.estimator,
        X            = X_train,
        y            = y_train,
        n_repeats    = 3,
        max_samples  = 0.5,
        random_state = 123
    )

importances = pd.DataFrame({
                  'feature': X_train.columns,
                  'mean_importance': r.importances_mean,
                  'std_importance': r.importances_std
              }).sort_values('mean_importance', ascending=False)
importances
```

Metode ini merupakan alternatif explainability di luar SHAP. Cara kerjanya: nilai suatu fitur diacak secara berulang, kemudian diukur seberapa besar penurunan performa model — semakin besar penurunannya, semakin krusial fitur tersebut bagi model.

Parameter yang digunakan:
- `n_repeats=3` — pengacakan dilakukan tiga kali untuk memperoleh estimasi yang lebih stabil.
- `max_samples=0.5` — hanya 50% data yang digunakan guna mempercepat komputasi.
- `random_state=123` — memastikan hasil yang dapat direproduksi.

Hasil disusun dalam DataFrame yang diurutkan berdasarkan rata-rata nilai importance dari tertinggi ke terendah.

---

### Cell 22 — Partial Dependence Plot

```python
fig, ax = plt.subplots(figsize=(9, 4))
ax.set_title("Decision Tree")
pd.plots = PartialDependenceDisplay.from_estimator(
    estimator = forecaster.estimator,
    X         = X_train,
    features  = ["Temperature", "lag_1"],
    kind      = 'both',
    ax        = ax,
)
ax.set_title("Partial Dependence Plot")
fig.tight_layout()
```

Partial Dependence Plot (PDP) ditampilkan untuk fitur `Temperature` dan `lag_1`. PDP menggambarkan efek rata-rata suatu fitur terhadap hasil prediksi dengan menganggap fitur-fitur lain bersifat konstan (dimarginalisasi), sehingga pengaruh murni setiap fitur dapat terlihat dengan lebih jelas.