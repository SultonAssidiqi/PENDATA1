# Analisis Data Menggunakan Naive Bayes

## 1. Naive Bayes

Naive Bayes adalah metode klasifikasi berbasis probabilitas yang menggunakan konsep dari Teorema Bayes. Metode ini mengasumsikan bahwa setiap fitur bersifat independen terhadap fitur lainnya.

**Rumus Naive Bayes**
$P(C_i \mid X) = \frac{P(X \mid C_i) \cdot P(C_i)}{P(X)}$

**Keterangan:**

- $P(C_i \mid X)$ : probabilitas suatu kelas terhadap data
- $P(X \mid C_i)$ : probabilitas data terhadap kelas
- $P(C_i)$ : probabilitas kelas (prior)
- $P(X)$ : probabilitas data keseluruhan


## 2. Jenis-jenis Naive Bayes

1. **Gaussian Naive Bayes (`GaussianNB`)**
Digunakan untuk **data numerik (kontinu)** dan mengasumsikan data berdistribusi normal (Gaussian).

**Rumus**

$$
P(x \mid C_i) = \frac{1}{\sqrt{2\pi\sigma_i^2}} \exp\left(-\frac{(x - \mu_i)^2}{2\sigma_i^2}\right)
$$

**Keterangan**

- $\mu_i$ : rata-rata (mean)
- $\sigma_i^2$ : variansi

2. **Multinomial Naive Bayes (`MultinomialNB`)**
Digunakan untuk **data frekuensi (count)**.

**Rumus**

$$
P(x_j \mid C_i) = \frac{\text{count}(x_j, C_i)}{\sum_k \text{count}(x_k, C_i)}
$$

3. **Bernoulli Naive Bayes (`BernoulliNB`)**
Digunakan untuk **data biner (0/1)**.

**Rumus**

$$
P(x_j \mid C_i) = p^{x_j} (1 - p)^{(1 - x_j)}
$$

4. **Complement Naive Bayes (`ComplementNB`)**
Digunakan untuk dataset tidak seimbang.

**Rumus**

$$
\hat{\theta}_{ci} = \frac{\alpha + \sum_{j:y_j \neq c} d_{ij}}{\alpha n + \sum_{j:y_j \neq c} \sum_k d_{kj}}
$$

5. **Categorical Naive Bayes (`CategoricalNB`)**
Digunakan untuk **data kategorikal**.

**Rumus**

$$
P(x_j \mid C_i) = \frac{N_{x_j,C_i} + \alpha}{N_{C_i} + \alpha \cdot n}
$$

## 3. Dataset

Dataset yang digunakan dalam penelitian ini adalah **Gender Classification Dataset** yang diperoleh dari Kaggle.

Sumber dataset:
https://www.kaggle.com/datasets/elakiricoder/gender-classification-dataset

Dataset ini berisi karakteristik fisik yang digunakan untuk mengklasifikasikan jenis kelamin seseorang (**Male** atau **Female**).

Dataset memiliki beberapa fitur dengan tipe data campuran, yaitu numerik dan biner.

**Struktur Dataset**

Dalam penelitian ini digunakan beberapa fitur utama:

- long_hair (biner)
- forehead_width_cm (numerik)
- forehead_height_cm (numerik)
- nose_wide (biner)
- gender (class)

**Penggunaan Dataset**

Dalam tugas ini, dataset digunakan dengan dua pendekatan:

1. **Pengolahan di Excel**
    - Diambil sebanyak **10 data (sampling)**
    - Digunakan untuk **perhitungan manual Naive Bayes**
    - Bertujuan memahami proses perhitungan secara detail
2. **Pengolahan menggunakan KNIME Script Python (sklearn)**
    - Menggunakan **seluruh dataset**
    - Digunakan untuk:
        - Training model
        - Testing model
        - Evaluasi hasil klasifikasi

## 4. Proses Perhitungan

### 4.1 Perhitungan Menggunakan Excel

![original image](https://cdn.mathpix.com/snip/images/Laq0eDX28YR4DYpJbPX98-fY0IASsEcqkH1TpZUDkrY.original.fullsize.png)

![original image](https://cdn.mathpix.com/snip/images/42P6ZSQOKaq9kuoCT9PE8idduG70L-BuqE0IZ0Pen5Q.original.fullsize.png)

#### 4.1.1 Menghitung Probabilitas Prior

$$
P(C_i) = \frac{\text{jumlah data pada kelas } C_i}{\text{total data}}
$$

#### 4.1.2 Menghitung Probabilitas Likelihood

a. Untuk data kategorikal/biner:

$$
P(x_j \mid C_i) = \frac{\text{jumlah kemunculan } x_j \text{ pada } C_i}{\text{jumlah data pada } C_i}
$$

b. Untuk data numerik (Gaussian):

$$
P(x \mid C_i) = \frac{1}{\sqrt{2\pi\sigma^2}} \exp\left(-\frac{(x - \mu)^2}{2\sigma^2}\right)
$$

#### 4.1.3 Menghitung Likelihood Gabungan

$$
P(X \mid C_i) = \prod_{j=1}^{n} P(x_j \mid C_i)
$$

Contoh:

$$
P(X \mid Male) = P(long\_hair|Male) \times P(forehead\_width|Male) \times P(forehead\_height|Male) \times P(nose\_wide|Male)
$$

#### 4.1.4 Menghitung Posterior

$$
P(X \mid C_i) \cdot P(C_i)
$$

#### 4.1.5 Menentukan Hasil Klasifikasi

$$
\hat{C} = \arg\max_{C_i} \left[ P(C_i) \cdot P(X \mid C_i) \right]
$$

Jika:

- $P(X|Male) \cdot P(Male) > P(X|Female) \cdot P(Female)$
maka hasil = **Male**


### 4.2 Perhitungan Menggunakan KNIME Script Python

Pada tahap ini digunakan seluruh dataset untuk membangun model menggunakan **Gaussian Naive Bayes**.

#### 4.2.1 Node yang digunakan

Pengolahan data dilakukan menggunakan KNIME dengan beberapa node:

**1. CSV Reader**
Digunakan untuk membaca dataset dari file `.csv`.

**2. Missing Value**
Digunakan untuk menangani data kosong dengan:

- Mean untuk numerik
- Modus untuk biner

**3. Python Script**
Digunakan untuk:

- Preprocessing data
- Training model
- Prediksi


#### 4.2.2 Script Python

Berikut adalah script Python yang digunakan dalam proses klasifikasi menggunakan metode Naive Bayes pada KNIME:

```
import knime.scripting.io as knio
import pandas as pd

from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.pipeline import Pipeline
from sklearn.naive_bayes import GaussianNB
from sklearn.metrics import accuracy_score, classification_report

# 1. Ambil data dari KNIME
df = knio.input_tables[0].to_pandas()

# 2. Bersihkan nama kolom
df.columns = df.columns.str.strip()

# 3. Definisi kolom
target_col = "gender"

numerical_cols = [
    "long_hair", "forehead_width_cm", "forehead_height_cm",
    "nose_wide", "nose_long", "lips_thin", "distance_nose_to_lip_long"
]

# 4. Validasi kolom
all_cols = numerical_cols + [target_col]
missing_cols = [col for col in all_cols if col not in df.columns]

if len(missing_cols) > 0:
    raise ValueError(f"Kolom tidak ditemukan: {missing_cols}")

# 5. Pisahkan fitur & target
X = df[numerical_cols]
y = df[target_col]

# 6. Split 80:20
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# 7. Pipeline (lebih simpel)
model = Pipeline(steps=[
    ("scaler", StandardScaler()),
    ("classifier", GaussianNB())
])

# 8. Training
model.fit(X_train, y_train)

# 9. Prediksi
y_pred = model.predict(X_test)

# 10. Evaluasi
accuracy = accuracy_score(y_test, y_pred) * 100

print(f"Accuracy: {accuracy:.2f}%")
print(classification_report(y_test, y_pred))

# 11. Output ke KNIME
result_df = X_test.copy()
result_df["Actual"] = y_test.values
result_df["Predicted"] = y_pred
result_df["Accuracy"] = accuracy

knio.output_tables[0] = knio.Table.from_pandas(result_df)
```


## 5. Hasil dan Pembahasan

### 5.1 Hasil Prediksi Model

![Hasil KNIME](https://cdn.mathpix.com/snip/images/QbVGuz8ceb2wlT2wGR5RDvrsWjZ1wkjq5bmbqzKosBc.original.fullsize.png)

Berdasarkan hasil pengolahan data menggunakan Naive Bayes pada KNIME, diperoleh tabel yang berisi:

- fitur input
- nilai Actual
- nilai Predicted
- nilai Accuracy


### 5.2 Analisis Hasil Prediksi

Sebagian besar data menunjukkan kesesuaian antara nilai Actual dan Predicted, yang berarti model mampu melakukan klasifikasi dengan baik.

Namun, terdapat beberapa kesalahan prediksi, misalnya:

- Actual: Male
- Predicted: Female

Hal ini disebabkan oleh kemiripan fitur antar kelas.

### 5.3 Evaluasi Model

Model menghasilkan akurasi sebesar **87.5%**, yang menunjukkan performa cukup baik dalam melakukan klasifikasi.

## 6. Kesimpulan

Metode Naive Bayes dapat digunakan untuk melakukan klasifikasi pada dataset gender classification dengan baik, baik secara manual maupun menggunakan KNIME dan Python.

