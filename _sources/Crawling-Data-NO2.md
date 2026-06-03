# Prediksi Kadar Nitrogen Dioksida (NO₂) di Kota Probolinggo Menggunakan Algoritma K-Nearest Neighbor (KNN)

## Latar belakang

Kualitas udara merupakan salah satu aspek penting yang berpengaruh terhadap kesehatan masyarakat dan kondisi lingkungan. Salah satu indikator utama pencemaran udara adalah Nitrogen Dioksida (NO₂), yaitu gas berbahaya yang umumnya dihasilkan dari aktivitas pembakaran bahan bakar fosil, seperti kendaraan bermotor, pembangkit listrik, serta kegiatan industri. Oleh karena itu, pemantauan dan peramalan kadar NO₂ diperlukan untuk mengetahui kondisi kualitas udara serta mengantisipasi potensi peningkatan tingkat pencemaran.

Seiring perkembangan teknologi penginderaan jauh, data konsentrasi NO₂ kini dapat diperoleh secara berkala melalui satelit Sentinel-5P. Data tersebut kemudian dapat dimanfaatkan untuk membangun model prediksi berdasarkan pola historis yang ada. Pada penelitian ini, digunakan metode K-Nearest Neighbor (KNN) Regression untuk memprediksi konsentrasi NO₂ di Kota Probolinggo. Hasil peramalan ini diharapkan dapat memberikan gambaran kondisi kualitas udara pada periode mendatang serta menjadi bahan pendukung dalam pengambilan keputusan terkait pengelolaan lingkungan.

## A. Pengumpulan Data

Tahap awal dalam penelitian ini adalah mengumpulkan data deret waktu (time series) harian konsentrasi NO₂ di wilayah Bangkalan, Madura. Data tersebut diperoleh melalui platform **Copernicus Data Space Ecosystem**. Sebelum proses pengambilan data dilakukan, pengguna perlu terlebih dahulu melakukan pendaftaran akun pada platform tersebut.

Selanjutnya, proses pengambilan data mengikuti panduan dari **OpenEO** yang disediakan oleh Copernicus, yang dapat diakses melalui *Panduan Pengambilan Data NO₂ dengan OpenEO*. Dokumentasi ini berisi tahapan penggunaan layanan OpenEO untuk mengakses serta mengolah data NO₂ yang terintegrasi dengan citra satelit Sentinel-5P. Dengan mengikuti langkah-langkah tersebut, data dapat diperoleh dan diolah sesuai kebutuhan analisis penelitian.

### 1. Instalasi Library OpenEO

```
!pip install openeo
```

digunakan untuk menginstal library OpenEO pada lingkungan Python. Library ini dibutuhkan agar program dapat terhubung dengan Copernicus Data Space Ecosystem dan memungkinkan pengambilan serta pengolahan data penginderaan jauh dari satelit.

### 2. Import Library OpenEO

```
import openeo
```

digunakan untuk mengimpor library OpenEO ke dalam program Python. Setelah library berhasil diinstal, perintah ini diperlukan agar seluruh fungsi dan fitur yang tersedia pada OpenEO dapat digunakan.

Library OpenEO berfungsi sebagai antarmuka yang menghubungkan Python dengan Copernicus Data Space Ecosystem, sehingga pengguna dapat melakukan koneksi ke server, mengakses data satelit, serta menjalankan proses pengolahan data penginderaan jauh secara langsung melalui kode. Tahap ini menjadi langkah awal sebelum melakukan autentikasi dan pengambilan data NO₂ dari satelit Sentinel-5P.

### 3. Koneksi ke Copernicus Data Space Ecosystem

```
connection = openeo.connect("openeo.dataspace.copernicus.eu").authenticate_oidc()
```

digunakan untuk membuat koneksi antara Python dan server OpenEO Copernicus Data Space Ecosystem. Saat baris kode ini dijalankan, sistem akan meminta proses autentikasi (login).

Proses autentikasi dilakukan dengan membuka tautan yang ditampilkan, kemudian pengguna diminta login menggunakan akun Copernicus yang telah dibuat sebelumnya. Setelah login berhasil, sistem akan memberikan konfirmasi seperti berikut:

```
Visit https://identity.dataspace.copernicus.eu/auth/realms/CDSE/device?user_code=OGME-UTEB 📋 to authenticate.
✅ Authorized successfully
Authenticated using device code flow.
```

Setelah autentikasi berhasil, objek connection akan aktif dan dapat digunakan untuk mengakses berbagai layanan OpenEO, seperti pengambilan data satelit Sentinel-5P, penentuan area penelitian, serta proses pengolahan data secara langsung di platform Copernicus Data Space Ecosystem. Tahap ini sangat penting karena seluruh proses analisis data hanya dapat dilakukan melalui koneksi yang valid ke server OpenEO.

### 4. Menentukan Area Penelitian dan Mengambil Data NO₂ Sentinel-5P

```
aoi = {
    "type": "Polygon",
    "coordinates": [
        [
            [113.16, -7.73],
            [113.25, -7.73],
            [113.25, -7.82],
            [113.16, -7.82],
            [113.16, -7.73]
        ]
    ]
}

s5post = connection.load_collection(
    "SENTINEL_5P_L2",
    temporal_extent=["2023-10-01", "2025-10-01"],
    spatial_extent={
        "west": 113.16,
        "south": -7.82,
        "east": 113.25,
        "north": -7.73
    },
    bands=["NO2"],
)

# Now aggregate by day to avoid having multiple data per day
s5p_no2_daily = s5post.aggregate_temporal_period(reducer="mean", period="day")

# Now create a spatial aggregation to generate mean timeseries data
s5p_no2_aoi = s5p_no2_daily.aggregate_spatial(reducer="mean", geometries=aoi)
```

digunakan untuk mengambil dan mengolah data konsentrasi Nitrogen Dioksida (NO₂) dari satelit Sentinel-5P pada wilayah penelitian yang telah ditentukan melalui Area of Interest (AOI) berbentuk poligon koordinat.

Sebelum proses pengambilan data dilakukan, terlebih dahulu ditentukan batas wilayah penelitian menggunakan koordinat geografis (longitude dan latitude). Koordinat ini dapat diperoleh melalui situs seperti geojson.io atau panduan pemetaan lainnya yang sesuai dengan kebutuhan penelitian.

Setelah AOI ditentukan, data NO₂ kemudian diambil dari koleksi SENTINEL_5P_L2 pada platform OpenEO dengan rentang waktu 1 Oktober 2023 hingga 1 Oktober 2025, serta hanya mengambil variabel NO₂.

Selanjutnya dilakukan dua tahap pengolahan data, yaitu:

Agregasi temporal (harian)
Data dirata-ratakan per hari menggunakan metode mean, sehingga menghindari adanya data ganda dalam satu hari.
Agregasi spasial (wilayah)
Data yang sudah dirata-ratakan per hari kemudian dihitung kembali nilai rata-rata seluruh area penelitian sehingga menghasilkan satu nilai representatif per hari.

Hasil akhir dari proses ini adalah data deret waktu (time series) harian konsentrasi NO₂ yang sudah bersih dan siap digunakan untuk analisis serta peramalan.

### 5. Mengekspor Data ke Format NetCDF

```
job = s5post.execute_batch(title="NO2 in Probolinggo", outputfile="NO2Probolinggo.nc")
```

digunakan untuk menjalankan proses pengolahan data NO₂ (Nitrogen Dioksida) secara batch processing pada platform OpenEO. Proses ini akan mengeksekusi seluruh alur pengambilan dan pengolahan data yang telah didefinisikan sebelumnya, kemudian menjalankannya di server Copernicus.

Hasil dari proses ini disimpan dalam bentuk file berformat NetCDF (.nc) dengan nama NO2Probolinggo.nc. File tersebut berisi data konsentrasi NO₂ di wilayah Probolinggo yang sudah melalui tahap pengolahan, sehingga dapat langsung digunakan untuk analisis lanjutan.

Selama proses berjalan, sistem akan menampilkan status pekerjaan (job) secara bertahap, mulai dari queued, running, hingga finished. Contoh output prosesnya adalah sebagai berikut:

Output:

```
0:00:00 Job 'j-2606030333364fa09d15b71a1f0056c1': send 'start'
0:00:05 Job 'j-2606030333364fa09d15b71a1f0056c1': queued (progress 0%)
0:00:10 Job 'j-2606030333364fa09d15b71a1f0056c1': queued (progress 0%)
0:00:16 Job 'j-2606030333364fa09d15b71a1f0056c1': queued (progress 0%)
0:00:25 Job 'j-2606030333364fa09d15b71a1f0056c1': queued (progress 0%)
0:00:35 Job 'j-2606030333364fa09d15b71a1f0056c1': running (progress N/A)
0:00:47 Job 'j-2606030333364fa09d15b71a1f0056c1': running (progress N/A)
0:01:03 Job 'j-2606030333364fa09d15b71a1f0056c1': running (progress N/A)
0:01:22 Job 'j-2606030333364fa09d15b71a1f0056c1': running (progress N/A)
0:01:46 Job 'j-2606030333364fa09d15b71a1f0056c1': running (progress N/A)
0:02:16 Job 'j-2606030333364fa09d15b71a1f0056c1': running (progress N/A)
0:02:54 Job 'j-2606030333364fa09d15b71a1f0056c1': running (progress N/A)
0:03:41 Job 'j-2606030333364fa09d15b71a1f0056c1': running (progress N/A)
0:04:39 Job 'j-2606030333364fa09d15b71a1f0056c1': running (progress N/A)
0:05:39 Job 'j-2606030333364fa09d15b71a1f0056c1': running (progress N/A)
0:06:40 Job 'j-2606030333364fa09d15b71a1f0056c1': finished (progress 100%)
```

Setelah status menunjukkan finished, maka proses telah selesai dan file hasil dapat diunduh atau digunakan untuk tahap analisis selanjutnya.

## B. Preprocessing Data

### 1. Instalasi Library netCDF4

```
!pip install netCDF4
```

Perintah ini digunakan untuk menginstal library netCDF4 pada Python. Library ini diperlukan untuk membaca, menulis, dan mengolah file berformat NetCDF (.nc) yang biasanya digunakan dalam data ilmiah, termasuk data hasil penginderaan jauh dari satelit. Dengan adanya library ini, file hasil pengolahan NO₂ dapat dibuka dan dianalisis langsung menggunakan Python.

### 2. Membaca dan Mengeksplorasi Data NetCDF (NO₂ Probolinggo)

```
import netCDF4

file_path = "NO2Probolinggo.nc"
ds = netCDF4.Dataset(file_path)

# Lihat seluruh variabel yang tersedia
print("📦 Variabel dalam file:")
print(ds.variables.keys())
# dict_keys(['t', 'x', 'y', 'crs', 'NO2'])

# Ambil NO2
no2 = ds.variables["NO2"][:]

# Ambil Time
time = ds.variables["t"][:]

# Konversi waktu ke format tanggal jika punya atribut 'units'
try:
    time_units = ds.variables["t"].units
    dates = netCDF4.num2date(time, units=time_units)
except Exception:
    dates = time  # fallback kalau tidak ada units

# Tampilkan struktur data NO2
print(type(no2))
# type <class 'numpy.ma.core.MaskedArray'>

print(len(no2))
# banyaknya data record NO2 725

print(len(no2[0]))
# panjang data perbaris 9

print(len(no2[0][0]))
# panjang perdata 8

print(no2[0][0][0])
# 3.7701793e-05
```

Kode ini digunakan untuk membuka dan membaca file hasil pengolahan data satelit NO₂ Probolinggo dalam format NetCDF (.nc) menggunakan library netCDF4.

Tahapan pertama adalah memuat file NO2Probolinggo.nc ke dalam objek dataset, sehingga seluruh isi file dapat diakses melalui Python. Setelah itu, dilakukan pengecekan variabel yang tersedia di dalam file untuk mengetahui struktur data yang disimpan, seperti t (waktu), x dan y (koordinat spasial), crs (system koordinat), serta NO2 (nilai konsentrasi nitrogen dioksida).

Selanjutnya, data NO₂ dan waktu diambil dari dataset untuk dianalisis lebih lanjut. Waktu kemudian dikonversi ke format tanggal menggunakan fungsi num2date, apabila informasi satuan waktu tersedia.

Dari hasil eksplorasi, diketahui bahwa data NO₂ memiliki tipe MaskedArray, yang berarti terdapat kemungkinan nilai yang hilang atau tidak valid pada beberapa titik data. Struktur data juga menunjukkan dimensi data yang cukup besar, sehingga perlu dipahami sebelum dilakukan analisis lanjutan.

Secara keseluruhan, tahap ini bertujuan untuk mengecek struktur, isi, dan karakteristik data, sehingga data siap digunakan pada proses preprocessing dan analisis berikutnya.

### 3. Interpolasi Missing Value Data NO₂

```
import numpy as np
import pandas as pd

# Interpolasi missing value
no2_filled = np.zeros_like(no2)
no2_filled = no2_filled.filled(0)

# Loop tiap grid (y,x)
for i in range(no2.shape[1]):     # 9 baris
    for j in range(no2.shape[2]): # 8 kolom
        series = pd.Series(no2[:, i, j])
        no2_filled[:, i, j] = series.interpolate(
            method='linear',
            limit_direction='both'
        ).to_numpy()
```

Kode di atas digunakan untuk melakukan preprocessing data, yaitu mengisi nilai yang hilang (missing value) pada data konsentrasi NO₂ Probolinggo.

Proses ini dilakukan dengan menggunakan metode interpolasi linear, yaitu teknik untuk memperkirakan nilai yang kosong berdasarkan nilai sebelum dan sesudahnya.

Langkah kerja kode tersebut adalah sebagai berikut:

Membuat array baru no2_filled sebagai wadah data hasil pengisian.
Setiap titik grid pada data (berdasarkan dimensi waktu, latitude, dan longitude) diproses satu per satu.
Setiap kombinasi koordinat (i, j) diubah menjadi deret waktu (time series) menggunakan pandas.Series.
Nilai yang hilang pada deret tersebut diisi menggunakan metode linear interpolation.
Hasilnya kemudian dikembalikan ke dalam bentuk array 3 dimensi.

Dengan proses ini, data menjadi lebih lengkap tanpa nilai kosong, sehingga lebih stabil dan siap digunakan untuk tahap analisis atau pemodelan selanjutnya.

### 4. Mengubah Format Tanggal dan Merata-ratakan Data NO₂

```
new_dates = []
new_no2 = []

for i in range(len(dates)):
    new_dates.append(dates[i].strftime('%Y-%m-%d'))
    new_no2.append(np.mean(no2_filled[i]))
```

Kode ini digunakan untuk melakukan transformasi dan peringkasan data (data aggregation) dari hasil pengolahan NO₂.

Proses yang dilakukan adalah sebagai berikut:

Variabel new_dates digunakan untuk menyimpan tanggal dalam format yang lebih sederhana (YYYY-MM-DD), sehingga lebih mudah dibaca dan digunakan dalam analisis.
Variabel new_no2 digunakan untuk menyimpan nilai rata-rata konsentrasi NO₂ pada setiap waktu.
Pada setiap iterasi, data satu hari dari no2_filled dihitung nilai rata-ratanya menggunakan np.mean(), sehingga data yang awalnya berbentuk grid spasial diringkas menjadi satu nilai representatif per hari.

Dengan hasil ini, data NO₂ berubah menjadi bentuk time series harian, yang lebih sederhana dan siap digunakan untuk tahap analisis lanjutan seperti visualisasi atau pemodelan.

### 5. Membuat DataFrame dan Menyimpan ke CSV

```
df = pd.DataFrame({
    "date": new_dates,
    "NO2": new_no2
})

# Tampilkan 5 data pertama
print(df.head())

# Simpan ke CSV
df.to_csv("NO2_Probolinggo_timeseries.csv", index=False)

print("File berhasil disimpan!")
```

Output:

```
         date       NO2
0  2023-10-01  0.000020
1  2023-10-02  0.000028
2  2023-10-03  0.000046
3  2023-10-04  0.000031
4  2023-10-05  0.000038
File berhasil disimpan!
```

Kode di atas digunakan untuk mengubah hasil pengolahan data NO₂ menjadi bentuk dataset akhir menggunakan library Pandas.

Langkah pertama adalah menggabungkan data tanggal (new_dates) dan nilai rata-rata NO₂ (new_no2) ke dalam sebuah DataFrame, sehingga data tersusun dalam format tabel yang lebih rapi dan mudah digunakan.

Setelah itu, dilakukan pengecekan hasil dengan menampilkan 5 data pertama menggunakan df.head() untuk memastikan data sudah sesuai.

Terakhir, data disimpan ke dalam file berformat CSV dengan nama NO2_Probolinggo_timeseries.csv. File ini dapat digunakan kembali untuk analisis lanjutan, seperti visualisasi, pemodelan, atau pengolahan di aplikasi lain seperti Excel maupun Python.

Secara keseluruhan, tahap ini berfungsi sebagai proses akhir untuk menyimpan hasil pengolahan data menjadi dataset siap pakai.

### 6. Mengecek Missing Date pada Data Time Series NO₂

```
import pandas as pd
import numpy as np

df = pd.read_csv("NO2_Probolinggo_timeseries.csv")

# Pastikan kolom 'date' bertipe datetime
df['date'] = pd.to_datetime(df['date'])

# Buat rentang tanggal lengkap
start_date = "2023-10-01"
end_date = "2025-09-30"
full_range = pd.date_range(start=start_date, end=end_date, freq='D')

# Cek tanggal yang hilang
missing_dates = full_range.difference(df['date'])

print(f"Jumlah hari missing: {len(missing_dates)}")
print("Daftar tanggal missing:")
print(missing_dates)
```

Output:

```
Jumlah hari missing: 7
Daftar tanggal missing:
DatetimeIndex(['2023-11-11', '2024-01-01', '2024-03-11', '2024-03-23',
               '2024-08-12', '2025-01-30', '2025-01-31'],
              dtype='datetime64[ns]', freq=None)
```

Kode di atas digunakan untuk melakukan validasi kelengkapan data time series NO₂ yang telah disimpan sebelumnya dalam bentuk file CSV.

Langkah pertama adalah membaca dataset menggunakan pandas dan memastikan kolom date sudah dalam format datetime, sehingga dapat diperlakukan sebagai data waktu.

Selanjutnya, dibuat rentang tanggal lengkap dari 1 Oktober 2023 hingga 30 September 2025 dengan interval harian. Rentang ini merepresentasikan data ideal tanpa adanya kekosongan.

Kemudian, sistem membandingkan rentang tanggal lengkap tersebut dengan tanggal yang ada di dataset untuk mengidentifikasi missing date atau tanggal yang tidak memiliki data NO₂.

Hasil output menunjukkan bahwa terdapat 7 hari yang tidak memiliki data, beserta daftar tanggalnya.

Secara keseluruhan, tahap ini berfungsi untuk memastikan kualitas dan kelengkapan data sebelum digunakan pada proses analisis atau pemodelan lebih lanjut.

### 7. Menangani Missing Date dan Interpolasi Time Series NO₂

```
import pandas as pd

# Pastikan datetime dan sorting
df['date'] = pd.to_datetime(df['date'])
df = df.sort_values('date')

# Buat rentang tanggal lengkap
full_range = pd.date_range(start="2023-10-01", end="2025-09-30", freq='D')

# Reindex agar tanggal yang hilang muncul sebagai NaN
df = df.set_index('date').reindex(full_range)
df.index.name = 'date'

# Interpolasi linear berdasarkan waktu
df['NO2'] = df['NO2'].interpolate(method='time')

# Mengisi nilai kosong di awal/akhir data
df['NO2'] = df['NO2'].fillna(method='bfill').fillna(method='ffill')

# Simpan kembali ke CSV
df.to_csv("no2_timeseries_interpolated.csv")
```

Kode di atas digunakan untuk melakukan perbaikan dan penyempurnaan data time series NO₂ yang masih memiliki tanggal hilang (missing date).

Tahap pertama adalah memastikan data sudah dalam format datetime dan diurutkan berdasarkan tanggal agar urutan waktu tidak acak.

Selanjutnya dibuat rentang tanggal lengkap dari 1 Oktober 2023 hingga 30 September 2025 dengan interval harian. Rentang ini digunakan sebagai acuan data yang seharusnya lengkap tanpa ada hari yang terlewat.

Kemudian dilakukan proses reindexing, yaitu menyamakan dataset dengan rentang tanggal tersebut. Hasilnya, tanggal yang sebelumnya tidak ada akan muncul sebagai nilai kosong (NaN).

Nilai kosong ini kemudian diperbaiki menggunakan interpolasi linear berbasis waktu (interpolate(method='time')), sehingga nilai yang hilang diisi berdasarkan pola data di sekitarnya.

Jika masih terdapat nilai kosong di bagian awal atau akhir data, dilakukan pengisian tambahan menggunakan backward fill dan forward fill agar tidak ada nilai yang tersisa kosong.

Terakhir, data yang sudah lengkap dan bersih disimpan kembali ke file CSV baru bernama no2_timeseries_interpolated.csv untuk digunakan pada tahap analisis atau pemodelan selanjutnya.

### 8. Mengecek Ulang Missing Date Setelah Interpolasi

```
import pandas as pd
import numpy as np

df = pd.read_csv("no2_timeseries_interpolated.csv")

# Pastikan kolom 'date' bertipe datetime
df['date'] = pd.to_datetime(df['date'])

# Buat rentang tanggal lengkap
start_date = "2023-10-01"
end_date = "2025-09-30"
full_range = pd.date_range(start=start_date, end=end_date, freq='D')

# Cek tanggal yang hilang
missing_dates = full_range.difference(df['date'])

print(f"Jumlah hari missing: {len(missing_dates)}")
print("Daftar tanggal missing:")
print(missing_dates)
```

Output:

```
Jumlah hari missing: 0
Daftar tanggal missing:
DatetimeIndex([], dtype='datetime64[ns]', freq='D')
```

Kode di atas digunakan untuk melakukan validasi akhir terhadap kelengkapan data time series NO₂ setelah proses interpolasi selesai dilakukan.

Pada tahap ini, data hasil interpolasi dibaca kembali, kemudian kolom date dipastikan sudah dalam format datetime agar dapat diproses sebagai data waktu.

Selanjutnya dibuat kembali rentang tanggal lengkap dari 1 Oktober 2023 hingga 30 September 2025 dengan interval harian. Rentang ini digunakan sebagai acuan untuk mengecek apakah seluruh hari sudah terisi data.

Kemudian dilakukan perbandingan antara rentang tanggal lengkap dengan tanggal yang tersedia di dataset untuk mendeteksi missing date.

Hasil output menunjukkan bahwa jumlah hari yang hilang adalah 0, yang berarti seluruh tanggal dalam rentang waktu penelitian sudah terisi dengan baik.

Secara keseluruhan, tahap ini menegaskan bahwa data sudah lengkap, konsisten, dan siap digunakan untuk analisis atau pemodelan lebih lanjut.

### 9. Deteksi Outlier Data NO₂ Menggunakan Metode IQR

```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

df = pd.read_csv("no2_timeseries_interpolated.csv")

df['date'] = pd.to_datetime(df['date'])

# Hitung IQR
Q1 = df['NO2'].quantile(0.25)
Q3 = df['NO2'].quantile(0.75)
IQR = Q3 - Q1

lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR

# Filter outlier
outliers_iqr = df[(df['NO2'] < lower_bound) | (df['NO2'] > upper_bound)]

print("Jumlah Outlier (IQR):", len(outliers_iqr))
print(outliers_iqr[['date', 'NO2']].head())
```

Output:

```
Jumlah Outlier (IQR): 13
          date       NO2
38  2023-11-08  0.000062
239 2024-05-27  0.000072
255 2024-06-12  0.000071
278 2024-07-05  0.000069
282 2024-07-09  0.000081
```

Kode di atas digunakan untuk melakukan deteksi outlier pada data konsentrasi NO₂ menggunakan metode Interquartile Range (IQR).

Tahap pertama adalah membaca dataset dan memastikan kolom date sudah dalam format datetime agar konsisten dalam analisis waktu.

Selanjutnya dilakukan perhitungan statistik dasar, yaitu:

Q1 (kuartil pertama) sebagai batas 25% data terbawah
Q3 (kuartil ketiga) sebagai batas 75% data teratas
IQR (Interquartile Range) yang merupakan selisih antara Q3 dan Q1

Berdasarkan nilai IQR tersebut, ditentukan batas normal data:

Batas bawah = Q1 − 1.5 × IQR
Batas atas = Q3 + 1.5 × IQR

Data yang berada di luar rentang tersebut dianggap sebagai outlier karena memiliki nilai yang terlalu ekstrem dibandingkan data lainnya.

Hasil output menunjukkan bahwa terdapat 13 data outlier, beserta beberapa contoh tanggal dan nilai NO₂ yang terdeteksi.

Secara keseluruhan, tahap ini bertujuan untuk mengidentifikasi anomali dalam data sebelum digunakan pada proses analisis atau pemodelan lebih lanjut.

### 10. Visualisasi Deteksi Outlier Data NO₂ (Metode IQR)

```
# === Visualisasi ===
plt.figure(figsize=(15,5))
plt.plot(df['date'], df['NO2'], label="NO2", linewidth=1)

# Titik Outlier
plt.scatter(outliers_iqr['date'], outliers_iqr['NO2'], 
            color='red', marker='o', label="Outliers")

# Garis batas atas & bawah
plt.axhline(upper_bound, color='orange', linestyle='dashed', label="Upper Bound (IQR)")
plt.axhline(lower_bound, color='blue', linestyle='dashed', label="Lower Bound (IQR)")

plt.title("Deteksi Outlier Data NO2 (Metode IQR)")
plt.xlabel("Tanggal")
plt.ylabel("Kadar NO2")
plt.legend()
plt.tight_layout()

plt.xticks(
    ticks=[df['date'].iloc[0], df['date'].iloc[-1]],
    labels=[df['date'].iloc[0].strftime('%Y-%m-%d'),
            df['date'].iloc[-1].strftime('%Y-%m-%d')]
)

plt.show()
```

![original image](https://cdn.mathpix.com/snip/images/08_NCZJRhYQx7NP5rWWC3fcoa1mRvzZCEq5EsPwPKY0.original.fullsize.png)

### 11. Membersihkan Outlier dan Mengisi Kembali Data NO₂

```
# Tandai outlier menjadi NaN
df['NO2_cleaned'] = df['NO2'].mask((df['NO2'] < lower_bound) | (df['NO2'] > upper_bound))

print("Jumlah nilai yang dinyatakan sebagai outlier:", df['NO2_cleaned'].isna().sum())

# Interpolasi linear untuk mengisi kembali nilai outlier
df['NO2_filled'] = df['NO2_cleaned'].interpolate(method='linear')

# Jika masih tersisa NaN di ujung data, isi dengan forward/backward fill
df['NO2_filled'] = df['NO2_filled'].bfill().ffill()

print("Jumlah missing setelah interpolasi:", df['NO2_filled'].isna().sum())
```

Kode ini digunakan untuk membersihkan data NO₂ dari outlier serta memperbaiki nilai yang tidak wajar agar data lebih stabil dan siap dianalisis lebih lanjut.

### 12. Visualisasi Data NO₂ Setelah Cleaning (Outlier Removal \& Interpolasi)

```
plt.figure(figsize=(15,5))

# Plot data hasil interpolasi
plt.plot(df['date'], df['NO2_filled'], label="NO2 (Interpolated)", linewidth=1)

# Tampilkan hanya tanggal awal dan akhir di sumbu X
plt.xticks(
    ticks=[df['date'].iloc[0], df['date'].iloc[-1]],
    labels=[df['date'].iloc[0].strftime('%Y-%m-%d'),
            df['date'].iloc[-1].strftime('%Y-%m-%d')]
)

plt.title("Plot Data NO2 Setelah Outlier Removal & Interpolasi")
plt.xlabel("Tanggal")
plt.ylabel("Kadar NO2")
plt.legend()
plt.tight_layout()
plt.show()
```

![original image](https://cdn.mathpix.com/snip/images/3_NQD0XxyDB0_tUz6mQNUndMvkLTq9HoOsNkp48i_R8.original.fullsize.png)

