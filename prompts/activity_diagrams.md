## 1. Activity Diagram: Interaksi Chatbot

```plantuml
@startuml
|Mahasiswa|
start
:Mengakses Antarmuka Chatbot;
:Mengetikkan pertanyaan;
:Mengirim Pertanyaan;

|Antarmuka Pengguna (UI)|
:Menerima input pertanyaan dari Mahasiswa;
:Mengirim pertanyaan ke Sistem Backend;

|Sistem Backend|
:Menerima request pertanyaan;
:Meneruskan pertanyaan ke Modul RAG;

|Modul RAG|
:Memproses pertanyaan;
:Mencari informasi relevan di Basis Pengetahuan;

|Basis Pengetahuan (ChromaDB)|
:Menerima permintaan pencarian;
:Mengembalikan informasi relevan;

|Modul RAG|
:Menerima informasi relevan;
:Menyiapkan prompt (Pertanyaan + Konteks);
:Mengirim prompt ke Model Gemini;

|Model Gemini|
:Menerima prompt;
:Menghasilkan jawaban komprehensif;
:Mengembalikan jawaban;

|Modul RAG|
:Menerima jawaban dari Gemini;
:Mengirim jawaban ke Sistem Backend;

|Sistem Backend|
:Menerima jawaban;
:Mengirim jawaban ke Antarmuka Pengguna;
:Menyimpan riwayat chat;

|Antarmuka Pengguna (UI)|
:Menerima jawaban dari backend;
:Menampilkan jawaban kepada Mahasiswa;

|Mahasiswa|
:Melihat jawaban;
stop
@enduml
```

## 2. Activity Diagram: Manajemen Basis Pengetahuan

### 2.1. Penambahan Materi

```plantuml
@startuml
|Mahasiswa|
start
:Login ke sistem;
:Mengakses fitur "Manajemen Basis Pengetahuan";
:Memilih opsi "Penambahan Materi";
:Mengunggah atau memasukkan materi baru;

|Antarmuka Pengguna (UI)|
:Menerima materi baru;
:Mengirim materi ke Sistem Backend;

|Sistem Backend|
:Menerima request penambahan materi;
:Meneruskan ke Modul Manajemen Pengetahuan;

|Modul Manajemen Pengetahuan|
:Memproses materi baru;
:Menyimpan materi ke Basis Pengetahuan;

|Basis Pengetahuan (ChromaDB)|
:Menerima dan menyimpan materi;
:Memberikan konfirmasi penyimpanan;

|Modul Manajemen Pengetahuan|
:Menerima konfirmasi;
:Mengirim konfirmasi penambahan ke Backend;

|Sistem Backend|
:Menerima konfirmasi;
:Mengirim konfirmasi ke Antarmuka Pengguna;

|Antarmuka Pengguna (UI)|
:Menerima konfirmasi;
:Menampilkan konfirmasi penambahan kepada Mahasiswa;

|Mahasiswa|
:Melihat konfirmasi;
stop
@enduml
```

### 2.2. Pengubahan Materi

```plantuml
@startuml
|Mahasiswa|
start
:Login ke sistem;
:Mengakses fitur "Manajemen Basis Pengetahuan";
:Memilih opsi "Pengubahan Materi";
:Memilih materi yang akan diubah;
:Melakukan perubahan pada materi;

|Antarmuka Pengguna (UI)|
:Menerima data perubahan materi;
:Mengirim data perubahan ke Sistem Backend;

|Sistem Backend|
:Menerima request pengubahan materi;
:Meneruskan ke Modul Manajemen Pengetahuan;

|Modul Manajemen Pengetahuan|
:Memproses perubahan;
:Memperbarui materi di Basis Pengetahuan;

|Basis Pengetahuan (ChromaDB)|
:Menerima dan memperbarui materi;
:Memberikan konfirmasi pembaruan;

|Modul Manajemen Pengetahuan|
:Menerima konfirmasi;
:Mengirim konfirmasi pengubahan ke Backend;

|Sistem Backend|
:Menerima konfirmasi;
:Mengirim konfirmasi ke Antarmuka Pengguna;

|Antarmuka Pengguna (UI)|
:Menerima konfirmasi;
:Menampilkan konfirmasi pengubahan kepada Mahasiswa;

|Mahasiswa|
:Melihat konfirmasi;
stop
@enduml
```

### 2.3. Penghapusan Materi

```plantuml
@startuml
|Mahasiswa|
start
:Login ke sistem;
:Mengakses fitur "Manajemen Basis Pengetahuan";
:Memilih opsi "Penghapusan Materi";
:Memilih materi yang akan dihapus;

|Antarmuka Pengguna (UI)|
:Menampilkan dialog konfirmasi penghapusan;

|Mahasiswa|
:Memberikan konfirmasi penghapusan;

|Antarmuka Pengguna (UI)|
:Menerima konfirmasi;
:Mengirim permintaan penghapusan ke Sistem Backend;

|Sistem Backend|
:Menerima request penghapusan;
:Meneruskan ke Modul Manajemen Pengetahuan;

|Modul Manajemen Pengetahuan|
:Memproses permintaan penghapusan;
:Menghapus materi dari Basis Pengetahuan;

|Basis Pengetahuan (ChromaDB)|
:Menerima dan memproses penghapusan;
:Memberikan konfirmasi penghapusan;

|Modul Manajemen Pengetahuan|
:Menerima konfirmasi;
:Mengirim konfirmasi penghapusan ke Backend;

|Sistem Backend|
:Menerima konfirmasi;
:Mengirim konfirmasi ke Antarmuka Pengguna;

|Antarmuka Pengguna (UI)|
:Menerima konfirmasi;
:Menampilkan konfirmasi penghapusan kepada Mahasiswa;

|Mahasiswa|
:Melihat konfirmasi;
stop
@enduml
```

## 3. Activity Diagram: Autentikasi Pengguna

### 3.1. Pendaftaran Pengguna Baru

```plantuml
@startuml
|Mahasiswa|
start
:Mengakses halaman pendaftaran;
:Mengisi formulir pendaftaran;
:Mengirim formulir;

|Antarmuka Pengguna (UI)|
:Menerima data pendaftaran;
:Mengirim data ke Sistem Backend;

|Sistem Backend|
:Menerima request pendaftaran;
:Meneruskan ke Modul Autentikasi;

|Modul Autentikasi|
:Memvalidasi input;
:Membuat akun pengguna baru;
:Menyimpan data pengguna ke Basis Data;

|Basis Data Pengguna|
:Menerima dan menyimpan data pengguna;
:Memberikan konfirmasi penyimpanan;

|Modul Autentikasi|
:Menerima konfirmasi;
:Mengirim konfirmasi pendaftaran ke Backend;

|Sistem Backend|
:Menerima konfirmasi;
:Mengirim konfirmasi ke Antarmuka Pengguna;

|Antarmuka Pengguna (UI)|
:Menerima konfirmasi;
:Menampilkan konfirmasi pendaftaran;
:Mengarahkan ke halaman login;

|Mahasiswa|
:Melihat konfirmasi;
stop
@enduml
```

### 3.2. Login Pengguna

```plantuml
@startuml
|Mahasiswa|
start
:Mengakses halaman login;
:Memasukkan kredensial (username/password);
:Menekan tombol login;

|Antarmuka Pengguna (UI)|
:Menerima kredensial;
:Mengirim kredensial ke Sistem Backend;

|Sistem Backend|
:Menerima request login;
:Meneruskan ke Modul Autentikasi;

|Modul Autentikasi|
:Memvalidasi kredensial;
:Mencari pengguna di Basis Data;

|Basis Data Pengguna|
:Menerima permintaan verifikasi;
:Memverifikasi kredensial;
:Mengembalikan hasil verifikasi;

|Modul Autentikasi|
:Menerima hasil verifikasi;
if (Kredensial Valid?) then (Ya)
  :Mengirim notifikasi sukses ke Backend;
else (Tidak)
  :Mengirim notifikasi gagal ke Backend;
endif

|Sistem Backend|
if (Notifikasi Sukses?) then (Ya)
  :Membuat sesi pengguna;
  :Mengirim respons sukses ke UI;
else (Tidak)
  :Mengirim pesan kesalahan ke UI;
endif

|Antarmuka Pengguna (UI)|
if (Respons Sukses?) then (Ya)
  :Mengarahkan ke halaman utama/dashboard;
else (Tidak)
  :Menampilkan pesan kesalahan;
endif

|Mahasiswa|
:Melihat halaman utama atau pesan kesalahan;
stop
@enduml
```
