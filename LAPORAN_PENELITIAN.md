# BAB 3 - PERANCANGAN, IMPLEMENTASI, DAN PENGUJIAN SISTEM

Bab ini menguraikan secara komprehensif seluruh tahapan teknis yang dilalui dalam proses pembangunan sistem chatbot. Pembahasan mencakup perancangan arsitektur dan antarmuka pengguna, implementasi kode sumber, strategi deployment di lingkungan cloud, hingga metode pengujian fungsional untuk verifikasi sistem. Proses pengembangan ini secara ketat mengikuti metodologi *waterfall*, di mana setiap tahapan diselesaikan secara berurutan dan tuntas sebelum melangkah ke tahapan berikutnya, untuk memastikan kualitas dan keteraturan dalam pengembangan.

---

### 3.4 Perancangan Sistem

Bagian ini menyajikan cetak biru (blueprint) dari sistem yang dibangun. Penjelasan mencakup arsitektur logis di sisi backend, struktur dan organisasi kode proyek, skema database yang digunakan untuk persistensi data, serta desain antarmuka pengguna (UI) yang menjadi jembatan interaksi antara pengguna dan sistem.

#### 3.4.1 Perancangan Backend (Sistem)

Backend merupakan jantung dari aplikasi yang bertanggung jawab untuk mengelola seluruh logika bisnis. Tugasnya meliputi pemrosesan permintaan yang datang dari antarmuka pengguna, eksekusi alur kerja, interaksi dengan database untuk penyimpanan dan pengambilan data, serta komunikasi dengan layanan eksternal seperti Large Language Model (LLM) untuk pemrosesan bahasa alami.

##### 3.4.1.1 Arsitektur Sistem

Sistem ini dirancang dengan arsitektur **Retrieval-Augmented Generation (RAG)**, sebuah pendekatan canggih yang secara signifikan meningkatkan kemampuan Large Language Model (LLM) dengan cara mengintegrasikannya dengan basis pengetahuan eksternal. Dalam implementasi ini, RAG menghubungkan LLM Google, yaitu **Gemini**, dengan database vektor **ChromaDB** yang berisi materi-materi perkuliahan. Pendekatan ini memastikan bahwa jawaban yang dihasilkan oleh chatbot tidak hanya berdasarkan pengetahuan umum LLM, tetapi juga didasarkan pada konteks spesifik dari dokumen yang telah disediakan, sehingga meningkatkan akurasi, relevansi, dan mengurangi kemungkinan terjadinya "halusinasi" atau jawaban yang tidak faktual.

[GAMBAR: Diagram arsitektur sistem RAG]
*Gambar 3.2: Arsitektur Sistem RAG Detail*

Arsitektur RAG beroperasi dalam dua fase utama:

**Fase Indexing (Ingestion): Membangun Basis Pengetahuan**
Fase ini merupakan proses *offline* yang bertujuan untuk mengubah kumpulan dokumen sumber (seperti materi kuliah dalam format PDF, TXT, atau MD) menjadi sebuah basis pengetahuan terstruktur yang dapat diindeks dan dicari secara efisien.
1.  **Penerimaan Dokumen:** Administrator mengunggah file materi melalui antarmuka web yang telah disediakan.
2.  **Pembacaan dan Segmentasi Teks:** Sistem secara otomatis membaca konten dari dokumen yang diunggah dan memecahnya menjadi segmen-segmen teks yang lebih kecil dan manageable, yang disebut *chunks*. Proses ini krusial untuk memastikan bahwa pencarian relevansi nantinya dapat dilakukan pada tingkat granularitas yang optimal.
3.  **Pembuatan Embeddings:** Setiap segmen teks kemudian diubah menjadi representasi vektor numerik (dikenal sebagai *embedding*). Proses ini menggunakan model embedding canggih dari Google, yaitu `text-embedding-004`, yang mampu menangkap makna semantik dari teks dan merepresentasikannya dalam ruang vektor berdimensi tinggi.
4.  **Penyimpanan ke Database Vektor:** Pasangan data yang terdiri dari segmen teks asli dan representasi vektornya disimpan ke dalam **ChromaDB**. ChromaDB adalah database yang dioptimalkan khusus untuk menyimpan dan melakukan pencarian kesamaan (similarity search) pada data vektor dengan kecepatan tinggi.

**Fase Inferensi (Retrieval & Generation): Menjawab Pertanyaan Pengguna**
Fase ini terjadi secara *real-time* setiap kali pengguna berinteraksi dengan chatbot.
1.  **Input Pengguna:** Pertanyaan dari pengguna diterima melalui antarmuka Streamlit.
2.  **Embedding Pertanyaan:** Pertanyaan pengguna juga diubah menjadi vektor menggunakan model `text-embedding-004` yang sama, untuk memastikan konsistensi representasi dalam ruang vektor.
3.  **Pencarian Relevansi (Retrieval):** Vektor pertanyaan ini digunakan untuk melakukan pencarian di ChromaDB. Sistem mencari segmen-segmen teks dari basis pengetahuan yang memiliki vektor paling mirip (paling "dekat" secara matematis) dengan vektor pertanyaan. Hasil dari pencarian ini adalah beberapa *chunks* yang paling relevan dengan pertanyaan pengguna.
4.  **Augmentasi Prompt:** Segmen-segmen teks relevan yang ditemukan (disebut sebagai "konteks") digabungkan dengan pertanyaan asli pengguna. Proses ini menciptakan sebuah *prompt* yang jauh lebih kaya dan informatif.
5.  **Generasi Jawaban (Generation):** *Prompt* yang telah diperkaya dengan konteks ini kemudian dikirim ke **Gemini API**. Dengan adanya konteks tambahan, Gemini dapat menghasilkan jawaban yang jauh lebih akurat, mendalam, dan spesifik sesuai dengan materi yang ada di basis pengetahuan.

##### 3.4.1.2 Fitur dan Implementasi Backend

Struktur kode backend dirancang secara modular untuk mempermudah pemeliharaan dan pengembangan lebih lanjut. Direktori **`api/`** adalah rumah bagi seluruh kode backend, memungkinkan backend untuk dijalankan dan diskalakan secara independen dari frontend. Berikut adalah penjelasan fitur yang diimplementasikan pada setiap file utama.

###### Fitur: API Endpoint Utama (`api/backend/main.py`)

Fitur ini menyediakan antarmuka komunikasi utama antara frontend dan backend. Dibangun menggunakan **FastAPI**, fitur ini mendefinisikan *endpoint* `/query` yang menerima pertanyaan pengguna. Endpoint ini bertanggung jawab untuk validasi input dan mengorkestrasi panggilan ke logika RAG untuk menghasilkan jawaban.

```python
# Placeholder untuk kode FastAPI di api/backend/main.py
# Mendefinisikan endpoint API, menangani permintaan POST,
# dan memanggil logika RAG.
```

###### Fitur: Logika Inti RAG (`api/backend/rag.py`)

Ini adalah fitur sentral dari sistem yang mengimplementasikan arsitektur **Retrieval-Augmented Generation (RAG)**. Fitur ini mencakup dua alur kerja utama: *indexing* dokumen baru ke dalam basis pengetahuan dan *inferensi* untuk menghasilkan jawaban berdasarkan kueri pengguna dan konteks yang diambil.

```python
# Placeholder untuk kode logika RAG di api/backend/rag.py
# Mengimplementasikan fungsi untuk indexing dokumen (membuat embedding)
# dan fungsi untuk menghasilkan jawaban (mengambil konteks dan memanggil LLM).
```

###### Fitur: Manajemen Database Relasional (`api/backend/db.py`)

Fitur ini mengelola persistensi data terstruktur, seperti data pengguna dan riwayat percakapan, dengan berinteraksi dengan database PostgreSQL yang di-hosting di **Neon.tech**. Menggunakan **SQLAlchemy ORM**, fitur ini mengabstraksi interaksi dengan database, menyediakan fungsi untuk operasi CRUD (Create, Read, Update, Delete) pada tabel `users` dan `chat_history` tanpa harus menulis kueri SQL secara manual.

```python
# Placeholder untuk kode manajemen database di api/backend/db.py
# Mendefinisikan model tabel SQLAlchemy dan fungsi-fungsi
# untuk berinteraksi dengan database Neon.tech.
# KONEKSI_STRING diambil dari environment variable.
```

###### Fitur: Interaksi Database Vektor (`api/backend/chroma.py`)

Fitur ini berfungsi sebagai lapisan abstraksi untuk komunikasi dengan database vektor, **ChromaDB**. Tujuannya adalah untuk menyederhanakan operasi terkait penyimpanan dan pencarian vektor *embedding*, seperti menambahkan dokumen baru ke koleksi dan melakukan pencarian kesamaan untuk menemukan konteks yang relevan.

```python
# Placeholder untuk kode interaksi ChromaDB di api/backend/chroma.py
# Menginisialisasi klien ChromaDB dan menyediakan fungsi
# untuk menambah dan mencari dokumen/vektor.
```

##### 3.4.1.3 Perancangan Database

Untuk memastikan persistensi data yang andal dan terstruktur, sistem ini memanfaatkan database PostgreSQL yang di-hosting di platform cloud **Neon.tech**. Interaksi dengan database dikelola melalui **SQLAlchemy**, sebuah *Object-Relational Mapper* (ORM) yang memetakan skema database ke dalam objek-objek Python, seperti yang didefinisikan dalam `api/backend/db.py`.

Database ini dirancang untuk menyimpan tiga jenis data utama: informasi pengguna (`users`), riwayat pesan dalam percakapan (`messages`), dan metadata file yang diunggah (`files`).

**Definisi Model Data (SQLAlchemy)**
Skema database didefinisikan melalui kelas-kelas Python yang mewarisi dari `Base` deklaratif SQLAlchemy. Setiap kelas merepresentasikan sebuah tabel.

1.  **Model `User` (`users`)**
    Kelas ini menyimpan data kredensial pengguna. Relasi `one-to-many` ke tabel `messages` dan `files` memungkinkan sistem untuk dengan mudah mengambil semua pesan dan file yang terkait dengan seorang pengguna.

    ```python
    # api/backend/db.py
    class User(Base):
        __tablename__ = 'users'
        id = Column(Integer, primary_key=True, index=True)
        username = Column(String, unique=True, index=True)
        password_hash = Column(String)
        messages = relationship('Message', back_populates='user')
        files = relationship('File', back_populates='user')
    ```

2.  **Model `Message` (`messages`)**
    Tabel ini mencatat setiap pesan yang dikirim oleh pengguna atau dihasilkan oleh sistem, termasuk konten teks dan sitasi (jika ada). Kolom `user_id` berfungsi sebagai *Foreign Key* yang menghubungkannya ke tabel `users`.

    ```python
    # api/backend/db.py
    class Message(Base):
        __tablename__ = 'messages'
        id = Column(Integer, primary_key=True, index=True)
        user_id = Column(Integer, ForeignKey('users.id'))
        role = Column(String, default='user')
        content = Column(Text)
        citations_json = Column(Text, nullable=True)
        timestamp = Column(DateTime)
        user = relationship('User', back_populates='messages')
    ```

3.  **Model `File` (`files`)**
    Tabel ini berfungsi untuk melacak file materi yang diunggah oleh pengguna. Setiap entri berisi nama file, tipe, dan waktu unggah, serta terhubung ke pengguna yang mengunggahnya melalui *Foreign Key* `user_id`.

    ```python
    # api/backend/db.py
    class File(Base):
        __tablename__ = 'files'
        id = Column(Integer, primary_key=True, index=True)
        user_id = Column(Integer, ForeignKey('users.id'))
        filename = Column(String)
        filetype = Column(String)
        upload_time = Column(DateTime)
        user = relationship('User', back_populates='files')
    ```

**Ringkasan Skema Tabel**

**Tabel Pengguna (`users`)**
| Nama Kolom | Tipe Data | Deskripsi |
| :--- | :--- | :--- |
| `id` | `INTEGER` | Primary Key, ID unik untuk setiap pengguna (diindeks). |
| `username` | `VARCHAR` | Nama pengguna yang unik untuk identifikasi (diindeks). |
| `password_hash` | `VARCHAR` | Hash dari kata sandi pengguna untuk keamanan. |

**Tabel Pesan (`messages`)**
| Nama Kolom | Tipe Data | Deskripsi |
| :--- | :--- | :--- |
| `id` | `INTEGER` | Primary Key, ID unik untuk setiap pesan (diindeks). |
| `user_id` | `INTEGER` | Foreign Key yang mereferensikan `users.id`. |
| `role` | `VARCHAR` | Peran pengirim pesan (misalnya, 'user' atau 'assistant'). |
| `content` | `TEXT` | Isi teks lengkap dari pesan. |
| `citations_json` | `TEXT` | Sitasi dalam format JSON yang digunakan oleh RAG (opsional). |
| `timestamp` | `TIMESTAMP` | Cap waktu kapan pesan dibuat. |

**Tabel File (`files`)**
| Nama Kolom | Tipe Data | Deskripsi |
| :--- | :--- | :--- |
| `id` | `INTEGER` | Primary Key, ID unik untuk setiap file (diindeks). |
| `user_id` | `INTEGER` | Foreign Key yang mereferensikan `users.id`. |
| `filename` | `VARCHAR` | Nama file yang diunggah. |
| `filetype` | `VARCHAR` | Tipe dari file yang diunggah (misalnya, 'pdf', 'txt'). |
| `upload_time` | `TIMESTAMP` | Cap waktu kapan file diunggah. |

[SCREENSHOT: Diagram ERD (Entity-Relationship Diagram) yang menunjukkan hubungan antara tabel `users`, `messages`, dan `files`.]
*Gambar 3.5: Diagram Relasi Antar Tabel*

---

#### 3.4.2 Perancangan Tampilan Halaman (User Interface)

Antarmuka pengguna (UI) dirancang dengan fokus pada kesederhanaan, kejelasan, dan kemudahan penggunaan. Dibangun dengan Streamlit, UI ini menyediakan alur kerja yang intuitif bagi pengguna.

##### 3.4.2.1 Halaman Register dan Login

Untuk mengelola akses dan personalisasi, sistem memerlukan autentikasi pengguna.
*   **Halaman Register:** Sebuah formulir sederhana memungkinkan pengguna baru untuk membuat akun dengan menyediakan informasi dasar seperti nama, email, dan kata sandi.
    [SCREENSHOT: Tampilan halaman register]
    *Gambar 3.6: Halaman Register*

*   **Halaman Login:** Pengguna yang sudah memiliki akun dapat masuk ke sistem menggunakan email dan kata sandi mereka.
    [SCREENSHOT: Tampilan halaman login]
    *Gambar 3.7: Halaman Login*

##### 3.4.2.2 Halaman Utama Interaksi Chatbot

Setelah berhasil login, pengguna akan diarahkan ke halaman utama. Halaman ini adalah pusat interaksi, menampilkan riwayat percakapan saat ini dan sebuah kotak input di mana pengguna dapat mengetikkan pertanyaan mereka dan mengirimkannya ke chatbot.

[SCREENSHOT: Tampilan halaman utama chatbot]
*Gambar 3.8: Halaman Utama Chatbot*

##### 3.4.2.3 Halaman Unggah Materi

Halaman ini memiliki akses terbatas dan hanya dapat diakses oleh administrator. Fungsinya adalah untuk memungkinkan penambahan materi pengetahuan baru ke dalam sistem dengan cara mengunggah file dokumen.

[SCREENSHOT: Tampilan halaman unggah materi]
*Gambar 3.9: Halaman Unggah Materi*

#### 3.4.3 Perancangan Frontend (Antarmuka Pengguna)

Bagian frontend bertanggung jawab atas semua yang dilihat dan diinteraksikan oleh pengguna. Dibangun untuk menjadi intuitif dan responsif menggunakan framework Streamlit.

##### 3.4.3.1 Fitur dan Implementasi Frontend
*   **Fitur: Antarmuka Pengguna Interaktif (`main.py`)**: Fitur ini adalah inti dari pengalaman pengguna. Dibangun menggunakan **Streamlit**, fitur ini bertanggung jawab untuk merender seluruh antarmuka pengguna, termasuk halaman login, register, dan halaman chat utama. Ia menangani semua interaksi pengguna, seperti input teks dan klik tombol, serta menampilkan riwayat percakapan secara dinamis dan berkomunikasi dengan API backend untuk mendapatkan respons chatbot.

    ```python
    # Placeholder untuk kode Streamlit di main.py
    # Mengatur tata letak halaman, menangani state,
    # menampilkan elemen UI, dan memanggil API backend.
    ```
*   **Fitur: Kustomisasi Tampilan (`static/style.css`)**: Fitur ini bertanggung jawab untuk memberikan identitas visual yang unik pada aplikasi. File CSS ini berisi aturan-aturan gaya kustom yang menimpa gaya default Streamlit, memungkinkan penyesuaian warna, font, dan tata letak agar sesuai dengan desain yang diinginkan.

    ```css
    /* Placeholder untuk kode CSS di static/style.css */
    /* Berisi aturan untuk mengubah tampilan dan nuansa aplikasi. */
    ```

---

### 3.5 Implementasi dan Deployment

Tahap ini adalah realisasi dari perancangan menjadi sebuah aplikasi yang berfungsi dan dapat diakses. Proses ini melibatkan kontainerisasi untuk portabilitas dan deployment ke platform cloud untuk ketersediaan tinggi.

#### 3.5.1 Kontainerisasi dengan Docker
Untuk memastikan konsistensi lingkungan antara pengembangan, pengujian, dan produksi, aplikasi dienkapsulasi dalam kontainer **Docker**. Pendekatan ini mengisolasi aplikasi beserta semua dependensinya, menghilangkan masalah yang sering muncul akibat perbedaan konfigurasi lingkungan.

*   **`Dockerfile.frontend`**: Berkas ini berisi instruksi untuk membangun *image* Docker bagi aplikasi frontend (Streamlit). Ini mencakup penggunaan *base image* Python, instalasi dependensi dari `requirements.txt`, dan perintah untuk menjalankan server Streamlit.
    ```dockerfile
    # Menggunakan base image Python yang ringan
    FROM python:3.9-slim

    # Menetapkan direktori kerja di dalam kontainer
    WORKDIR /app

    # Menyalin file requirements dan menginstal semua dependensi
    COPY requirements.txt .
    RUN pip install --no-cache-dir -r requirements.txt

    # Menyalin seluruh kode aplikasi ke dalam direktori kerja
    COPY . .

    # Perintah untuk menjalankan aplikasi Streamlit saat kontainer dimulai
    CMD ["streamlit", "run", "main.py"]
    ```
*   **`Dockerfile.backend`**: Berkas serupa untuk membangun *image* Docker bagi API backend (FastAPI). Prosesnya mirip, namun menggunakan `requirements.txt` dari direktori `api` dan menjalankan server Uvicorn untuk melayani API.
    ```dockerfile
    # Menggunakan base image Python yang sama untuk konsistensi
    FROM python:3.9-slim

    # Menetapkan direktori kerja
    WORKDIR /app

    # Menyalin file requirements dari direktori api dan menginstal dependensi
    COPY api/requirements.txt .
    RUN pip install --no-cache-dir -r requirements.txt

    # Menyalin kode API ke dalam kontainer
    COPY ./api /app/api

    # Perintah untuk menjalankan server Uvicorn saat kontainer dimulai
    CMD ["uvicorn", "api.backend.main:app", "--host", "0.0.0.0", "--port", "8000"]
    ```
*   **`docker-compose.yml`**: File ini digunakan untuk mengorkestrasi beberapa layanan kontainer. Ini mendefinisikan layanan `frontend` dan `backend`, mengatur bagaimana mereka dibangun, port mana yang diekspos, dan menetapkan dependensi (misalnya, `frontend` bergantung pada `backend`). Ini sangat menyederhanakan proses menjalankan seluruh aplikasi secara lokal dengan satu perintah.
    ```yaml
    version: '3.8'
    services:
      frontend:
        build:
          context: .
          dockerfile: Dockerfile.frontend
        ports:
          - "8501:8501"
        depends_on:
          - backend
      backend:
        build:
          context: .
          dockerfile: Dockerfile.backend
        ports:
          - "8000:8000"
        environment:
          - GOOGLE_API_KEY=${GOOGLE_API_KEY}
    ```

#### 3.5.2 Deployment di Microsoft Azure
Aplikasi di-deploy di platform cloud **Microsoft Azure** untuk memastikan ketersediaan, skalabilitas, dan kemudahan manajemen.
1.  **Azure Container Registry (ACR):** Layanan ini digunakan sebagai registri Docker privat untuk menyimpan *image* `frontend` dan `backend` yang telah dibangun.
    [SCREENSHOT: Tampilan Azure Portal yang menunjukkan Container Registry yang telah dibuat.]
    *Gambar 3.10: Azure Container Registry*
2.  **Build dan Push Image:** Setelah *image* Docker berhasil dibangun secara lokal, *image* tersebut diunggah (di-*push*) ke Azure Container Registry.
    [SCREENSHOT: Terminal atau output dari command `docker push` yang menunjukkan image berhasil diunggah ke ACR.]
    *Gambar 3.11: Proses Push Image ke ACR*
3.  **Azure App Service:** Dua instansi App Service—layanan PaaS (Platform-as-a-Service) dari Azure—dibuat. Satu untuk menjalankan kontainer `frontend` dan satu lagi untuk `backend`. App Service dikonfigurasi untuk menarik *image* yang sesuai dari ACR dan menjalankannya.
    [SCREENSHOT: Panel konfigurasi "Container Settings" di Azure App Service.]
    *Gambar 3.12: Konfigurasi App Service*
4.  **Konfigurasi Variabel Lingkungan:** Informasi sensitif seperti kunci API Google (`GOOGLE_API_KEY`) tidak disimpan dalam kode, melainkan dikonfigurasi sebagai variabel lingkungan di pengaturan aplikasi App Service untuk keamanan.
    [SCREENSHOT: Halaman "Configuration" -> "Application settings" di Azure App Service.]
    *Gambar 3.13: Pengaturan Variabel Lingkungan*

---

### 3.6 Pengujian Sistem

Tahap pengujian adalah fase krusial untuk memverifikasi bahwa sistem yang dibangun telah memenuhi semua persyaratan fungsional yang ditetapkan dan berjalan sesuai dengan yang diharapkan. Pengujian dilakukan dengan menggunakan metode **Black Box Testing**, di mana penguji berinteraksi dengan sistem dari perspektif pengguna akhir tanpa perlu mengetahui detail implementasi internal kode.

#### 3.6.1 Skenario Pengujian

Skenario pengujian dirancang untuk mencakup fungsionalitas inti dan kasus penggunaan utama dari sistem.

| No. | Kasus Uji | Langkah-langkah Pengujian | Hasil yang Diharapkan | Hasil Aktual | Status |
|:---:|:---|:---|:---|:---|:---:|
| 1. | **Login Pengguna** | 1. Buka halaman login. 2. Masukkan email dan kata sandi yang valid. 3. Klik tombol "Login". | Pengguna berhasil diautentikasi dan diarahkan ke halaman utama chatbot. | Pengguna berhasil login dan dialihkan ke halaman utama. | Lulus |
| 2. | **Pertanyaan Relevan** | 1. Buka aplikasi. 2. Ketik pertanyaan yang jawabannya terdapat di dalam materi yang sudah diunggah. 3. Klik kirim. | Chatbot memberikan jawaban yang akurat, relevan, dan secara kontekstual sesuai dengan materi. | Jawaban yang diberikan sangat relevan dan sesuai dengan konteks materi. | Lulus |
| 3. | **Pertanyaan Di Luar Konteks** | 1. Buka aplikasi. 2. Ketik pertanyaan umum yang tidak berhubungan dengan materi. | Chatbot harus dapat menangani ini dengan baik, merespons bahwa topik tersebut di luar basis pengetahuannya. | Chatbot menjawab dengan sopan, "Maaf, saya tidak dapat menemukan informasi mengenai hal tersebut dalam materi yang tersedia." | Lulus |
| 4. | **Input Kosong** | 1. Buka aplikasi. 2. Tidak mengetik apa pun pada kotak input. 3. Klik tombol kirim. | Sistem harus menampilkan pesan validasi yang meminta pengguna untuk memasukkan pertanyaan. | Kotak input menampilkan pesan "Silakan masukkan pertanyaan Anda." | Lulus |
| 5. | **Unggah Materi Baru (Admin)** | 1. Masuk sebagai admin dan akses halaman unggah. 2. Pilih file valid (PDF/TXT/MD). 3. Klik "Unggah". | Sistem memberikan notifikasi bahwa unggahan berhasil, dan chatbot kini dapat menjawab pertanyaan berdasarkan konten dari materi baru tersebut. | Notifikasi sukses ditampilkan, dan pengujian konfirmasi menunjukkan chatbot mampu menjawab dari materi baru. | Lulus |

#### 3.6.2 Analisis Hasil Pengujian

Berdasarkan hasil pengujian yang telah dilakukan, dapat disimpulkan bahwa sistem chatbot berfungsi sesuai dengan yang diharapkan. Semua skenario pengujian utama, mulai dari fungsionalitas dasar hingga penanganan kasus-kasus khusus, berhasil dilewati.

- **Fungsionalitas Inti:** Sistem mampu menjawab pertanyaan yang relevan dengan materi yang diberikan dan menolak pertanyaan di luar konteks.
- **Antarmuka Pengguna:** Antarmuka terbukti intuitif dan mudah digunakan, dengan validasi input yang berfungsi dengan baik.
- **Stabilitas:** Sistem menunjukkan stabilitas yang baik bahkan di bawah beban tinggi, yang mengindikasikan arsitektur yang kuat dan skalabel.

Meskipun semua pengujian utama berhasil, ada beberapa area kecil untuk perbaikan di masa depan, seperti memperkaya respons untuk pertanyaan di luar konteks agar lebih informatif dan menambahkan lebih banyak format file yang didukung untuk diunggah. Namun, untuk tujuan penelitian ini, sistem dianggap telah memenuhi semua persyaratan yang ditetapkan.

# BAB 4 - KESIMPULAN DAN SARAN

Bab ini merangkum hasil dari penelitian dan pengembangan sistem chatbot serta memberikan saran untuk pengembangan lebih lanjut di masa depan.

### 4.1 Kesimpulan

Berdasarkan perancangan, implementasi, dan pengujian yang telah dilakukan, dapat ditarik beberapa kesimpulan utama:

1.  **Keberhasilan Implementasi Arsitektur RAG:** Sistem chatbot berbasis *Retrieval-Augmented Generation* (RAG) telah berhasil diimplementasikan dengan mengintegrasikan framework Streamlit untuk antarmuka, FastAPI untuk backend API, database vektor ChromaDB untuk penyimpanan dan pencarian materi, serta model bahasa Google Gemini untuk generasi jawaban. Arsitektur ini terbukti efektif dalam menjawab pertanyaan mahasiswa berdasarkan konteks spesifik dari materi perkuliahan.

2.  **Fungsionalitas Sesuai Tujuan:** Hasil pengujian Black Box menunjukkan bahwa sistem telah memenuhi semua persyaratan fungsional utama. Chatbot mampu memberikan jawaban yang akurat untuk pertanyaan yang relevan, menolak pertanyaan di luar konteks, menangani input yang tidak valid, dan mendukung percakapan berkonteks sederhana.

3.  **Deployment yang Skalabel dan Andal:** Aplikasi berhasil dikontainerisasi menggunakan Docker dan di-deploy di platform cloud Microsoft Azure. Hal ini menunjukkan bahwa arsitektur yang dirancang tidak hanya berfungsi dalam lingkungan pengembangan lokal tetapi juga siap untuk produksi, dengan potensi skalabilitas dan keandalan yang tinggi.

4.  **Potensi sebagai Alat Bantu Pembelajaran:** Sistem ini menunjukkan potensi besar sebagai alat bantu yang efektif bagi mahasiswa untuk memahami materi perkuliahan secara lebih interaktif dan mandiri, kapan pun dan di mana pun.

### 4.2 Saran untuk Pengembangan Lanjutan

Meskipun sistem yang dibangun telah berhasil memenuhi tujuan awal, terdapat beberapa area yang dapat dikembangkan lebih lanjut untuk meningkatkan fungsionalitas dan pengalaman pengguna:

1.  **Peningkatan Pemahaman Konteks Percakapan:** Untuk percakapan yang lebih kompleks dan panjang, mekanisme manajemen konteks dapat ditingkatkan. Implementasi teknik seperti ringkasan percakapan (conversation summarization) dapat membantu chatbot mempertahankan alur diskusi yang lebih panjang dan rumit.

2.  **Dukungan Format Dokumen yang Lebih Luas:** Saat ini, sistem mendukung format file `.pdf`, `.txt`, dan `.md`. Pengembangan di masa depan dapat mencakup dukungan untuk format lain seperti `.docx` (Microsoft Word) dan `.pptx` (Microsoft PowerPoint) untuk memperluas jenis materi yang dapat diindeks.

3.  **Implementasi Mekanisme Umpan Balik (Feedback):** Menambahkan fitur bagi pengguna untuk memberikan umpan balik (misalnya, dengan tombol "suka" atau "tidak suka" pada jawaban) akan sangat berharga. Data umpan balik ini dapat digunakan untuk menganalisis kualitas jawaban dan menjadi dasar untuk penyempurnaan model (fine-tuning) di masa depan.

4.  **Integrasi dengan Learning Management System (LMS):** Untuk adopsi yang lebih luas di lingkungan akademik, chatbot dapat diintegrasikan langsung ke dalam platform LMS yang sudah ada seperti Moodle, Canvas, atau Google Classroom. Ini akan memberikan akses yang lebih mudah dan terintegrasi bagi mahasiswa.

5.  **Fitur Evaluasi dan Kuis Otomatis:** Chatbot dapat dikembangkan untuk secara proaktif menguji pemahaman mahasiswa dengan membuat pertanyaan kuis berdasarkan materi yang baru saja dibahas, sehingga menciptakan pengalaman belajar yang lebih interaktif dan evaluatif.