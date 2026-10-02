# MINIPROJECT : DETEKSI TANDA TANGAN

**Nama:** Gita Ceasar Rani  
**NIM:** F1G124008  
**Kelas:** C  
**Mata Kuliah:** Pengolahan Citra Digital  

## TUGAS 6 : DETEKSI TANDA TANGAN (Signature Detection)

Project ini bertujuan untuk mendeteksi keberadaan tanda tangan pada dokumen ijazah menggunakan teknik pengolahan citra digital. Proses deteksi dilakukan dengan mengambil area tertentu pada dokumen, melakukan segmentasi citra, membersihkan hasil segmentasi, kemudian menghitung jumlah piksel foreground sebagai dasar pengambilan keputusan.

## Alur Pemrosesan (Pipeline)

### 1. Region of Interest (ROI) Cropping

Tahap pertama adalah menentukan **Region of Interest (ROI)**, yaitu area tertentu pada dokumen yang diperkirakan sebagai lokasi tanda tangan kepala sekolah.

Pada project ini, ROI berada pada bagian kanan atas dokumen. Pemotongan dilakukan berdasarkan persentase ukuran gambar, yaitu:

- **10%–35% dari tinggi gambar**
- **65%–95% dari lebar gambar**

Pendekatan berdasarkan persentase digunakan agar area ROI dapat menyesuaikan ukuran citra yang digunakan.

### 2. Grayscale Conversion

Citra hasil crop kemudian diubah menjadi **grayscale** atau citra keabuan.

Grayscale digunakan untuk mengubah citra yang sebelumnya memiliki beberapa kanal warna menjadi satu kanal intensitas. Dengan demikian, proses thresholding dan pemisahan antara foreground dan background dapat dilakukan dengan lebih sederhana.

### 3. Thresholding

Tahap berikutnya adalah melakukan thresholding untuk memisahkan bagian foreground dan background.

Pada project ini digunakan dua metode thresholding, yaitu:

- **Global Threshold**
- **Otsu Threshold**

Global Threshold menggunakan nilai threshold tetap sebesar **127**.

Sementara itu, **Otsu Threshold** menentukan nilai threshold secara otomatis berdasarkan distribusi intensitas piksel pada citra.

Hasil thresholding berupa citra biner yang kemudian digunakan sebagai dasar untuk proses selanjutnya.

### 4. Morphological Operations

Setelah proses thresholding, dilakukan operasi morfologi untuk memperbaiki hasil segmentasi.

Operasi morfologi yang digunakan adalah:

- **Opening**, digunakan untuk membantu mengurangi noise dan objek kecil yang tidak diperlukan.
- **Closing**, digunakan untuk membantu menutup celah kecil serta menghubungkan bagian foreground yang terputus.

Kedua operasi tersebut menggunakan **kernel berukuran 3 × 3 piksel**.

### 5. Pixel Ratio Calculation

Setelah proses morphology, jumlah foreground pixel dihitung menggunakan fungsi `cv2.countNonZero()`.

Persentase foreground terhadap seluruh piksel pada area ROI dihitung menggunakan rumus:

**Foreground (%) = (Jumlah Foreground Pixel / Jumlah Seluruh Pixel) × 100%**

Sistem menggunakan batas keputusan sebesar **1.5%**.

- Jika rasio foreground **> 1.5%** → **SIGNATURE PRESENT**
- Jika rasio foreground **≤ 1.5%** → **SIGNATURE ABSENT**

Nilai 1.5% digunakan sebagai batas keputusan awal pada project dan perlu dievaluasi kembali apabila digunakan pada dataset yang lebih beragam.

## Hasil Pengujian

Pengujian dilakukan terhadap **9 citra ijazah** dengan ukuran **2481 × 3506 piksel**.

Setiap citra memiliki kondisi kualitas yang berbeda, seperti kontras rendah, blur, noise, perubahan warna, resolusi rendah, dan artefak kompresi.

| No | Nama File | Jumlah Piksel TTD | Rasio Piksel | Status |
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

Setelah proses tersebut, jumlah foreground pixel dapat dihitung dan digunakan sebagai salah satu dasar untuk menentukan apakah tanda tangan terdeteksi atau tidak.

### 2. Apa yang Terjadi Jika Threshold Tidak Sesuai?

Nilai threshold yang tidak sesuai dapat memengaruhi hasil segmentasi.

Jika threshold kurang sesuai, sebagian goresan tanda tangan dapat tidak terdeteksi sehingga jumlah foreground pixel menjadi terlalu sedikit. Sebaliknya, hasil thresholding juga dapat memasukkan bagian background atau noise sebagai foreground sehingga jumlah foreground pixel menjadi lebih besar dari kondisi sebenarnya.

Untuk mengatasi perbedaan kondisi citra, project ini menggunakan dua pendekatan thresholding, yaitu **Global Threshold** dan **Otsu Threshold**.

Hasil segmentasi kemudian diperbaiki menggunakan operasi **Opening** dan **Closing** untuk mengurangi noise serta memperbaiki bagian foreground yang terputus.

### 3. Perbandingan Global Threshold dan Otsu Threshold

**Global Threshold** menggunakan nilai threshold yang telah ditentukan, yaitu 127. Metode ini sederhana dan mudah diterapkan, tetapi hasilnya dapat dipengaruhi oleh kondisi pencahayaan dan kualitas citra.

**Otsu Threshold** menentukan nilai threshold secara otomatis berdasarkan distribusi intensitas piksel. Metode ini lebih adaptif terhadap perbedaan distribusi intensitas pada citra.

Dalam project ini, kedua metode digunakan untuk melakukan proses segmentasi sebelum hasilnya diperbaiki menggunakan operasi morfologi.

## Kesimpulan

Berdasarkan pengujian terhadap **9 citra ijazah**, seluruh citra menghasilkan status **SIGNATURE PRESENT**.

Rasio foreground yang diperoleh berada pada rentang **7.16% sampai 11.69%**, sedangkan batas keputusan yang digunakan adalah **1.5%**.

Proses deteksi dilakukan melalui beberapa tahap, yaitu:

1. **ROI Cropping**
2. **Grayscale Conversion**
3. **Thresholding**
4. **Morphological Operations**
5. **Pixel Ratio Calculation**
6. **Pengambilan Keputusan**

Metode tersebut digunakan untuk melakukan segmentasi area tanda tangan dan menentukan keberadaan tanda tangan berdasarkan jumlah foreground pixel pada ROI.

Namun, pengujian pada dataset ini baru menggunakan citra yang memiliki tanda tangan. Oleh karena itu, pengujian menggunakan citra yang benar-benar **tidak memiliki tanda tangan** masih diperlukan untuk mengevaluasi hasil **SIGNATURE ABSENT** dan mengetahui kemampuan sistem dalam membedakan kedua kondisi tersebut.
