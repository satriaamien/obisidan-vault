---
name: test-case-classification
description: Standar klasifikasi Test Type (Positive/Negative) dan penentuan Priority (Critical, High, Medium, Low) pada setiap skenario test case QA.
---
---
name: test-case-classification
description: Standar klasifikasi Test Type (Positive/Negative) dan penentuan Priority (Critical, High, Medium, Low) pada setiap skenario test case QA.
---

# Standar Klasifikasi & Prioritas Test Case

Setiap baris *test case* yang dihasilkan wajib memiliki klasifikasi berikut:

## 1. Test Type (Tipe Pengujian)
- **Positive:** Menguji alur normal (*happy path*) menggunakan data valid untuk memastikan sistem bekerja sesuai spesifikasi[span_2](start_span)[span_2](end_span).
- **Negative:** Menguji validasi error, nilai di luar batas (*out-of-bounds*), input ekstrem, atau alur ilegal untuk memastikan sistem menolak dengan aman tanpa *crash*[span_3](start_span)[span_3](end_span).

## 2. Priority (Tingkat Prioritas Eksekusi)
Gunakan kriteria berikut untuk menentukan urutan prioritas saat waktu *regression testing* terbatas[span_4](start_span)[span_4](end_span):
- **Critical (P0):** Alur bisnis utama (*core flow*), autentikasi/keamanan, transaksi finansial, atau potensi kehilangan/kerusakan data.
- **High (P1):** Fitur penting yang sering digunakan pengguna dan validasi bisnis utama (tidak ada *workaround* mudah jika gagal).
- **Medium (P2):** Fitur pendukung, validasi batas minor, atau skenario kombinasi lanjutan.
- **Low (P3):** Masalah kosmetik/UI minor atau skenario *edge-case* yang sangat jarang terjadi.
