# UAS | Analisis Perbandingan Decision Tree dan Random Forest

> **Sumber:** [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/856/higher+education+students+performance+evaluation)
> **Tema Analisis:** Random Forest \& Decision Tree

## Identitas Saya

| Keterangan | Isi |
| :-- | :-- |
| Nama | Moh. Sulton Muqsith A. |
| NIM | 240411100221 |
| Mata Kuliah | Penambangan Data / Data Mining |
| Kelas | Penambangan Data B |
| Dosen Pengampu | Bapak Mula'ab, S.Si., M.Kom. |
| Tools Utama | IPYNB / Google Colab |
| Metode | Decision Tree dan Random Forest |
| Dataset | Higher Education Students Performance Evaluation |
| Sumber Dataset | UCI Machine Learning Repository |

---
Dataset ini dikumpulkan dari mahasiswa **Fakultas Teknik** dan **Fakultas Ilmu Pendidikan** pada tahun 2019, dengan tujuan memprediksi performa mahasiswa di akhir semester menggunakan teknik Machine Learning. Dataset tersedia di UCI Machine Learning Repository dan bersifat **multivariate** dengan tugas utama **klasifikasi**.


| Atribut | Detail |
| :-- | :-- |
| Jumlah Instansi | 145 |
| Jumlah Fitur | 31 |
| Tipe Fitur | Integer (Kategorik) |
| Missing Values | Tidak ada |
| Domain | Ilmu Sosial / Pendidikan |


---

## Variabel Target (Output)

Variabel output adalah **nilai akhir mata kuliah** dengan 8 kelas:


| Kode | Label |
| :-- | :-- |
| 0 | Fail |
| 1 | DD |
| 2 | DC |
| 3 | CC |
| 4 | CB |
| 5 | BB |
| 6 | BA |
| 7 | AA |

Ini menjadikan masalah ini sebagai **klasifikasi multi-kelas**, yang sangat cocok untuk algoritma Random Forest dan Decision Tree.

---

## Kelompok Fitur (31 Variabel)

Fitur nomor 1–10 mencakup pertanyaan personal, fitur 11–16 mencakup pertanyaan tentang keluarga, dan fitur sisanya mencakup kebiasaan belajar.

### 1. Fitur Personal (Variabel 1–10)

| No | Variabel | Keterangan |
| :-- | :-- | :-- |
| 1 | Student Age | 1: 18-21, 2: 22-25, 3: di atas 26 |
| 2 | Sex | 1: Perempuan, 2: Laki-laki |
| 3 | Graduated High-School Type | 1: Swasta, 2: Negeri, 3: Lainnya |
| 4 | Scholarship Type | 1: Tidak ada, 2: 25%, 3: 50%, 4: 75%, 5: Penuh |
| 5 | Additional Work | 1: Ya, 2: Tidak |
| 6 | Regular Artistic or Sports Activity | 1: Ya, 2: Tidak |
| 7 | Do You Have a Partner | 1: Ya, 2: Tidak |
| 8 | Total Salary (if available) | 1: \$135-200, 2: \$201-270, 3: \$271-340, 4: \$341-410, 5: >\$410 |
| 9 | Transportation to University | 1: Bus, 2: Mobil/Taksi, 3: Sepeda, 4: Lainnya |
| 10 | Accommodation Type | 1: Sewa, 2: Asrama, 3: Keluarga, 4: Lainnya |

### 2. Fitur Keluarga (Variabel 11–16)

| No | Variabel | Keterangan |
| :-- | :-- | :-- |
| 11 | Mother's Education | 1: SD, 2: SMP, 3: SMA, 4: Universitas, 5: S2, 6: S3 |
| 12 | Father's Education | 1: SD, 2: SMP, 3: SMA, 4: Universitas, 5: S2, 6: S3 |
| 13 | Number of Siblings | 1: 1, 2: 2, 3: 3, 4: 4, 5: 5 atau lebih |
| 14 | Parental Status | 1: Menikah, 2: Cerai, 3: Meninggal (salah satu/keduanya) |
| 15 | Mother's Occupation | 1: Pensiun, 2: Ibu Rumah Tangga, 3: PNS, 4: Swasta, 5: Wirausaha, 6: Lainnya |
| 16 | Father's Occupation | 1: Pensiun, 2: PNS, 3: Swasta, 4: Wirausaha, 5: Lainnya |

### 3. Fitur Kebiasaan Belajar (Variabel 17–31)

| No | Variabel | Keterangan |
| :-- | :-- | :-- |
| 17 | Weekly Study Hours | 1: Tidak ada, 2: <5 jam, 3: 6-10 jam, 4: 11-20 jam, 5: >20 jam |
| 18 | Reading Frequency (Non-Scientific) | 1: Tidak pernah, 2: Kadang, 3: Sering |
| 19 | Reading Frequency (Scientific) | 1: Tidak pernah, 2: Kadang, 3: Sering |
| 20 | Attendance to Seminars/Conferences | 1: Ya, 2: Tidak |
| 21 | Impact of Projects on Success | 1: Positif, 2: Negatif, 3: Netral |
| 22 | Attendance to Classes | 1: Selalu, 2: Kadang, 3: Tidak pernah |
| 23 | Preparation to Midterm Exams 1 | 1: Sendiri, 2: Bersama teman, 3: Tidak berlaku |
| 24 | Preparation to Midterm Exams 2 | 1: Mendekati ujian, 2: Rutin selama semester, 3: Tidak pernah |
| 25 | Taking Notes in Classes | 1: Tidak pernah, 2: Kadang, 3: Selalu |
| 26 | Listening in Classes | 1: Tidak pernah, 2: Kadang, 3: Selalu |
| 27 | Discussion Improves Interest | 1: Tidak pernah, 2: Kadang, 3: Selalu |
| 28 | Flip-Classroom | 1: Tidak berguna, 2: Berguna, 3: Tidak berlaku |
| 29 | Cumulative GPA Last Semester (/4.00) | 1: <2.00, 2: 2.00-2.49, 3: 2.50-2.99, 4: 3.00-3.49, 5: >3.49 |
| 30 | Expected GPA at Graduation (/4.00) | 1: <2.00, 2: 2.00-2.49, 3: 2.50-2.99, 4: 3.00-3.49, 5: >3.49 |
| 31 | Course ID | Identifikasi mata kuliah |


---

## Relevansi untuk Random Forest \& Decision Tree

Dataset ini sangat ideal untuk kedua algoritma tersebut karena beberapa alasan:

- **Semua fitur bertipe kategorik (integer)** — Decision Tree dapat langsung mempartisi data berdasarkan nilai diskret tanpa perlu normalisasi.
- **Tidak ada missing values** — Tidak perlu preprocessing imputasi, sehingga langsung siap digunakan.
- **Multi-kelas (8 label)** — Random Forest unggul dalam menangani multi-kelas melalui mekanisme voting dari banyak pohon yang mengurangi bias dan varians.
- **Fitur campuran (personal, keluarga, akademik)** — Feature importance dari Random Forest dapat mengidentifikasi faktor mana yang paling berpengaruh terhadap nilai akhir.
- **Jumlah data kecil (145 instansi)** — Decision Tree tunggal berpotensi overfitting; Random Forest dengan ensemble lebih robust untuk ukuran data kecil ini.

---

## Potensi Analisis

| Analisis | Keterangan |
| :-- | :-- |
| **Feature Importance** | RF dapat meranking fitur seperti IPK, jam belajar, dan kehadiran kelas sebagai prediktor terkuat |
| **Decision Tree Visualization** | Pohon keputusan dapat divisualisasikan untuk interpretasi aturan klasifikasi secara transparan |
| **Perbandingan Akurasi** | RF umumnya lebih akurat dari DT tunggal pada data kecil karena mekanisme bagging |
| **Cross-Validation** | Dengan hanya 145 data, k-fold CV penting untuk evaluasi model yang andal |
| **Hyperparameter Tuning** | Optimasi `max_depth`, `n_estimators`, dan `min_samples_split` untuk performa terbaik |


---

## Import Dataset (Python)

```python
# Install library
pip install ucimlrepo

# Import dataset
from ucimlrepo import fetch_ucirepo

higher_education_students_performance_evaluation = fetch_ucirepo(id=856)

# Data sebagai pandas dataframes
X = higher_education_students_performance_evaluation.data.features
y = higher_education_students_performance_evaluation.data.targets

# Metadata
print(higher_education_students_performance_evaluation.metadata)
print(higher_education_students_performance_evaluation.variables)
```


---
# Analisis Menggunakan Python (Jupyter Notebook)

## 

---

## Fokus Analisis

Dalam notebook ini, dilakukan analisis perbandingan antara metode **Decision Tree** dan **Random Forest** guna memprediksi kategori performa mahasiswa. Variabel target pada dataset adalah `OUTPUT Grade` dengan rentang nilai 0 hingga 7. Agar klasifikasi lebih stabil dan mudah diinterpretasikan, target tersebut disederhanakan menjadi tiga kategori:


| Grade Asli | Kategori Performa |
| :-- | :-- |
| 0, 1, 2 | Rendah |
| 3, 4, 5 | Sedang |
| 6, 7 | Tinggi |

Tujuan utama analisis ini adalah menentukan model yang menghasilkan prediksi paling akurat sekaligus mengidentifikasi fitur yang paling berpengaruh terhadap kategori performa mahasiswa.

---

## 1. Pendahuluan

Penambangan data (*data mining*) merupakan proses menemukan pola bermakna dari kumpulan data berukuran besar. Pada analisis ini digunakan dataset **Higher Education Students Performance Evaluation** dari UCI Machine Learning Repository — sebuah dataset yang memuat informasi mahasiswa mencakup faktor personal, latar belakang keluarga, dan kebiasaan akademik.

Permasalahan yang dikaji adalah bagaimana memprediksi kategori performa mahasiswa berdasarkan atribut-atribut yang tersedia. Karena target yang diprediksi berbentuk kategori diskrit, maka pendekatan yang digunakan adalah **klasifikasi**.

Dua algoritma yang dibandingkan:

1. **Decision Tree** — model klasifikasi berbasis aturan keputusan yang bersifat transparan dan mudah divisualisasikan.
2. **Random Forest** — model ensemble yang membangun ratusan pohon keputusan secara paralel untuk meningkatkan stabilitas dan akurasi prediksi.

---

## 2. Instalasi dan Import Library

Langkah pertama adalah menginstal dependensi yang diperlukan. Jika dijalankan di Google Colab, library `ucimlrepo` akan digunakan untuk mengambil dataset langsung dari UCI Machine Learning Repository.

```python
!pip install ucimlrepo

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

from ucimlrepo import fetch_ucirepo
from sklearn.model_selection import train_test_split
from sklearn.tree import DecisionTreeClassifier, plot_tree
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import (
    classification_report,
    accuracy_score,
    confusion_matrix,
    ConfusionMatrixDisplay,
    precision_score,
    recall_score,
    f1_score
)
```


---

## 3. Load Dataset

Dataset diambil langsung dari UCI ML Repository menggunakan `fetch_ucirepo`, atau dari file lokal jika sudah tersedia.

```python
# Coba ambil dari UCI ML Repository
try:
    higher_education = fetch_ucirepo(id=856)
    X = higher_education.data.features.copy()
    y = higher_education.data.targets.copy()
    if y is not None and len(y.columns) > 0:
        df = pd.concat([X, y], axis=1)
    else:
        print("Mengambil data dari file lokal...")
        df = pd.read_csv('DATA (1).csv')
except Exception as e:
    print(f"Gagal mengambil data dari UCI: {e}")
    df = pd.read_csv('DATA (1).csv')
```


---

## 4. Eksplorasi Data

Sebelum pemodelan, dilakukan eksplorasi awal untuk memahami struktur dan kualitas data:

```python
# Informasi umum dataset
print("Informasi dataset:")
df.info()
df.head(5)
```

**Hasil eksplorasi awal:**

- Jumlah data: **145 baris**
- Jumlah fitur: **31 kolom**
- Distribusi target awal (`OUTPUT Grade`): 8 kelas (0–7)
- **Tidak ada missing values** pada seluruh kolom

---

## 5. Distribusi OUTPUT Grade

Distribusi kelas pada target asli menunjukkan ketidakseimbangan (*class imbalance*):

```
Distribusi OUTPUT Grade asli:
0     8
1    35
2    24
3    21
4    10
5    17
6    13
7    17
```

Kelas 1 (grade DD) mendominasi dengan 35 data, sementara kelas 0 (Fail) hanya berjumlah 8. Kondisi ini menjadi salah satu alasan penyederhanaan target menjadi 3 kategori.

---

## 6. Membuat Target Baru: Kategori Performa

Karena jumlah data hanya 145 baris, klasifikasi 8 kelas berpotensi menghasilkan model yang tidak stabil akibat jumlah sampel per kelas yang sangat sedikit. Oleh karena itu, target disederhanakan menjadi 3 kategori:

- **Rendah**: grade 0, 1, dan 2
- **Sedang**: grade 3, 4, dan 5
- **Tinggi**: grade 6 dan 7

```python
def kategorisasi_grade(grade):
    if grade in [0, 1, 2]:
        return 'Rendah'
    elif grade in [3, 4, 5]:
        return 'Sedang'
    else:
        return 'Tinggi'

df['Kategori_Performa'] = df['OUTPUT Grade'].apply(kategorisasi_grade)
```

Distribusi setelah kategorisasi:


| Kategori | Jumlah |
| :-- | :-- |
| Rendah | 67 |
| Sedang | 48 |
| Tinggi | 30 |


---

## 7. Preprocessing Data

Tahap preprocessing mencakup lima langkah utama:

1. Menghapus kolom identitas mahasiswa (tidak relevan untuk pemodelan).
2. Menghapus target asli `OUTPUT Grade` dari fitur.
3. Menggunakan `Kategori_Performa` sebagai target klasifikasi.
4. Memastikan seluruh fitur berbentuk numerik.
5. Mengisi nilai kosong (jika ada setelah konversi) dengan median.
```python
# Identifikasi kolom ID mahasiswa
id_cols = [
    col for col in df.columns
    if ('student' in col.lower() and 'id' in col.lower())
    or (col.lower().replace(' ', '') == 'studentid')
]

target_col = 'OUTPUT Grade'
drop_cols = id_cols + [target_col, 'Kategori_Performa']

data_model = df.dropna(subset=['Kategori_Performa']).copy()
X_model = data_model.drop(columns=drop_cols, errors='ignore')
y_model = data_model['Kategori_Performa']

# Pastikan semua fitur numerik
for col in X_model.columns:
    X_model[col] = pd.to_numeric(X_model[col], errors='coerce')

# Isi nilai kosong dengan median
X_model = X_model.fillna(X_model.median(numeric_only=True))

print("Ukuran fitur:", X_model.shape)
print("Ukuran target:", y_model.shape)
```

**Output:**

```
Kolom ID yang akan dihapus: ['STUDENT ID']
Ukuran fitur: (145, 31)
Ukuran target: (145,)
```


---

## 8. Split Data Training dan Testing

Data dibagi menjadi **80% data training** dan **20% data testing**. Parameter `stratify` digunakan agar proporsi kategori target pada kedua set tetap seimbang.

```python
X_train, X_test, y_train, y_test = train_test_split(
    X_model,
    y_model,
    test_size=0.2,
    random_state=42,
    stratify=y_model
)

print("Jumlah data training:", X_train.shape[0])
print("Jumlah data testing:", X_test.shape[0])
```

**Output:**

```
Jumlah data training: 116
Jumlah data testing: 29
Distribusi target pada data training:
  Rendah: 54
  Sedang: 38
  Tinggi: 24
```


---

## 9. Pemodelan Decision Tree

Model Decision Tree dibangun menggunakan kriteria **entropy** (Information Gain) dengan kedalaman maksimum 4 untuk menghindari overfitting.

```python
model_dt = DecisionTreeClassifier(
    criterion='entropy',
    max_depth=4,
    random_state=10
)
model_dt.fit(X_train, y_train)

# Prediksi pada data testing
pred_dt = model_dt.predict(X_test)
print("Model Decision Tree berhasil dilatih.")
print(classification_report(y_test, pred_dt))
```

**Hasil Evaluasi Decision Tree:**


| Kategori | Precision | Recall | F1-Score | Support |
| :-- | :-- | :-- | :-- | :-- |
| Rendah | 1.000 | 0.538 | 0.700 | 13 |
| Sedang | 0.700 | 0.700 | 0.700 | 10 |
| Tinggi | 0.500 | 0.667 | 0.571 | 6 |
| **Accuracy** |  |  | **0.621** | **29** |
| Weighted Avg | 0.675 | 0.621 | 0.621 | 29 |


---

## 10. Pemodelan Random Forest

Model Random Forest dibangun dengan **200 pohon keputusan** dan mengaktifkan `class_weight='balanced'` untuk menangani ketidakseimbangan kelas.

```python
model_rf = RandomForestClassifier(
    n_estimators=200,
    criterion='entropy',
    max_depth=4,
    random_state=10,
    class_weight='balanced'
)
model_rf.fit(X_train, y_train)

# Prediksi pada data testing
pred_rf = model_rf.predict(X_test)
print("Model Random Forest berhasil dilatih.")
print(classification_report(y_test, pred_rf))
```

**Hasil Evaluasi Random Forest:**


| Kategori | Precision | Recall | F1-Score | Support |
| :-- | :-- | :-- | :-- | :-- |
| Rendah | 1.000 | 1.000 | 1.000 | 13 |
| Sedang | 0.500 | 0.500 | 0.500 | 10 |
| Tinggi | 0.833 | 0.833 | 0.833 | 6 |
| **Accuracy** |  |  | **0.759** | **29** |
| Weighted Avg | 0.771 | 0.759 | 0.759 | 29 |


---

## 11. Evaluasi Model

Ringkasan metrik evaluasi kedua model:

```python
def evaluasi_model(nama, y_test, y_pred):
    return {
        'Model': nama,
        'Accuracy': accuracy_score(y_test, y_pred),
        'Precision': precision_score(y_test, y_pred, average='weighted', zero_division=0),
        'Recall': recall_score(y_test, y_pred, average='weighted', zero_division=0),
        'F1-Score': f1_score(y_test, y_pred, average='weighted', zero_division=0)
    }

hasil_evaluasi = pd.DataFrame([
    evaluasi_model('Decision Tree', y_test, pred_dt),
    evaluasi_model('Random Forest', y_test, pred_rf)
])
```


---

## 12. Confusion Matrix

### Confusion Matrix — Decision Tree

```
             Rendah  Sedang  Tinggi
Rendah          7       6       0
Sedang          2       7       1
Tinggi          0       2       4
```


### Confusion Matrix — Random Forest

```
             Rendah  Sedang  Tinggi
Rendah         12       1       0
Sedang          3       5       2
Tinggi          1       0       5
```

Random Forest secara konsisten lebih baik dalam mengklasifikasikan kelas **Rendah** (12/13 benar) dan **Tinggi** (5/6 benar) dibandingkan Decision Tree tunggal.

---

## 13. Visualisasi Decision Tree

Pohon keputusan divisualisasikan untuk memperlihatkan struktur aturan yang digunakan model dalam melakukan klasifikasi. Simpul akar (root node) menggunakan `COURSE ID` sebagai fitur pemisah pertama.

```python
plt.figure(figsize=(20, 10))
plot_tree(
    model_dt,
    feature_names=X_model.columns,
    class_names=model_dt.classes_,
    filled=True,
    rounded=True,
    fontsize=7
)
plt.title('Visualisasi Model Decision Tree')
plt.show()
```


---

## 14. Feature Importance pada Random Forest

Feature importance digunakan untuk mengidentifikasi atribut yang paling berpengaruh dalam proses prediksi kategori performa mahasiswa.

```python
feature_importance = pd.DataFrame({
    'Fitur': X_model.columns,
    'Importance': model_rf.feature_importances_
}).sort_values(by='Importance', ascending=False)

feature_importance.head(10)
```

**10 Fitur Paling Berpengaruh:**


| Rank | Fitur | Importance |
| :-- | :-- | :-- |
| 1 | COURSE ID | 0.189788 |
| 2 | 29 | 0.072192 |
| 3 | 11 | 0.044421 |
| 4 | 16 | 0.041363 |
| 5 | 2 | 0.041027 |
| 6 | 4 | 0.040636 |
| 7 | 13 | 0.040290 |
| 8 | 12 | 0.036497 |
| 9 | 30 | 0.036012 |
| 10 | 17 | 0.028628 |

> **Catatan:** `COURSE ID` mendominasi feature importance dengan nilai 0.189, jauh di atas fitur lainnya. Fitur 29 (Expected GPA at Graduation) dan 11 (Mother's Education) juga memiliki pengaruh signifikan.

---

## 15. Perbandingan Hasil Model

Perbandingan performa kedua model pada data testing:


| Metrik Evaluasi | Decision Tree | Random Forest | Selisih Peningkatan |
| :-- | :-- | :-- | :-- |
| **Accuracy** | 62.07% | **75.86%** | +13.79% |
| **Precision (Weighted)** | 67.51% | **77.13%** | +9.62% |
| **Recall (Weighted)** | 62.07% | **75.86%** | +13.79% |
| **F1-Score (Weighted)** | 62.88% | **74.57%** | +11.69% |

### Mengapa Random Forest Lebih Unggul?

**1. Efek Ensemble (Voting/Bagging)**
Decision Tree tunggal cenderung *overfitting* karena keputusan diambil dari satu jalur pohon. Random Forest melatih **200 pohon keputusan independen** secara acak (*bagging*) dan mengambil keputusan akhir berdasarkan suara terbanyak (*majority voting*), sehingga varians model berkurang drastis.

**2. Penanganan Imbalanced Class**
Distribusi target cukup tidak seimbang (Rendah: 67, Sedang: 48, Tinggi: 30). Parameter `class_weight='balanced'` pada Random Forest secara otomatis memberikan bobot lebih besar kepada kelas minoritas (Tinggi dan Sedang), sehingga model mampu mengenali kelas **Tinggi** dengan Recall **83.3%** — jauh melampaui Decision Tree yang hanya **50.0%**.

**3. Stabilitas Prediksi**
Melalui pengacakan pemilihan fitur (*feature sampling*) di setiap percabangan pohon, Random Forest mampu menangkap pola tersembunyi yang mungkin terlewat jika hanya mengandalkan algoritma pembagian tunggal berbasis *information gain/entropy* seperti pada Decision Tree.

---

## 16. Menyimpan Hasil Evaluasi dan Prediksi

Hasil evaluasi dan prediksi disimpan ke dalam file CSV untuk keperluan dokumentasi dan lampiran laporan.

```python
# Menyimpan hasil evaluasi
hasil_evaluasi.to_csv('hasil_evaluasi_decision_tree_random_forest.csv', index=False)

# Menyimpan hasil prediksi data testing
hasil_prediksi = X_test.copy()
hasil_prediksi['Aktual'] = y_test.values
hasil_prediksi['Prediksi_Decision_Tree'] = pred_dt
hasil_prediksi['Prediksi_Random_Forest'] = pred_rf
hasil_prediksi.to_csv('hasil_prediksi_decision_tree_random_forest.csv', index=False)

print("File berhasil disimpan:")
print("1. hasil_evaluasi_decision_tree_random_forest.csv")
print("2. hasil_prediksi_decision_tree_random_forest.csv")
```


---

## 18. Kesimpulan

Berdasarkan analisis yang telah dilakukan, perbandingan dua metode klasifikasi — **Decision Tree** dan **Random Forest** — untuk memprediksi kategori performa mahasiswa (**Rendah**, **Sedang**, **Tinggi**) menghasilkan temuan berikut:

**Decision Tree** menawarkan keunggulan interpretabilitas tinggi karena menghasilkan aturan keputusan yang dapat divisualisasikan secara langsung. Namun, model ini lebih rentan terhadap *overfitting* pada data berukuran kecil seperti dataset ini (145 instansi).

**Random Forest** terbukti lebih unggul secara metrik dengan akurasi **75.86%** (vs 62.07%) dan F1-Score weighted **74.57%** (vs 62.88%). Keunggulan ini diperoleh melalui mekanisme ensemble yang mengurangi varians dan kemampuannya menangani ketidakseimbangan kelas secara lebih efektif.

Dari analisis **feature importance**, `COURSE ID` menjadi fitur paling dominan, diikuti oleh Expected GPA (fitur 29) dan pendidikan ibu (fitur 11). Temuan ini menunjukkan bahwa faktor akademik dan latar belakang keluarga memiliki peran signifikan dalam menentukan capaian performa mahasiswa.

Dengan demikian, analisis ini tidak hanya membandingkan performa model secara kuantitatif, tetapi juga memberikan wawasan tentang faktor-faktor yang perlu diperhatikan dalam upaya meningkatkan capaian akademik mahasiswa.

---

## Analisis Tambahan Menggunakan Orange Data Mining

Selain analisis berbasis Python, evaluasi performa model klasifikasi juga dilakukan menggunakan aplikasi visual **Orange Data Mining**.

Alur kerja (*workflow*) yang digunakan dalam Orange mencakup:

1. **File / Data** — Memuat dataset evaluasi performa mahasiswa.
2. **Formula / Select Columns** — Menentukan target variabel (`GRADE`) dan fitur lainnya.
3. **Test and Score** — Arena evaluasi utama untuk membandingkan kedua model secara berdampingan.
4. **Tree \& Random Forest** — Dua algoritma yang dikompetisikan. Decision Tree terbukti memiliki performa yang sedikit lebih unggul dan stabil dalam mengenali kelas mahasiswa 'Rendah' dan 'Sedang', sedangkan Random Forest unggul di kelas 'Tinggi'.
5. **Confusion Matrix, ROC Analysis, dan Tree Viewer** — Digunakan untuk menganalisis detail prediksi dan menginterpretasikan aturan dari pohon keputusan.

---

*Laporan ini merupakan bagian dari tugas akhir mata kuliah Penambangan Data, Program Studi Teknik Informatika.*
*Tools: Google Colab (Python).*
---

## Referensi

> Yilmaz, N. \& Şekeroğlu, B. (2019). *Higher Education Students Performance Evaluation* [Dataset]. UCI Machine Learning Repository.
> DOI: [10.24432/C51G82](https://doi.org/10.24432/C51G82)

Paper pendamping: *"Student Performance Classification Using Artificial Intelligence Techniques"*, dipublikasikan dalam **Advances in Intelligent Systems and Computing**, vol. 1095.



