# MINIPROJECT : DETEKSI TANDA TANGAN

**Nama:** Gita Ceasar Rani  
**NIM:** F1G124008 
**Kelas:** C
**Mata Kuliah:** Pengolahan Citra Digital  

## TUGAS 6 : DETEKSI TANDA TANGAN (Signature Detection)

## Alur Pemrosesan (Pipeline)

### 1. Region of Interest (ROI) Cropping

Tahap pertama dilakukan dengan mengambil bagian tertentu dari dokumen yang diperkirakan sebagai lokasi tanda tangan kepala sekolah.

Pada project ini, area yang digunakan berada di bagian kanan atas dokumen. Pemotongan dilakukan berdasarkan persentase ukuran gambar, yaitu **10%-35% dari tinggi gambar** dan **65%-95% dari lebar gambar**.

Pendekatan ini digunakan agar area yang diproses dapat menyesuaikan ukuran citra.

### 2. Grayscale Conversion

Citra hasil crop kemudian diubah menjadi grayscale atau citra keabuan.

Tahap ini dilakukan untuk menyederhanakan citra menjadi satu kanal intensitas sehingga proses pemisahan antara foreground dan background dapat dilakukan dengan lebih mudah.

### 3. Thresholding

Pada proses segmentasi digunakan dua metode thresholding, yaitu **Global Threshold** dan **Otsu Threshold**.

Global Threshold menggunakan nilai threshold tetap sebesar **127**. Sedangkan Otsu menentukan nilai threshold secara otomatis berdasarkan distribusi intensitas piksel pada citra.

Hasil thresholding digunakan untuk memisahkan bagian foreground dari background.

### 4. Morphological Operations

Setelah thresholding, dilakukan proses morphology untuk memperbaiki hasil segmentasi.

Operasi yang digunakan adalah:

- **Opening**, untuk membantu mengurangi noise dan objek kecil yang tidak diperlukan.
- **Closing**, untuk membantu menutup celah kecil dan menghubungkan bagian foreground yang terputus.

Kedua operasi menggunakan kernel berukuran **3×3 piksel**.

### 5. Pixel Ratio Calculation

Jumlah foreground pixel dihitung menggunakan `cv2.countNonZero()`.

Kemudian dihitung persentase foreground terhadap seluruh piksel pada area ROI dengan rumus:

```text
Foreground (%) =
Jumlah Foreground Pixel / Jumlah Seluruh Pixel × 100%
## Hasil Pengujian

Sistem diuji menggunakan 9 citra ijazah dengan ukuran **2481 × 3506 piksel**.

Setiap citra memiliki kondisi kualitas yang berbeda, seperti blur, noise, kontras rendah, perubahan warna, resolusi rendah, dan artefak kompresi.

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

Berdasarkan hasil pengujian, seluruh 9 citra menghasilkan status **SIGNATURE PRESENT**. Nilai rasio piksel berada pada rentang **7.16% hingga 11.69%**, sehingga seluruh hasil berada di atas batas keputusan **1.5%**.

---

## Analisis

### 1. Mengapa Thresholding Diperlukan?

Thresholding diperlukan untuk mengubah citra grayscale menjadi citra biner sehingga foreground dan background dapat dipisahkan.

Dengan hasil segmentasi tersebut, sistem dapat menghitung jumlah piksel yang dianggap sebagai bagian dari objek tanda tangan. Nilai tersebut kemudian digunakan untuk menentukan keberadaan tanda tangan.

### 2. Apa yang Terjadi Jika Threshold Terlalu Rendah atau Terlalu Tinggi?

Jika threshold terlalu rendah, sebagian objek tanda tangan dapat tidak masuk ke dalam foreground. Hal ini dapat menyebabkan jumlah piksel tanda tangan menjadi lebih sedikit.

Sebaliknya, jika threshold terlalu tinggi, bagian background, noise, atau pola pada dokumen dapat ikut dianggap sebagai foreground. Akibatnya, jumlah piksel dapat menjadi lebih besar dari kondisi sebenarnya.

Untuk mengatasi perbedaan kondisi citra, project ini menggunakan **Global Threshold** dan **Otsu Threshold**. Hasil segmentasi kemudian diperbaiki menggunakan morphology **Opening** dan **Closing**.

---

## Kesimpulan

Berdasarkan hasil pengujian terhadap 9 citra, seluruh citra menghasilkan status **SIGNATURE PRESENT**.

Rasio foreground yang diperoleh berada pada rentang **7.16% sampai 11.69%**, sedangkan batas keputusan yang digunakan adalah **1.5%**.

Proses deteksi dilakukan melalui beberapa tahap, yaitu **ROI Cropping, Grayscale, Thresholding, Morphological Operations**, dan **Pixel Ratio Calculation**.

Hasil pengujian menunjukkan bahwa metode tersebut dapat digunakan untuk melakukan segmentasi dan mendeteksi keberadaan tanda tangan pada citra dokumen berdasarkan jumlah foreground pixel.

> **Catatan:** Pengujian yang dilakukan pada dataset ini baru menggunakan citra yang menghasilkan kondisi `SIGNATURE PRESENT`. Pengujian terhadap citra yang benar-benar tidak memiliki tanda tangan diperlukan untuk mengevaluasi kondisi `SIGNATURE ABSENT`.
