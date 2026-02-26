# Membangun Containerized App Menggunakan Docker

Dokumen ini menjelaskan langkah-langkah membangun dan menjalankan **aplikasi Flask** sebagai **Containerized Application (CA)** menggunakan **Docker**, mulai dari pengecekan Docker hingga aplikasi berhasil digunakan melalui browser.

Container memungkinkan aplikasi berjalan secara konsisten di berbagai lingkungan tanpa konflik dependensi maupun konfigurasi sistem.

---

## 1. Pengecekan Versi Docker

Pastikan Docker telah terinstal dan berjalan dengan baik pada sistem Anda sebelum memulai proses containerisasi.

```bash
docker version
```

Perintah ini digunakan untuk menampilkan informasi versi Docker Client dan Docker Server (Engine). Jika informasi versi muncul, berarti Docker sudah aktif dan siap digunakan.

<img src="https://github.com/user-attachments/assets/5de9c40c-e2d3-4ec8-86c7-93d6bba8d5be" width="550" />

---

## 2. Mengambil Source Code Flask

Langkah berikutnya adalah mengambil source code resmi Flask dari GitHub.

```bash
git clone https://github.com/pallets/flask
```

Perintah `git clone` digunakan untuk menyalin seluruh repository Flask ke komputer lokal agar dapat digunakan sebagai dasar pembuatan aplikasi.

<img src="https://github.com/user-attachments/assets/c88f655c-8690-463e-b6a1-08b4be9445c6" width="550" />

Masuk ke direktori Flask dan lihat daftar tag versi yang tersedia:

```bash
cd flask
git tag
```

Perintah `git tag` menampilkan daftar versi yang tersedia pada repository tersebut.

<img src="https://github.com/user-attachments/assets/21df7a8f-6e6c-46d5-bf80-13d9f00bcaf7" width="550" />

Pindah ke versi stabil yang digunakan:

```bash
git checkout 3.1.2
```

Perintah `git checkout` digunakan untuk berpindah ke versi tertentu agar aplikasi menggunakan versi yang stabil dan teruji.

<img src="https://github.com/user-attachments/assets/16642e77-6c37-4271-b187-3997ba2999ed" width="550" />

---

## 3. Menyiapkan Aplikasi Flask Tutorial

Flask menyediakan contoh aplikasi tutorial yang dapat digunakan sebagai bahan praktik.

Salin contoh aplikasi tutorial Flask:

```bash
cp -R flask/examples/tutorial flask-app
```

Perintah ini menyalin folder tutorial ke folder baru bernama `flask-app` yang akan dijadikan aplikasi utama.

Cek isi direktori:

```bash
ls
```

Perintah `ls` digunakan untuk memastikan folder `flask-app` berhasil dibuat.

<img src="https://github.com/user-attachments/assets/32d8af8e-de46-4a05-9b23-adeff8f50cc0" width="550" />

Masuk ke direktori aplikasi:

```bash
cd flask-app
```

<img src="https://github.com/user-attachments/assets/14890043-30b9-4704-8bb3-d1850565795f" width="550" />

Aplikasi tutorial ini memiliki fitur dasar seperti:
- Registrasi pengguna
- Login
- Membuat postingan
- Mengedit dan menghapus postingan

---

## 4. Membuat Dockerfile

Dockerfile adalah file konfigurasi yang digunakan untuk membangun image Docker.

Buat file **Dockerfile** di direktori root aplikasi Flask dengan isi berikut:

```dockerfile
FROM python:3.13
RUN mkdir /app
WORKDIR /app
ADD . /app/
RUN pip install -e .
RUN flask --app flaskr init-db
EXPOSE 5000
CMD ["flask", "--app", "flaskr", "run", "--host=0.0.0.0", "--port=5000"]
```

Penjelasan instruksi:

- `FROM python:3.13` → Menggunakan image Python sebagai dasar container.
- `WORKDIR /app` → Menentukan direktori kerja di dalam container.
- `ADD . /app/` → Menyalin seluruh file aplikasi ke dalam container.
- `RUN pip install -e .` → Menginstal dependensi aplikasi.
- `RUN flask --app flaskr init-db` → Menginisialisasi database sebelum aplikasi dijalankan.
- `EXPOSE 5000` → Membuka port 5000 pada container.
- `CMD` → Menjalankan aplikasi Flask saat container dijalankan.

<img src="https://github.com/user-attachments/assets/3f676551-305c-463c-9422-e1637df94c4c" width="550" />

---

## 5. Build Image Docker

Setelah Dockerfile dibuat, bangun image Docker menggunakan perintah berikut:

```bash
docker build -t flaskr:1.0.0 .
```

Perintah ini membaca Dockerfile dan membuat image baru dengan nama `flaskr` dan tag versi `1.0.0`.

<img src="https://github.com/user-attachments/assets/574ac673-2a3f-4072-9870-0daacbd5cac7" width="550" />

---

## 6. Menjalankan Container

Jalankan container dari image yang telah dibuat dengan memetakan port container ke port lokal:

```bash
docker run -p 5001:5000 flaskr:1.0.0
```

Artinya:
- Port 5000 di dalam container
- Dipetakan ke port 5001 di komputer lokal

Dengan demikian, aplikasi dapat diakses melalui browser menggunakan port 5001.

<img src="https://github.com/user-attachments/assets/8fe8f2a2-a017-458e-8cd6-316614f1f69e" width="550" />

---

## 7. Akses Aplikasi di Browser

Buka browser dan akses alamat berikut:

```
http://localhost:5001
```

Jika berhasil, halaman utama aplikasi Flask akan tampil.

<img src="https://github.com/user-attachments/assets/e80c072f-8cb0-4937-87db-a61d8427ed95" width="550" />

Pengguna dapat melakukan:
- Register
- Login
- Membuat postingan
- Mengedit dan menghapus postingan sendiri

---

## 8. Membangun Ulang Image (Jika Ada Perubahan)

Jika terdapat perubahan pada kode aplikasi atau Dockerfile, lakukan build ulang:

```bash
docker build -t flaskr:1.0.0 .
```

<img src="https://github.com/user-attachments/assets/7c5ccf76-fa4b-4897-8066-0c24668edd31" width="550" />

Kemudian jalankan kembali container:

```bash
docker run -p 5001:5000 flaskr:1.0.0
```

<img src="https://github.com/user-attachments/assets/553d2493-85f9-4494-9933-cbc548e4d6a1" width="550" />

---

## 9. Monitoring Container dan Image

Untuk melihat container yang sedang atau pernah berjalan:

```bash
docker ps -a
```

Perintah ini menampilkan daftar seluruh container beserta statusnya.

<img src="https://github.com/user-attachments/assets/5998da3c-6f8a-4e9f-b983-6a7434e79de2" width="550" />

Untuk melihat daftar image Docker:

```bash
docker image ls
```

Perintah ini menampilkan seluruh image yang tersimpan di sistem.

<img src="https://github.com/user-attachments/assets/b5939e0f-b4e8-4420-a94f-a98c9dc132f0" width="550" />

---

## 10. Pengujian Aplikasi

Contoh hasil pengujian aplikasi menunjukkan bahwa seluruh fitur berjalan dengan baik, seperti:

- Login berhasil
- Membuat postingan
- Menampilkan daftar postingan
- Mengedit dan menghapus postingan

<img src="https://github.com/user-attachments/assets/56a44e3d-ad96-4a8d-a5d5-a3c386dc1ffa" width="500" />
<img src="https://github.com/user-attachments/assets/5bf6ee64-3b69-4ef9-a1a9-f9aa0a769356" width="500" />
<img src="https://github.com/user-attachments/assets/786e85b6-2e0b-475c-afe4-3f9e9077935b" width="500" />
<img src="https://github.com/user-attachments/assets/2ee28607-6075-4bd5-a2d3-3799e55a17aa" width="500" />

---

## Kesimpulan

Berdasarkan tahapan yang telah dilakukan, aplikasi Flask berhasil dikemas menjadi **Containerized Application (CA)** menggunakan Docker. Proses ini memastikan aplikasi dapat dijalankan secara konsisten di berbagai lingkungan tanpa konfigurasi tambahan. Containerisasi mempermudah proses distribusi, deployment, serta pemeliharaan aplikasi dalam skala yang lebih besar.
