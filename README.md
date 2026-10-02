# MINIPROJECT : DETEKSI TANDA TANGAN

**Nama:** Gita Ceasar Rani  
**NIM:** F1G124008  
**Kelas:** C  
**Mata Kuliah:** Pengolahan Citra Digital  

## TUGAS 6 : DETEKSI TANDA TANGAN (Signature Detection)

Project ini bertujuan untuk mendeteksi keberadaan tanda tangan pada dokumen ijazah menggunakan teknik pengolahan citra digital. Proses dilakukan dengan mengambil area tertentu pada dokumen, mengubah citra menjadi grayscale, melakukan thresholding, memperbaiki hasil segmentasi menggunakan operasi morfologi, kemudian menghitung jumlah foreground pixel sebagai dasar pengambilan keputusan.

## Alur Pemrosesan (Pipeline)

### 1. Region of Interest (ROI) Cropping

Tahap pertama adalah menentukan **Region of Interest (ROI)**, yaitu area tertentu pada dokumen yang diperkirakan sebagai lokasi tanda tangan kepala sekolah.

Pada project ini, area ROI ditentukan berdasarkan persentase ukuran gambar, yaitu:

- **10%–35% dari tinggi gambar**
- **65%–95% dari lebar gambar**

Area tersebut digunakan sebagai bagian citra yang akan diproses lebih lanjut.

Pendekatan berdasarkan persentase digunakan agar pemotongan ROI dapat menyesuaikan ukuran citra yang digunakan.

### 2. Grayscale Conversion

Citra hasil crop kemudian diubah menjadi **grayscale** atau citra keabuan.

Grayscale digunakan untuk mengubah citra yang memiliki beberapa kanal warna menjadi satu kanal intensitas. Dengan demikian, proses thresholding dapat dilakukan dengan lebih sederhana untuk memisahkan foreground dan background.

### 3. Thresholding

Tahap berikutnya adalah melakukan thresholding untuk memisahkan bagian foreground dan background pada citra grayscale.

Pada project ini digunakan dua metode thresholding, yaitu:

- **Global Threshold**
- **Otsu Threshold**

#### Global Threshold

Global Threshold menggunakan nilai threshold tetap sebesar **127**.

Metode ini menggunakan satu nilai threshold yang sama untuk seluruh piksel pada citra.

#### Otsu Threshold

Otsu Threshold menentukan nilai threshold secara otomatis berdasarkan distribusi intensitas piksel pada citra. Dengan demikian, nilai threshold tidak perlu ditentukan secara manual.

Pada proses akhir deteksi, hasil **Otsu Threshold** digunakan sebagai dasar untuk proses morphological operations dan perhitungan foreground pixel.

### 4. Morphological Operations

Setelah proses Otsu Threshold, dilakukan operasi morfologi untuk membantu memperbaiki hasil segmentasi.

Operasi morfologi yang digunakan adalah:

- **Opening**, untuk membantu mengurangi noise dan objek kecil yang tidak diperlukan.
- **Closing**, untuk membantu menutup celah kecil serta menghubungkan bagian foreground yang terputus.

Kedua operasi tersebut menggunakan **kernel berukuran 3 × 3 piksel**.

Urutan prosesnya adalah:

**Otsu Threshold → Opening → Closing**

### 5. Pixel Ratio Calculation

Setelah proses morphology, jumlah foreground pixel dihitung menggunakan fungsi `cv2.countNonZero()`.

Persentase foreground terhadap seluruh piksel pada area ROI dihitung menggunakan rumus:

**Foreground (%) = (Jumlah Foreground Pixel / Jumlah Seluruh Pixel) × 100%**

Sistem menggunakan batas keputusan sebesar **1.5%**.

- Jika rasio foreground **> 1.5%** → **SIGNATURE PRESENT**
- Jika rasio foreground **≤ 1.5%** → **SIGNATURE ABSENT**

Nilai **1.5%** digunakan sebagai batas keputusan awal pada project ini. Nilai tersebut masih dapat dievaluasi kembali apabila sistem diuji menggunakan dataset yang lebih beragam.

## Hasil Pengujian

Pengujian dilakukan terhadap **9 citra ijazah** dengan ukuran **2481 × 3506 piksel**.

Setiap citra memiliki kondisi kualitas yang berbeda, seperti kontras rendah, blur, noise, perubahan warna, resolusi rendah, dan artefak kompresi.

| No | Nama File | Jumlah Foreground Pixel | Rasio Foreground | Status |
|---|---|---:|---:|---|
| 1 | `01_HighQuality_Enhanced.jpg` | 48.376 | 7.41% | SIGNATURE PRESENT |
| 2 | `02_LowContrast.jpg` | 48.933 | 7.50% | SIGNATURE PRESENT |
| 3 | `03_Blurred.jpg` | 76.256 | 11.69% | SIGNATURE PRESENT |
| 4 | `04_HighNoise.jpg` | 46.717 | 7.16% | SIGNATURE PRESENT |
| 5 | `05_LowResolution_Upsampled.jpg` | 60.048 | 9.20% | SIGNATURE PRESENT |
| 6 | `06_Faded_Underexposed.jpg` | 49.777 | 7.63% | SIGNATURE PRESENT |
| 7 | `07_ColorShift_WarmTint.jpg` | 49.062 | 7.52% | SIGNATURE PRESENT |
| 8 | `08_JPEGCompression_Artifacts.jpg` | 50.344 | 7.72% | SIGNATURE PRESENT |
| 9 | `09_CombinedDegradation.jpg` | 57.502 | 8.81% | SIGNATURE PRESENT |

Berdasarkan hasil pengujian, seluruh **9 citra** menghasilkan status **SIGNATURE PRESENT**.

Nilai rasio foreground berada pada rentang **7.16% sampai 11.69%**. Seluruh nilai tersebut berada di atas batas keputusan yang digunakan, yaitu **1.5%**.

## Analisis

### 1. Mengapa Thresholding Diperlukan?

Thresholding diperlukan untuk mengubah citra grayscale menjadi citra biner sehingga bagian foreground dan background dapat dipisahkan.

Dalam project ini, hasil thresholding digunakan untuk memisahkan bagian yang dianggap sebagai objek atau goresan tanda tangan dari background dokumen.

Setelah proses segmentasi, jumlah foreground pixel dapat dihitung menggunakan `cv2.countNonZero()`. Nilai tersebut kemudian digunakan sebagai dasar untuk menentukan keberadaan tanda tangan.

### 2. Apa yang Terjadi Jika Threshold Tidak Sesuai?

Nilai threshold yang tidak sesuai dapat memengaruhi hasil segmentasi.

Jika threshold tidak sesuai, sebagian goresan tanda tangan dapat tidak terdeteksi sehingga jumlah foreground pixel menjadi terlalu sedikit. Sebaliknya, bagian background atau noise juga dapat ikut dianggap sebagai foreground sehingga jumlah foreground pixel menjadi lebih besar dari kondisi sebenarnya.

Hal tersebut dapat memengaruhi hasil akhir deteksi.

Pada project ini digunakan **Global Threshold** dan **Otsu Threshold** untuk melihat pendekatan segmentasi yang berbeda. Untuk proses deteksi akhir digunakan hasil Otsu yang kemudian diperbaiki dengan operasi Opening dan Closing.

### 3. Perbandingan Global Threshold dan Otsu Threshold

**Global Threshold** menggunakan nilai threshold yang telah ditentukan, yaitu **127**. Metode ini sederhana dan mudah diterapkan, tetapi hasil segmentasinya dapat dipengaruhi oleh kondisi pencahayaan dan distribusi intensitas pada citra.

**Otsu Threshold** menentukan nilai threshold secara otomatis berdasarkan distribusi intensitas piksel pada citra. Metode ini tidak memerlukan penentuan nilai threshold secara manual.

Kedua metode digunakan pada tahap thresholding untuk melihat hasil pemisahan foreground dan background. Pada proses akhir deteksi, hasil Otsu digunakan untuk tahap morphological operations dan perhitungan foreground pixel.

### 4. Fungsi Morphological Operations

Operasi morphology digunakan untuk membantu memperbaiki hasil segmentasi setelah thresholding.

**Opening** membantu mengurangi noise dan objek kecil yang tidak diperlukan.

**Closing** membantu menutup celah kecil serta menghubungkan bagian foreground yang terputus.

Dengan menggunakan kedua operasi tersebut, hasil segmentasi dapat menjadi lebih bersih sebelum dilakukan perhitungan foreground pixel.

### 5. Dasar Pengambilan Keputusan

Setelah proses morphology selesai, jumlah foreground pixel dihitung menggunakan `cv2.countNonZero()`.

Jumlah tersebut kemudian dibandingkan dengan seluruh piksel pada ROI untuk mendapatkan rasio foreground.

Batas keputusan yang digunakan adalah **1.5%**.

Jika nilai rasio lebih dari 1.5%, sistem memberikan hasil **SIGNATURE PRESENT**. Jika nilai rasio sama dengan atau kurang dari 1.5%, sistem memberikan hasil **SIGNATURE ABSENT**.

Batas 1.5% merupakan nilai keputusan awal yang digunakan pada project ini dan dapat disesuaikan apabila tersedia dataset pengujian yang lebih lengkap.

## Kesimpulan

Berdasarkan pengujian terhadap **9 citra ijazah**, seluruh citra menghasilkan status **SIGNATURE PRESENT**.

Rasio foreground yang diperoleh berada pada rentang **7.16% sampai 11.69%**, sedangkan batas keputusan yang digunakan adalah **1.5%**.

Proses deteksi dilakukan melalui beberapa tahap, yaitu:

1. **ROI Cropping**
2. **Grayscale Conversion**
3. **Global Threshold dan Otsu Threshold**
4. **Morphological Operations**
5. **Pixel Ratio Calculation**
6. **Pengambilan Keputusan**

Pada proses deteksi akhir, hasil **Otsu Threshold** digunakan untuk proses **Opening dan Closing**, kemudian jumlah foreground pixel dihitung untuk memperoleh rasio foreground.

Hasil pengujian menunjukkan bahwa seluruh citra yang digunakan menghasilkan status **SIGNATURE PRESENT** berdasarkan batas keputusan yang telah ditentukan.

Namun, pengujian pada dataset ini baru menggunakan citra yang memiliki tanda tangan. Oleh karena itu, pengujian menggunakan citra yang benar-benar **tidak memiliki tanda tangan** masih diperlukan untuk mengevaluasi hasil **SIGNATURE ABSENT** dan mengetahui kemampuan sistem dalam membedakan kedua kondisi tersebut.
