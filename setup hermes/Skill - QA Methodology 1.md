​~/.hermes/skills/qa-methodology/SKILL.md


cat << 'EOF' > ~/.hermes/skills/qa-methodology/SKILL.md
---
name: qa-methodology
description: Standar pemilihan metode testing QA berbasis ISTQB (EP, BVA, Decision Table, State Transition, Pairwise, Error Guessing) lengkap dengan prosedur Decision Table dan format output.
---

# Panduan Seleksi Metode Testing QA

Gunakan panduan ini untuk menentukan metode paling akurat pada setiap komponen fitur:

| Metode Testing | Kapan Tepat Digunakan | Kapan Jangan Digunakan | Fokus Pengujian |
| :--- | :--- | :--- | :--- |
| **Equivalence Partitioning (EP)** | Input terbagi dalam kelompok/kategori yang diperlakukan sama oleh sistem (misal: tipe member, kode wilayah, rentang non-batas). | Jika fokusnya adalah titik ujung batas minimum/maksimum. | 1 sampel perwakilan dari tiap partisi valid & tidak valid. |
| **Boundary Value Analysis (BVA)** | Terdapat batasan angka, panjang teks, jumlah item, atau kuota yang jelas (`min` / `max`). | Input berupa pilihan kategori tanpa urutan atau batas ukuran. | Nilai tepi: `min-1`, `min`, `max`, `max+1`. |
| **Decision Table** | Aturan bisnis kompleks dengan beberapa kondisi (`True/False`) yang menghasilkan output berbeda (ideal untuk 2–4 kondisi biner). | Menguji urutan langkah (*flow*), atau jika jumlah kondisi terlalu banyak (misal 10 kondisi = $2^{10}$ = 1.024 aturan -> alihkan ke **Pairwise**). | Matriks kombinasi `Condition` vs `Rule (R1, R2...)` dan `Expected Action`. |
| **State Transition** | Sistem memiliki alur status (misal: `Pending -> Paid -> Shipped`) atau perilaku bergantung pada riwayat aksi. | Fitur *stateless* yang tidak menyimpan status sesi/data sebelumnya. | Transisi status yang sah (*valid path*) dan upaya melompati status (*invalid transition*). |
| **Pairwise Testing** | Banyak kombinasi parameter independen (Browser, OS, Resolusi, Bahasa) atau saat kondisi Decision Table membengkak. | Jika kombinasi parameter sedikit dan menentukan aturan bisnis bercabang yang ketat. | Pasangan kombinasi 2-arah (*all-pairs*) yang paling efisien. |
| **Error Guessing** | Validasi input ekstrem, keamanan dasar, dan skenario kegagalan umum pengguna. | Sebagai metode tunggal tanpa teknik sistematis di atas. | Emoji, karakter khusus, string kosong, spasi berlebih, XSS/SQLi dasar, klik ganda. |

## Prosedur Khusus Membuat Decision Table
Jika kondisi fitur memenuhi syarat **Decision Table**, ikuti 5 langkah berikut secara berurutan:
1. **Identifikasi Kondisi (*Conditions*):** Tulis semua variabel input yang memengaruhi keputusan.
2. **Identifikasi Aksi (*Actions*):** Tulis semua kemungkinan output atau respons sistem.
3. **Hitung Jumlah Kombinasi ($2^n$):** Hitung $2^n$ (di mana $n$ adalah jumlah kondisi biner Ya/Tidak). Jika $n$ terlalu besar (misal $\ge 5$ kondisi sehingga kombinasi membengkak), hentikan Decision Table dan alihkan ke **Pairwise Testing**.
4. **Isi Tabel & Sederhanakan (*Collapsed Decision Table*):** Tentukan aksi untuk setiap kombinasi. Tandai kondisi yang tidak memengaruhi hasil akhir dengan strip (`-`) agar tabel lebih ringkas dan deteksi jika ada aturan bisnis yang bertabrakan/belum didefinisikan.
5. **Konversi ke Test Case:** Konversikan setiap kolom aturan (`R1`, `R2`, dst.) menjadi 1 skenario *test case* siap eksekusi.

## Format Output Wajib
1. **Analisis & Pemilihan Metode:** Daftar komponen fitur beserta metode yang dipilih dan alasan pemilihannya.
2. **Matriks Pendukung:** Tampilkan tabel **Collapsed Decision Table** (jika menggunakan Decision Table) atau diagram/daftar transisi (jika menggunakan State Transition).
3. **Tabel Test Case:** Kolom wajib: `Test ID`, `Komponen / Kondisi`, `Metode Testing`, `Skenario & Test Data`, dan `Expected Result`.