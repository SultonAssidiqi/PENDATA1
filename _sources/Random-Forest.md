# Implementasi Rendom Forest Menggunakan Script Python pada KNIME

Implementasi metode Random Forest menggunakan script Python pada KNIME dilakukan untuk mengklasifikasikan data evaluasi mobil menggunakan dataset dari Kaggle Car Evaluation Dataset (https://www.kaggle.com/datasets/kryusufkaya/car-evaluation). Dataset ini memiliki atribut seperti buying , maint, doors, persons, lug_boot, safety, dan class. KNIME digunakan untuk membangun workflow machine learning, sedangkan Python digunakan untuk proses training dan prediksi menggunakan algoritma RandomForestClassifier dari library scikit-learn.

Workflow terdiri dari CSV Reader, Table Partitioner, Python Script (Training), Python Script (Predict), dan Scorer. Data dibagi menjadi data training dan testing, kemudian model Random Forest dilatih menggunakan data training dan digunakan untuk memprediksi data testing. Hasil prediksi selanjutnya dievaluasi menggunakan node Scorer untuk mengetahui performa klasifikasi model.

## Tahapan Workflow

Workflow Random Forest pada tugas ini digunakan untuk melakukan proses klasifikasi data menggunakan algoritma Random Forest dengan bantuan node Python pada KNIME. Proses dimulai dari pembacaan dataset, pembagian data menjadi data training dan data testing, pelatihan model menggunakan Random Forest, proses prediksi data uji, hingga evaluasi hasil klasifikasi menggunakan node Scorer.

Berikut alur workflow yang digunakan:
![](https://cdn.mathpix.com/snip/images/e0DQx6ctM7tr9Ed4qV8SsfoR8D2XDgceUDdI67Lsono.original.fullsize.png)

### 1. CSV Reader

Node CSV Reader merupakan node pertama yang digunakan untuk membaca dataset berformat `.csv` ke dalam KNIME. Dataset yang dibaca akan diubah menjadi bentuk tabel sehingga dapat diproses lebih lanjut pada node berikutnya. Dataset ini terdiri dari beberapa atribut kategorikal, yaitu:


| Atribut | Keterangan |
| :-- | :-- |
| buying | Harga beli mobil |
| maint | Biaya perawatan |
| doors | Jumlah pintu |
| persons | Kapasitas penumpang |
| lug_boot | Ukuran bagasi |
| safety | Tingkat keamanan |
| class | Kategori evaluasi mobil |

Dataset Car Evaluation memiliki 1728 data dan seluruh atribut berupa data kategorikal tanpa missing value.

### 2. Table Partitioner

Node Table Partitioner digunakan untuk membagi dataset menjadi dua bagian, yaitu:

- Data Training
- Data Testing

Pada tugas kali ini, dilakukan pembagian dengan rasio 80% untuk data training dan 20% untuk data testing. Data training digunakan untuk membangun model Random Forest, sedangkan data testing digunakan untuk menguji performa model. Proses ini penting agar model dapat dievaluasi menggunakan data yang belum pernah dipelajari sebelumnya.

### 3. Python Script 1 (Training + Save)

Node **Python Script (Training)** digunakan untuk membangun model klasifikasi Random Forest menggunakan data training dari dataset *Car Evaluation*. Pada tahap ini dilakukan proses pembacaan data, preprocessing, pembentukan model, hingga penyimpanan model untuk digunakan pada tahap prediksi.

#### a. Import Library

```python
import knime.scripting.io as knio
import pandas as pd
import pickle

from sklearn.ensemble import RandomForestClassifier
```

Tahap pertama adalah melakukan import library yang dibutuhkan selama proses training model.

Library `knime.scripting.io` digunakan sebagai penghubung antara KNIME dan Python sehingga data dari node KNIME dapat dibaca ke dalam script Python. Library `pandas` digunakan untuk mengolah data dalam bentuk dataframe agar lebih mudah diproses. Library `pickle` digunakan untuk menyimpan model Random Forest yang telah dilatih ke dalam file berekstensi `.pkl`, sehingga model dapat digunakan kembali tanpa perlu melakukan training ulang.

Sementara itu, `RandomForestClassifier` dari library *scikit-learn* digunakan untuk membangun model klasifikasi Random Forest. Algoritma ini bekerja dengan menggabungkan banyak pohon keputusan (*decision tree*) untuk menghasilkan prediksi yang lebih stabil dan akurat.

#### b. Membaca Data Training

```python
df = knio.input_tables[0].to_pandas()
```

Pada tahap ini, data training yang berasal dari node **Table Partitioner** dibaca dari KNIME ke dalam Python.

`knio.input_tables[0]` menunjukkan tabel input pertama dari KNIME, kemudian fungsi `.to_pandas()` digunakan untuk mengubah data tersebut menjadi dataframe pandas.

Hasil pembacaan data disimpan ke variabel `df`. Dataframe ini berisi seluruh atribut dataset Car Evaluation seperti:

* `buying`
* `maint`
* `doors`
* `persons`
* `lug_boot`
* `safety`
* `class`

Dataframe pandas dipilih karena mempermudah proses manipulasi data seperti pemisahan fitur, encoding, dan training model machine learning.

#### c. Memisahkan Fitur dan Target

```python
X_train = df.drop("class", axis=1)
y_train = df["class"]
```

Tahap berikutnya adalah memisahkan data menjadi fitur (*input*) dan target (*output*).

Baris:

```python
X_train = df.drop("class", axis=1)
```

digunakan untuk mengambil seluruh atribut selain kolom `class`. Data ini akan digunakan sebagai variabel input model Random Forest.

Sedangkan:

```python
y_train = df["class"]
```

digunakan untuk mengambil kolom `class` sebagai target klasifikasi.

Pada dataset Car Evaluation:


| Kolom | Fungsi |
| :-- | :-- |
| buying | Input |
| maint | Input |
| doors | Input |
| persons | Input |
| lug_boot | Input |
| safety | Input |
| class | Target |

Model Random Forest nantinya akan mempelajari hubungan antara atribut input dengan kategori evaluasi mobil pada kolom `class`.

#### d. Encoding Data Kategorikal

```python
X_train = pd.get_dummies(X_train)
```

Seluruh atribut pada dataset Car Evaluation berbentuk data kategorikal atau teks, seperti:


| buying |
| :-- |
| low |
| high |
| vhigh |

Sedangkan algoritma Random Forest hanya dapat memproses data numerik. Oleh karena itu dilakukan proses **One Hot Encoding** menggunakan fungsi:

```python
pd.get_dummies()
```

Fungsi ini mengubah setiap kategori menjadi kolom biner bernilai 0 dan 1.

Contoh:


| buying |
| :-- |
| low |
| high |

akan berubah menjadi:


| buying_low | buying_high |
| :-- | :-- |
| 1 | 0 |
| 0 | 1 |

Artinya:

- nilai `1` menunjukkan kategori aktif,
- nilai `0` menunjukkan kategori tidak aktif.

Proses encoding sangat penting karena model machine learning tidak dapat memahami data berbentuk teks secara langsung.

Selain itu, hasil encoding membuat seluruh data memiliki format numerik sehingga dapat diproses oleh Random Forest.

#### e. Membentuk Model Random Forest

```python
model = RandomForestClassifier(
    n_estimators=100,
    random_state=42
)
```

Pada tahap ini dilakukan pembentukan model Random Forest menggunakan class `RandomForestClassifier`.

Parameter:

```python
n_estimators=100
```

menunjukkan bahwa model akan membangun sebanyak 100 pohon keputusan (*decision tree*).

Semakin banyak jumlah tree, maka model biasanya menjadi lebih stabil dan akurat karena prediksi tidak hanya bergantung pada satu pohon saja.

Sedangkan:

```python
random_state=42
```

digunakan untuk mengatur proses random agar hasil training tetap konsisten setiap kali program dijalankan.

Cara kerja Random Forest adalah:

1. Membuat banyak sampel data secara acak (*bootstrap sampling*)
2. Membentuk decision tree pada setiap sampel data
3. Setiap tree menggunakan kombinasi fitur yang berbeda secara acak
4. Seluruh tree melakukan prediksi
5. Hasil akhir ditentukan berdasarkan voting mayoritas

Karena menggunakan banyak tree, Random Forest mampu mengurangi risiko *overfitting* yang sering terjadi pada single decision tree.

#### f. Proses Training

```python
model.fit(X_train, y_train)
```

Tahap training merupakan proses utama dalam machine learning, yaitu melatih model menggunakan data training.

Perintah:

```python
model.fit(X_train, y_train)
```

digunakan untuk membuat model mempelajari pola hubungan antara atribut input dan target klasifikasi.

Pada proses ini Random Forest bekerja melalui beberapa tahapan:

1. Bootstrap Sampling

Model mengambil sampel data training secara acak dari dataset asli. Setiap decision tree mendapatkan data sampel yang berbeda.

Misalnya:

* Tree 1 → data acak A
* Tree 2 → data acak B
* Tree 3 → data acak C

Teknik ini disebut *bootstrap sampling*.

2. Pembentukan Decision Tree

Setiap sampel data digunakan untuk membentuk satu decision tree.

Pada setiap node tree, algoritma mencari atribut terbaik untuk memisahkan data menggunakan perhitungan seperti:

$$
Entropy(S) = -\sum p_i \log_2 p_i
$$

$$
Gain(S,A) = Entropy(S) - \sum \frac{|S_v|}{|S|} \times Entropy(S_v)
$$

Atribut dengan nilai *information gain* tertinggi akan dipilih sebagai percabangan terbaik.

3. Random Feature Selection

Pada setiap percabangan tree, Random Forest tidak menggunakan seluruh atribut, melainkan hanya memilih beberapa fitur secara acak.

Contoh:

* Tree pertama mungkin memilih `buying` dan `safety`
* Tree kedua memilih `maint` dan `persons`

Hal ini membuat setiap tree menjadi berbeda dan meningkatkan keberagaman model.

4. Voting Seluruh Tree

Setelah semua tree selesai dibangun, setiap tree menghasilkan prediksi kelas mobil.

Contoh:


| Tree | Prediksi |
| :-- | :-- |
| Tree 1 | acc |
| Tree 2 | acc |
| Tree 3 | unacc |
| Tree 4 | acc |

Hasil akhir ditentukan berdasarkan voting mayoritas:

$$
H(x) = \text{majority vote}(h_1, h_2, \ldots, h_n)
$$

Karena mayoritas tree menghasilkan `acc`, maka prediksi akhir adalah `acc`.

Metode voting inilah yang membuat Random Forest memiliki performa klasifikasi yang lebih baik dan lebih stabil dibandingkan decision tree tunggal.

### 4. Python Script 2 (Load Model + Predict)

Node **Python Script 2** digunakan untuk melakukan proses prediksi data testing menggunakan model Random Forest yang telah dilatih pada tahap sebelumnya. Selain melakukan prediksi, node ini juga digunakan untuk menghitung tingkat akurasi model terhadap data uji.

#### a. Membaca Nama Fitur

```python
feature_df = knio.input_tables[0].to_pandas()

model_features = feature_df["feature_names"].tolist()
```

Pada tahap training, hasil proses *One Hot Encoding* menghasilkan banyak kolom baru sesuai kategori atribut pada dataset Car Evaluation, seperti:


| buying_low | buying_high | doors_2 | doors_4 |
| :-- | :-- | :-- | :-- |
| 1 | 0 | 1 | 0 |

Nama seluruh kolom hasil encoding tersebut disimpan dan dikirim ke Python Script 2 agar struktur data testing dapat disamakan dengan data training.

Hal ini penting karena:

- Model Random Forest hanya dapat menerima jumlah dan urutan fitur yang sama seperti saat training.
- Data testing bisa memiliki kategori yang berbeda dengan data training.

Contoh:

- Pada data training terdapat kategori `buying_high`
- Namun pada data testing kategori tersebut tidak muncul

Jika struktur kolom berbeda, maka proses prediksi akan gagal.

Karena itu seluruh nama fitur disimpan dalam variabel:

```python
model_features
```


#### b. Membaca Data Testing

```python
df = knio.input_tables[1].to_pandas()
```

Data testing hasil *Partitioning* dibaca dari KNIME lalu diubah menjadi dataframe pandas agar dapat diproses menggunakan Python dan Scikit-Learn.

Dataset yang digunakan adalah **Car Evaluation Dataset** yang berisi atribut:

* buying
* maint
* doors
* persons
* lug_boot
* safety

Serta target:

* class

---

#### c. Memisahkan Fitur dan Target

```python
X_test = df.drop("class", axis=1)
y_true = df["class"]
```

Tahap ini digunakan untuk memisahkan:

* **Fitur (X_test)** → atribut input mobil
* **Target (y_true)** → kelas asli mobil

Contoh data:


| buying | maint | safety | class |
| :-- | :-- | :-- | :-- |
| low | med | high | good |

Maka:

* `X_test` berisi:
    * buying
    * maint
    * safety
* `y_true` berisi:
    * good

Nilai `y_true` nantinya digunakan untuk membandingkan hasil prediksi model.

#### d. Encoding Data Testing

```python
X_test = pd.get_dummies(X_test)
```

Karena seluruh atribut pada dataset Car Evaluation berbentuk kategorikal (teks), maka data testing juga harus diubah menjadi bentuk numerik menggunakan *One Hot Encoding*.

Contoh:


| safety |
| :-- |
| high |

Menjadi:


| safety_high | safety_low | safety_med |
| :-- | :-- | :-- |
| 1 | 0 | 0 |

Proses ini wajib dilakukan karena algoritma Random Forest tidak dapat memproses data string secara langsung.

#### e. Menyamakan Struktur Kolom

```python
X_test = X_test.reindex(
    columns=model_features,
    fill_value=0
)
```

Tahap ini merupakan salah satu proses paling penting pada sistem prediksi.

Hasil encoding data testing belum tentu sama dengan data training karena:

* Ada kategori yang tidak muncul
* Urutan kolom bisa berbeda

Contoh:

### Data Training

| buying_low | buying_high | safety_high |
| :-- | :-- | :-- |

### Data Testing

| buying_low | safety_high |
| :-- | :-- |

Kolom `buying_high` tidak muncul pada data testing.

Jika langsung diprediksi:

* jumlah fitur berbeda
* model akan error

Karena itu digunakan:

```python
reindex()
```

Fungsi ini digunakan untuk:

1. Menyamakan urutan kolom
2. Menambahkan kolom yang hilang
3. Mengisi nilai kosong dengan 0
```python
fill_value=0
```

Artinya:

- jika suatu kategori tidak muncul pada data testing
- maka nilainya dianggap 0

Dengan cara ini, struktur fitur data testing menjadi identik dengan data training sehingga model dapat melakukan prediksi dengan benar.

#### f. Load Model

```python
with open("rf_model.pkl", "rb") as file:
    model = pickle.load(file)
```

Pada tahap training sebelumnya, model Random Forest telah disimpan ke file:

```python
rf_model.pkl
```

File tersebut kemudian dipanggil kembali menggunakan library `pickle`.

Fungsi:

- `wb` → menyimpan model (*write binary*)
- `rb` → membaca model (*read binary*)

Tahap ini memungkinkan model yang sudah dilatih digunakan kembali tanpa harus melakukan training ulang.

#### g. Proses Prediksi

```python
y_pred = model.predict(X_test)
```

Tahap ini digunakan untuk melakukan klasifikasi data testing menggunakan model Random Forest.

Cara kerja Random Forest saat prediksi:

1. Data testing masuk ke seluruh decision tree
2. Setiap tree menghasilkan prediksi kelas
3. Seluruh hasil tree dikumpulkan
4. Dilakukan voting mayoritas
5. Kelas dengan suara terbanyak menjadi hasil akhir

#### h. Menghitung Accuracy

```python
acc = accuracy_score(y_true, y_pred)
```

Tahap ini digunakan untuk menghitung tingkat akurasi model terhadap data testing. Semakin tinggi nilai accuracy, maka semakin baik kemampuan model Random Forest dalam mengklasifikasikan data mobil pada dataset Car Evaluation.

### 5. Table View

Setelah proses training dan prediksi selesai dilakukan, node Table View digunakan kembali untuk menampilkan hasil klasifikasi Random Forest. Pada kode Python Predict, hasil prediksi disimpan menggunakan perintah:

```
result = df.copy()
result["Prediction"] = y_pred
```

Artinya, model Random Forest akan menambahkan kolom baru bernama Prediction yang berisi hasil prediksi kelas mobil.

Kemudian data dikirim kembali ke KNIME:

```
knio.output_tables[0] = knio.Table.from_pandas(result)
```

Hasil tersebut ditampilkan menggunakan Table View sehingga pengguna dapat membandingkan:

- nilai asli (class)
- hasil prediksi (Prediction)

Jika nilai pada kolom Prediction sama dengan class, maka data berhasil diklasifikasikan dengan benar oleh model Random Forest.

### 6. Scorer

Node Scorer digunakan untuk mengevaluasi hasil klasifikasi dari Random Forest.

Node ini membandingkan:

- nilai asli (class)
- hasil prediksi (Prediction)

Output yang dihasilkan biasanya berupa:

- Accuracy
- Confusion Matrix
- Precision
- Recall

Scorer digunakan untuk mengetahui seberapa baik model Random Forest dalam melakukan klasifikasi data evaluasi mobil.

![original image](https://cdn.mathpix.com/snip/images/QPlza_BeoVXWyW1VKOR5MaNvOlNSgwSSiADSInr4Uac.original.fullsize.png)

Berdasarkan gambar diatas, model Random Forest memperoleh nilai Accuracy sebesar:

- Accuracy=0.962

yang berarti model mampu mengklasifikasikan data dengan tingkat ketepatan sebesar 96,2%. Selain itu, nilai Cohen’s Kappa sebesar:

- Kappa=0.924

Hal ini menunjukkan bahwa tingkat kesesuaian hasil prediksi model dengan data asli termasuk kategori sangat baik.

Hasil evaluasi tiap kelas juga menunjukkan performa yang tinggi:

- Kelas unacc memiliki Recall 0.965 dan Precision 0.991, yang berarti model sangat baik dalam mengenali data unacc.
- Kelas acc memiliki Recall 0.965 dan Precision 0.901, sehingga sebagian besar data berhasil diprediksi dengan benar meskipun masih terdapat sedikit kesalahan klasifikasi.
- Kelas vgood memiliki Recall 0.882 dan Precision 0.938. Nilai recall yang lebih rendah menunjukkan masih ada beberapa data vgood yang belum terdeteksi dengan sempurna.
- Kelas good memiliki Recall 1.000 yang berarti seluruh data good berhasil dikenali oleh model.

Secara keseluruhan, hasil evaluasi menunjukkan bahwa algoritma Random Forest memiliki performa yang sangat baik dalam melakukan klasifikasi data evaluasi mobil dengan tingkat kesalahan yang rendah dan hasil prediksi yang konsisten.

