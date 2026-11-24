​~/.hermes/skills/qa-methodology/SKILL.md


cat << 'EOF' > ~/.hermes/skills/qa-methodology/SKILL.md
---
---
name: qa-methodology
description: Standar lengkap perancangan test case QA berbasis ISTQB (EP, BVA, Decision Table, State Transition, Pairwise, Error Guessing), dilengkapi audit Gap Analysis, strategi Test Pyramid & 4-Layer Test Design, prinsip Atomic, prosedur Decision Table, serta klasifikasi Test Type dan Priority.
---

# Panduan Seleksi Metode & Penyusunan Test Case QA

## 1. Matriks Pemilihan Metode Testing
Gunakan panduan ini untuk menentukan metode paling akurat pada setiap komponen fitur:

| Metode Testing | Kapan Tepat Digunakan | Kapan Jangan Digunakan | Fokus Pengujian |
| :--- | :--- | :--- | :--- |
| **Equivalence Partitioning (EP)** | Input terbagi dalam kelompok/kategori yang diperlakukan sama oleh sistem (misal: tipe member, kode wilayah, rentang non-batas). | Jika fokusnya adalah titik ujung batas minimum/maksimum. | 1 sampel perwakilan dari tiap partisi valid & tidak valid. |
| **Boundary Value Analysis (BVA)** | Terdapat batasan angka, panjang teks, jumlah item, atau kuota yang jelas (`min` / `max`). | Input berupa pilihan kategori tanpa urutan atau batas ukuran. | Nilai tepi: `min-1`, `min`, `max`, `max+1`. |
| **Decision Table** | Aturan bisnis kompleks dengan beberapa kondisi (`True/False`) yang menghasilkan output berbeda (ideal untuk 2–4 kondisi biner). | Menguji urutan langkah (*flow*), atau jika jumlah kondisi terlalu banyak (misal 10 kondisi = $2^{10}$ = 1.024 aturan -> alihkan ke **Pairwise**). | Matriks kombinasi `Condition` vs `Rule (R1, R2...)` dan `Expected Action`. |
| **State Transition** | Sistem memiliki alur status (misal: `Pending -> Paid -> Shipped`) atau perilaku bergantung pada riwayat aksi. | Fitur *stateless* yang tidak menyimpan status sesi/data sebelumnya. | Transisi status yang sah (*valid path*) dan upaya melompati status (*invalid transition*). |
| **Pairwise Testing** | Banyak kombinasi parameter independen (Browser, OS, Resolusi, Bahasa) atau saat kondisi Decision Table membengkak. | Jika kombinasi parameter sedikit dan menentukan aturan bisnis bercabang yang ketat. | Pasangan kombinasi 2-arah (*all-pairs*) yang paling efisien. |
| **Error Guessing** | Validasi input ekstrem, keamanan dasar, dan skenario kegagalan umum pengguna. | Sebagai metode tunggal tanpa teknik sistematis di atas. | Emoji, karakter khusus, string kosong, spasi berlebih, XSS/SQLi dasar, klik ganda. |

## 2. Prosedur Khusus Membuat Decision Table
Jika kondisi fitur memenuhi syarat **Decision Table**, ikuti 5 langkah berikut secara berurutan:
1. **Identifikasi Kondisi (*Conditions*):** Tulis semua variabel input yang memengaruhi keputusan.
2. **Identifikasi Aksi (*Actions*):** Tulis semua kemungkinan output atau respons sistem.
3. **Hitung Jumlah Kombinasi ($2^n$):** Hitung $2^n$ (di mana $n$ adalah jumlah kondisi biner Ya/Tidak). Jika $n$ terlalu besar (misal $\ge 5$ kondisi sehingga kombinasi membengkak), hentikan Decision Table dan alihkan ke **Pairwise Testing**.
4. **Isi Tabel & Sederhanakan (*Collapsed Decision Table*):** Tentukan aksi untuk setiap kombinasi. Tandai kondisi yang tidak memengaruhi hasil akhir dengan strip (`-`) agar tabel lebih ringkas dan deteksi jika ada aturan bisnis yang bertabrakan/belum didefinisikan.
5. **Konversi ke Test Case:** Konversikan setiap kolom aturan (`R1`, `R2`, dst.) menjadi 1 skenario *test case* siap eksekusi.

## 3. Strategi & Alur Penyusunan Test Case

### A. Audit Spesifikasi Sebelum Menulis (Shift-Left / Gap Analysis)
Sebelum menulis baris *test case* dari PRD, Figma, atau kode, audit spesifikasi untuk menemukan:
- **Ambiguitas:** Aturan yang tidak terdefinisi jelas (misal: *"password harus aman"* tanpa syarat panjang/karakter).
- **Missing Error Handling:** Skenario kegagalan yang belum diatur (misal: koneksi terputus saat transaksi, token *expired*, atau kuota penuh).
- **Konflik Logika:** Dua aturan bisnis yang saling bertabrakan.
*(Jika ditemukan celah, konfirmasi terlebih dahulu sebelum mengunci test case).*

### B. Pembagian Fokus Berbasis Test Pyramid
- **Lapisan API / Backend (Porsi Terbesar):** Fokus menguji aturan bisnis kompleks, perhitungan angka, *Boundary Value Analysis (BVA)*, *Decision Table*, validasi payload ekstrem (*Error Guessing*), dan keamanan otorisasi (IDOR/akses ilegal).
- **Lapisan UI / E2E (Fokus Alur Utama):** Fokus menguji alur pengguna nyata (*happy path*), *State Transition* (perubahan status tombol/halaman), dan kompatibilitas antarperangkat (*Pairwise Testing*).

### C. Urutan Penyusunan 4 Lapis (4-Layer Test Design)
Susun dan urutkan daftar *test case* secara berurutan dari skenario paling vital ke skenario tepi:
1. **Lapisan 1 — Smoke / Happy Path (`Positive`, `Critical`):** Memastikan alur utama berhasil dari awal sampai akhir dengan data ideal.
2. **Lapisan 2 — Business Rules & State (`Positive/Negative`, `Critical/High`):** Menerapkan **Decision Table** untuk kombinasi syarat bisnis dan **State Transition** untuk perubahan status atau urutan langkah yang dilompati.
3. **Lapisan 3 — Input & Boundary Validation (`Negative`, `High/Medium`):** Menerapkan **Equivalence Partitioning** untuk kategori input dan **Boundary Value Analysis** tepat di titik batas (`min-1`, `min`, `max`, `max+1`).
4. **Lapisan 4 — Resilience & Edge-Case (`Negative`, `Medium/Low`):** Menerapkan **Error Guessing** untuk input ekstrem (emoji, spasi kosong, karakter khusus), *double-click* tombol submit, atau gangguan jaringan.

### D. Prinsip Atomic & Independent
- **Satu Skenario, Satu Tujuan:** Jangan menggabungkan dua validasi gagal (misal: email salah dan password salah sekaligus) ke dalam satu *test case* yang sama.
- **Independen:** Setiap *test case* harus dapat dijalankan secara mandiri tanpa bergantung pada keberhasilan *test case* sebelumnya (kecuali sedang menguji alur *State Transition* berantai).

## 4. Standar Klasifikasi & Prioritas Test Case

### A. Test Type (Tipe Pengujian)
- **Positive:** Menguji alur normal (*happy path*) menggunakan data valid untuk memastikan sistem bekerja sesuai spesifikasi.
- **Negative:** Menguji validasi error, nilai di luar batas (*out-of-bounds*), input ekstrem, atau alur ilegal untuk memastikan sistem menolak dengan aman tanpa *crash*.

### B. Priority (Tingkat Prioritas Eksekusi)
Gunakan kriteria berikut untuk menentukan urutan prioritas saat waktu *regression testing* terbatas:
- **Critical (P0):** Alur bisnis utama (*core flow*), autentikasi/keamanan, transaksi finansial, atau potensi kehilangan/kerusakan data.
- **High (P1):** Fitur penting yang sering digunakan pengguna dan validasi bisnis utama (tidak ada *workaround* mudah jika gagal).
- **Medium (P2):** Fitur pendukung, validasi batas minor, atau skenario kombinasi lanjutan.
- **Low (P3):** Masalah kosmetik/UI minor atau skenario *edge-case* yang sangat jarang terjadi.

## 5. Format Output Wajib
1. **Analisis & Pemilihan Metode:** Daftar komponen fitur beserta metode yang dipilih dan alasan pemilihannya.
2. **Matriks Pendukung:** Tampilkan tabel **Collapsed Decision Table** (jika menggunakan Decision Table) atau diagram/daftar transisi (jika menggunakan State Transition).
3. **Tabel Test Case:** Urutkan dari `Priority` tertinggi dengan kolom wajib:
   - `Test ID`
   - `Komponen / Kondisi`
   - `Metode Testing`
   - `Test Type` (`Positive` / `Negative`)
   - `Priority` (`Critical` / `High` / `Medium` / `Low`)
   - `Skenario & Test Data`
   - `Expected Result`