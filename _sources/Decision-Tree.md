# Implementasi Decision Tree Menggunakan KNIME

## Deskripsi Node Decision Tree KNIME

![original image](https://cdn.mathpix.com/snip/images/aHfp5luKy0vr4VTUzc509ambsVFYdZof7rIbU2GqL8A.original.fullsize.png)

Dataset yang digunakan pada workflow ini adalah dataset Car Evaluation, yaitu dataset yang digunakan untuk melakukan klasifikasi kualitas mobil berdasarkan beberapa atribut seperti harga mobil, biaya perawatan, jumlah pintu, kapasitas penumpang, ukuran bagasi, dan tingkat keamanan mobil.

Target klasifikasi pada dataset ini adalah menentukan kategori kelayakan mobil, seperti:

- unacc → tidak layak
- acc → layak
- good → baik
- vgood → sangat baik


### 1. CSV Reader

Node CSV Reader digunakan untuk membaca dan mengimpor dataset car_evaluation.csv ke dalam KNIME. Dataset ini berisi data evaluasi mobil berdasarkan beberapa atribut seperti harga pembelian (buying), biaya perawatan (maint), jumlah pintu (doors), kapasitas penumpang (persons), ukuran bagasi (lug_boot), dan tingkat keamanan (safety).

![original image](https://cdn.mathpix.com/snip/images/Y8gsq7eheTYRsU9XFi7VKi9bFt4S2qMyoq1TAipuBWw.original.fullsize.png)

Node ini menjadi tahap awal agar data dapat diproses pada tahapan klasifikasi berikutnya.

### 2. Table Partitioner

Node Table Partitioner digunakan untuk membagi dataset menjadi dua bagian, yaitu:

- data latih
- data uji

Data latih digunakan untuk membentuk pohon keputusan menggunakan algoritma Decision Tree, sedangkan data uji digunakan untuk menguji hasil klasifikasi yang dihasilkan model.

Pembagian data biasanya dilakukan dengan perbandingan:

- 70% data latih
- 30% data uji

Tahap ini penting agar hasil klasifikasi dapat diuji menggunakan data yang berbeda dari data pembentukan model.

### 3. Color Manager

Node Color Manager digunakan untuk memberikan warna pada setiap kategori atau kelas data pada dataset Car Evaluation agar proses visualisasi menjadi lebih jelas dan mudah dipahami. Pada workflow ini, node Color Manager berfungsi untuk membedakan setiap hasil klasifikasi mobil berdasarkan kategori kelayakannya menggunakan warna tertentu.

![original image](https://cdn.mathpix.com/snip/images/H_MvoShX9xCZ0RAymQjCHFOjvbX-yL1kwBrhF0j78_k.original.fullsize.png)

Node ini membantu membedakan setiap kategori hasil klasifikasi secara visual.

### 4. Color Appender

Node Color Appender digunakan untuk menambahkan informasi warna dari node Color Manager ke dalam dataset.

Dengan node ini, data hasil klasifikasi dapat ditampilkan menggunakan warna sesuai kategori mobil sehingga mempermudah proses visualisasi dan analisis data.

### Decision Tree Learner

Node Decision Tree Learner digunakan untuk membentuk model klasifikasi dalam bentuk pohon keputusan berdasarkan data latih yang telah dibagi sebelumnya pada node Table Partitioner. Node ini merupakan tahap utama dalam proses klasifikasi karena pada tahap ini algoritma Decision Tree mulai melakukan pembentukan aturan keputusan berdasarkan pola data pada dataset Car Evaluation.

Algoritma Decision Tree bekerja dengan cara memilih atribut yang paling berpengaruh terhadap kategori kualitas mobil, kemudian membagi data menjadi beberapa percabangan hingga terbentuk struktur pohon keputusan. Setiap percabangan pada pohon menunjukkan kondisi tertentu dari atribut data yang digunakan.

Berikut settingan yang digunakan dalam Decision Tree Learner:

![original image](https://cdn.mathpix.com/snip/images/gaIyN4BZ_JptVLXsWDYk4oQ19SW_FTj9dR42UyvSYdM.original.fullsize.png)

Hasil node ini berupa pohon keputusan yang digunakan untuk menentukan kategori kualitas mobil.

### Decision Tree Predictor

Node Decision Tree Predictor digunakan untuk melakukan proses klasifikasi pada data uji menggunakan pohon keputusan yang telah dibentuk sebelumnya.

Node ini akan menghasilkan prediksi kategori mobil seperti:

- unacc
- acc
- good
- vgood

Hasil prediksi akan dibandingkan dengan data asli pada tahap evaluasi.

### Scorer

Node Scorer digunakan untuk mengevaluasi hasil klasifikasi dari algoritma Decision Tree.

Node ini membandingkan kategori asli pada dataset, dengan hasil prediksi dari Decision Tree Predictor.

Hasil evaluasi:

![original image](https://cdn.mathpix.com/snip/images/sfYvYNTo9Cm8KuiarhfAkT47DDve-cuClB_KKoCpDho.original.fullsize.png)

Melalui node ini dapat diketahui tingkat akurasi yang didapat adalah 93% dalam algoritma Decision Tree untuk mengklasifikasikan kualitas mobil berdasarkan atribut pada dataset Car Evaluation.

