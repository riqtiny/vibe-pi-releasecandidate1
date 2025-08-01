# Diagram Sekuens (Sequence Diagram) Sistem Chatbot Materi Perkuliahan

Dokumen ini berisi diagram sekuens (sequence diagram) dalam format PlantUML untuk setiap alur utama dari kasus penggunaan yang dijelaskan dalam `functional_requirements.md`. Fokus utama adalah interaksi antara pengguna (Mahasiswa) dan sistem, serta komponen-komponen internal sistem.

---

## 1. Use Case: Interaksi Chatbot (Mahasiswa)

Diagram ini menggambarkan alur utama ketika mahasiswa berinteraksi dengan chatbot untuk mengajukan pertanyaan dan menerima jawaban.

```plantuml
@startuml
actor Mahasiswa
participant "Antarmuka Pengguna (UI)" as UI
participant "Sistem Backend" as Backend
participant "Modul RAG" as RAG
participant "Basis Pengetahuan (ChromaDB)" as DB
participant "Model Gemini" as Gemini

Mahasiswa -> UI: 1. Mengakses Chatbot
Mahasiswa -> UI: 2. Mengetik & Mengirim Pertanyaan
UI -> Backend: 3. Kirim Pertanyaan (API Request)
activate Backend
Backend -> RAG: 4. Proses Pertanyaan
activate RAG
RAG -> DB: 5. Cari Informasi Relevan
activate DB
DB --> RAG: 6. Informasi Relevan
deactivate DB
RAG -> Gemini: 7. Hasilkan Jawaban (Pertanyaan + Konteks)
activate Gemini
Gemini --> RAG: 8. Jawaban Komprehensif
deactivate Gemini
RAG --> Backend: 9. Kirim Jawaban
deactivate RAG
Backend -> UI: 10. Tampilkan Jawaban (API Response)
UI -> Mahasiswa: 11. Tampilkan Jawaban
Backend -> Backend: 12. Simpan Riwayat Chat
deactivate Backend
@enduml
```

---

## 2. Use Case: Manajemen Basis Pengetahuan (Mahasiswa)

Kasus penggunaan ini dibagi menjadi tiga sub-alur utama: Penambahan, Pengubahan, dan Penghapusan materi.

### 2.1. Penambahan Materi

Diagram ini menunjukkan alur penambahan materi perkuliahan baru ke basis pengetahuan.

```plantuml
@startuml
actor Mahasiswa
participant "Antarmuka Pengguna (UI)" as UI
participant "Sistem Backend" as Backend
participant "Modul Manajemen Pengetahuan" as KnowledgeManager
participant "Basis Pengetahuan (ChromaDB)" as DB

Mahasiswa -> UI: 1. Akses Fitur "Manajemen Basis Pengetahuan"
Mahasiswa -> UI: 2. Pilih Opsi Penambahan
Mahasiswa -> UI: 3. Unggah/Masukkan Materi Baru
UI -> Backend: 4. Kirim Materi Baru (API Request)
activate Backend
Backend -> KnowledgeManager: 5. Proses & Simpan Materi
activate KnowledgeManager
KnowledgeManager -> DB: 6. Simpan Materi ke Basis Pengetahuan
activate DB
DB --> KnowledgeManager: 7. Konfirmasi Penyimpanan
deactivate DB
KnowledgeManager --> Backend: 8. Konfirmasi Penambahan
deactivate KnowledgeManager
Backend --> UI: 9. Konfirmasi Penambahan (API Response)
deactivate Backend
UI -> Mahasiswa: 10. Tampilkan Konfirmasi Penambahan
@enduml
```

### 2.2. Pengubahan Materi

Diagram ini menunjukkan alur pengubahan materi perkuliahan yang sudah ada di basis pengetahuan.

```plantuml
@startuml
actor Mahasiswa
participant "Antarmuka Pengguna (UI)" as UI
participant "Sistem Backend" as Backend
participant "Modul Manajemen Pengetahuan" as KnowledgeManager
participant "Basis Pengetahuan (ChromaDB)" as DB

Mahasiswa -> UI: 1. Akses Fitur "Manajemen Basis Pengetahuan"
Mahasiswa -> UI: 2. Pilih Opsi Pengubahan
Mahasiswa -> UI: 3. Pilih Materi yang Ingin Diubah
Mahasiswa -> UI: 4. Lakukan Perubahan pada Materi
UI -> Backend: 5. Kirim Perubahan Materi (API Request)
activate Backend
Backend -> KnowledgeManager: 6. Perbarui Materi
activate KnowledgeManager
KnowledgeManager -> DB: 7. Perbarui Materi di Basis Pengetahuan
activate DB
DB --> KnowledgeManager: 8. Konfirmasi Pembaruan
deactivate DB
KnowledgeManager --> Backend: 9. Konfirmasi Pengubahan
deactivate KnowledgeManager
Backend --> UI: 10. Konfirmasi Pengubahan (API Response)
deactivate Backend
UI -> Mahasiswa: 11. Tampilkan Konfirmasi Pengubahan
@enduml
```

### 2.3. Penghapusan Materi

Diagram ini menunjukkan alur penghapusan materi perkuliahan dari basis pengetahuan.

```plantuml
@startuml
actor Mahasiswa
participant "Antarmuka Pengguna (UI)" as UI
participant "Sistem Backend" as Backend
participant "Modul Manajemen Pengetahuan" as KnowledgeManager
participant "Basis Pengetahuan (ChromaDB)" as DB

Mahasiswa -> UI: 1. Akses Fitur "Manajemen Basis Pengetahuan"
Mahasiswa -> UI: 2. Pilih Opsi Penghapusan
Mahasiswa -> UI: 3. Pilih Materi yang Ingin Dihapus
UI -> Mahasiswa: 4. Minta Konfirmasi Penghapusan
Mahasiswa -> UI: 5. Konfirmasi Penghapusan
UI -> Backend: 6. Kirim Permintaan Penghapusan (API Request)
activate Backend
Backend -> KnowledgeManager: 7. Hapus Materi
activate KnowledgeManager
KnowledgeManager -> DB: 8. Hapus Materi dari Basis Pengetahuan
activate DB
DB --> KnowledgeManager: 9. Konfirmasi Penghapusan
deactivate DB
KnowledgeManager --> Backend: 10. Konfirmasi Penghapusan
deactivate KnowledgeManager
Backend --> UI: 11. Konfirmasi Penghapusan (API Response)
deactivate Backend
UI -> Mahasiswa: 12. Tampilkan Konfirmasi Penghapusan
@enduml
```

---

## 3. Use Case: Autentikasi Pengguna (Mahasiswa)

Kasus penggunaan ini dibagi menjadi dua sub-alur utama: Pendaftaran Pengguna Baru dan Login Pengguna.

### 3.1. Pendaftaran Pengguna Baru

Diagram ini menunjukkan alur pendaftaran akun baru oleh mahasiswa.

```plantuml
@startuml
actor Mahasiswa
participant "Antarmuka Pengguna (UI)" as UI
participant "Sistem Backend" as Backend
participant "Modul Autentikasi" as AuthModule
participant "Basis Data Pengguna" as UserDB

Mahasiswa -> UI: 1. Akses Halaman Login/Pendaftaran
Mahasiswa -> UI: 2. Pilih Opsi Pendaftaran Pengguna Baru
Mahasiswa -> UI: 3. Isi Formulir Pendaftaran
UI -> Backend: 4. Kirim Data Pendaftaran (API Request)
activate Backend
Backend -> AuthModule: 5. Validasi Input & Buat Akun
activate AuthModule
AuthModule -> UserDB: 6. Simpan Data Pengguna Baru
activate UserDB
UserDB --> AuthModule: 7. Konfirmasi Penyimpanan
deactivate UserDB
AuthModule --> Backend: 8. Konfirmasi Pembuatan Akun
deactivate AuthModule
Backend --> UI: 9. Konfirmasi Pendaftaran (API Response)
deactivate Backend
UI -> Mahasiswa: 10. Tampilkan Konfirmasi & Arahkan ke Halaman Login
@enduml
```

### 3.2. Login Pengguna

Diagram ini menunjukkan alur login pengguna ke sistem.

```plantuml
@startuml
actor Mahasiswa
participant "Antarmuka Pengguna (UI)" as UI
participant "Sistem Backend" as Backend
participant "Modul Autentikasi" as AuthModule
participant "Basis Data Pengguna" as UserDB

Mahasiswa -> UI: 1. Akses Halaman Login/Pendaftaran
Mahasiswa -> UI: 2. Pilih Opsi Login
Mahasiswa -> UI: 3. Masukkan Kredensial (Username/Email, Password)
UI -> Backend: 4. Kirim Kredensial (API Request)
activate Backend
Backend -> AuthModule: 5. Validasi Kredensial
activate AuthModule
AuthModule -> UserDB: 6. Cari Pengguna & Verifikasi Password
activate UserDB
UserDB --> AuthModule: 7. Hasil Verifikasi
deactivate UserDB
alt Kredensial Valid
    AuthModule --> Backend: 8. Kredensial Valid
    deactivate AuthModule
    Backend -> Backend: 9. Buat Sesi Pengguna
    Backend --> UI: 10. Arahkan ke Dashboard/Halaman Utama (API Response)
    deactivate Backend
    UI -> Mahasiswa: 11. Tampilkan Dashboard/Halaman Utama
else Kredensial Tidak Valid
    AuthModule --> Backend: 8. Kredensial Tidak Valid
    deactivate AuthModule
    Backend --> UI: 9. Kirim Pesan Kesalahan (API Response)
    deactivate Backend
    UI -> Mahasiswa: 10. Tampilkan Pesan Kesalahan
end
@enduml
```
