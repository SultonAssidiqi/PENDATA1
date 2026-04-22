# DATA PREPARATION

Tahap Data Preparation merupakan salah satu fase penting dalam metodologi CRISP-DM yang dilakukan setelah tahap Data Understanding. Pada fase ini, data yang telah dipelajari karakteristiknya kemudian diproses dan disiapkan agar dapat digunakan pada tahap pemodelan.

Data yang dikumpulkan dari berbagai sumber umumnya masih bersifat mentah dan belum siap untuk langsung dianalisis. Oleh karena itu, proses Data Preparation bertujuan untuk membersihkan, merapikan, dan meningkatkan kualitas data, sehingga analisis dan pemodelan dapat dilakukan secara optimal serta menghasilkan output yang lebih akurat.

### 1. Memilih Data

Pada tahap ini dilakukan proses seleksi data yang akan digunakan dalam analisis dan pembangunan model.

Dataset yang dipakai adalah **dataset Iris**, yang berisi 150 data dengan 5 atribut, yaitu:

* sepal_length
* sepal_width
* petal_length
* petal_width
* species


### 2. Membersihkan Data

##### - Cek Missing Value

|  |  |
| :-- | :-- |
| sepal_length | 0 |
| sepal_width | 0 |
| petal_length | 0 |
| petal_width | 0 |
| species | 0 |

Berdasarkan hasil pemeriksaan pada tahap Data Understanding, seluruh atribut tidak memiliki nilai yang hilang (missing value). Hal ini menunjukkan bahwa dataset sudah lengkap sehingga tidak diperlukan proses penanganan missing value.

##### - Cek Data Duplikat

Berdasarkan hasil pemeriksaan pada tahap Data Understanding, ditemukan 3 data yang terduplikasi dalam dataset. Oleh karena itu, data duplikat tersebut akan dihapus pada proses pembersihan data untuk menjaga kualitas dan keandalan dataset.

```
duplikat = df[df.duplicated()]
print(duplikat)
```

| sepal_length | sepal_width | petal_length | petal_length | petal_width | species |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 34 | 4.9 | 3.1 | 1.5 | 0.1 | Iris-setosa |
| 37 | 4.9 | 3.1 | 1.5 | 0.1 | Iris-setosa |
| 142 | 5.8 | 2.7 | 5.1 | 0.9 | Iris-virginica |

Data duplikat ditampilkan menggunakan fungsi duplicated(). Data duplikat terdapat pada baris  34, 37, dan 142.

```
df = df.drop_duplicates()
```

Keberadaan data duplikat dapat menurunkan tingkat keakuratan analisis karena data yang sama terhitung lebih dari satu kali. Oleh sebab itu, dilakukan proses pembersihan data dengan menghapus duplikasi menggunakan fungsi `drop_duplicates()` pada Python.

Setelah proses tersebut dilakukan, jumlah data menjadi berkurang dan tidak ditemukan lagi data yang terduplikasi. Dengan demikian, dataset menjadi lebih bersih dan siap digunakan untuk tahap analisis serta pemodelan.

![original image](https://cdn.mathpix.com/snip/images/n9MAjgsSondmmBsZjTsThSPfzJmFd8hVKyvfmxiaNLY.original.fullsize.png)

##### - Kesimpulan

Berdasarkan proses pembersihan data yang telah dilakukan, diperoleh hasil sebagai berikut:

* Tidak ditemukan nilai yang hilang (missing value)
* Tidak terdapat data yang terduplikasi

Dengan demikian, dataset telah berada dalam kondisi bersih dan siap digunakan pada tahap berikutnya, yaitu transformasi data dan proses pemodelan.

### 3. Integrasi Data

Tahap **data integration** bertujuan untuk menyatukan data dari berbagai sumber ke dalam satu dataset yang konsisten dan terorganisasi.

Dalam penelitian ini, dataset Iris diperoleh dari satu sumber, dan seluruh atribut yang diperlukan telah tersedia dalam satu file. Atribut tersebut meliputi sepal_length, sepal_width, petal_length, petal_width, dan species.

Karena data sudah berada dalam satu kesatuan dan tidak ada sumber lain yang perlu digabungkan, maka proses integrasi data tambahan tidak diperlukan.

