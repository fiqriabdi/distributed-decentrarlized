# Membangun Containerized App Menggunakan Podman

Dokumen ini menjelaskan langkah-langkah membangun dan menjalankan aplikasi Flask dalam container menggunakan **Podman**, mulai dari inisialisasi mesin hingga pemantauan container.

---

## 1. Inisialisasi Mesin Podman

Langkah pertama adalah membuat *virtual machine* Podman (khususnya pada Windows/macOS).

```bash
podman machine init
```

<img src="https://github.com/user-attachments/assets/87261fb9-4689-4efd-a4ff-baa36b09f6f0" width="800" />

---

## 2. Menjalankan Mesin Podman

Setelah mesin dibuat, jalankan mesin Podman dengan perintah berikut:

```bash
podman machine start
```

<img src="https://github.com/user-attachments/assets/9b3cd231-02be-4358-a746-e4303dbe01b1" width="450" />

<img src="https://github.com/user-attachments/assets/5f48dccb-3889-43ec-9184-f947f595e843" width="550" />

---

## 3. Build Image Aplikasi Flask

Lakukan *build ulang* image aplikasi Flask dengan tag lokal `localhost`.

```bash
podman build -t localhost/flaskr:1.0.0 .
```

<img src="https://github.com/user-attachments/assets/0ee8c21d-8cd8-4dff-8a10-2dff16c2422b" width="550" />

<img src="https://github.com/user-attachments/assets/dab03518-2dee-45b6-b068-f86ea40a76b3" width="550" />

---

## 4. Mengecek Image yang Tersedia

Gunakan perintah berikut untuk memastikan image berhasil dibuat:

```bash
podman images
```

<img src="https://github.com/user-attachments/assets/393be8cf-2f38-40f6-b79b-889367be341e" width="550" />

---

## 5. Menjalankan Container

Jalankan container dari image yang telah dibuat dengan pemetaan port:

```bash
podman run -p 8080:5000 --name flask-podman -d localhost/flaskr:1.0.0
```

<img src="https://github.com/user-attachments/assets/6cdc09e9-391b-42e5-be06-e16511a3da01" width="550" />

> Pada tahap ini, **Containerized Application (CA)** berhasil dijalankan.

---

## 6. Mengecek Container yang Berjalan

Pastikan container berjalan dengan baik:

```bash
podman ps
```

<img src="https://github.com/user-attachments/assets/6cba2bd4-8bb2-43ff-bb05-c6ad312eb395" width="550" />

---

## 7. Akses Terminal Interaktif Container

Masuk ke dalam container menggunakan perintah `exec`:

```bash
podman exec -it flask-podman bash
```

### 7.1 Cek Direktori Aktif

```bash
pwd
```

<img src="https://github.com/user-attachments/assets/85e8927a-278a-4861-b8af-af075f1f0f64" width="800" />

### 7.2 Melihat Struktur Folder Aplikasi Flask

```bash
ls -R
```

<img src="https://github.com/user-attachments/assets/3a4a4f8b-f928-4e81-961c-63d3a13b4efd" width="550" />

---

## 8. Mengecek Database Aplikasi

Periksa keberadaan database SQLite yang digunakan oleh Flask:

```bash
ls -l instance/flaskr.sqlite
```

<img src="https://github.com/user-attachments/assets/ecfcd710-0cce-443b-ba2e-1665347992a5" width="800" />

---

## 9. Memantau Log Container Secara Real-Time

Gunakan perintah berikut untuk melihat log aplikasi:

```bash
podman logs -f flask-podman
```

<img src="https://github.com/user-attachments/assets/33e545b7-cebf-434e-9764-cc297224434d" width="550" />
<img src="https://github.com/user-attachments/assets/4b66b57d-f54e-4c2d-a5ff-6848aa4f3f95" width="550" />
<img src="https://github.com/user-attachments/assets/8b938cd2-4462-4b61-8876-43a4fcb76363" width="450" />
<img src="https://github.com/user-attachments/assets/92c97065-123c-43bd-ae7b-3be83e5e7ac1" width="450" />
<img src="https://github.com/user-attachments/assets/1ce9c7db-4b5f-449c-a239-3f2b578c14a9" width="450" />
<img src="https://github.com/user-attachments/assets/79233423-731d-4d5c-9129-11c24c11744b" width="450" />

---

## 10. Pemeriksaan File di Dalam Container

<img src="https://github.com/user-attachments/assets/ddb3b1c0-414a-4a15-92f6-05c1f414ab4b" width="450" />

---

## Kesimpulan

Berdasarkan langkah-langkah di atas, dapat disimpulkan bahwa aplikasi Flask berhasil dikemas (*containerized*) menggunakan Podman, dijalankan dengan baik, serta dapat diakses dan dimonitor melalui container yang aktif.
