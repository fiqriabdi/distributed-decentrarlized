# Membangun Containerized App - Podman 

> Inisialisasi mesin:
```
podman machine init
```
<img width="940" height="269" alt="image" src="https://github.com/user-attachments/assets/87261fb9-4689-4efd-a4ff-baa36b09f6f0" />

> Jalankan mesin:
```
podman machine start
```
<img width="600" height="619" alt="image" src="https://github.com/user-attachments/assets/9b3cd231-02be-4358-a746-e4303dbe01b1" />

<img width="797" height="508" alt="image" src="https://github.com/user-attachments/assets/5f48dccb-3889-43ec-9184-f947f595e843" />

> Build Ulang dengan Tag Localhost
```
podman build -t localhost/flaskr:1.0.0 .
```
<img width="600" height="338" alt="image" src="https://github.com/user-attachments/assets/0ee8c21d-8cd8-4dff-8a10-2dff16c2422b" />

<img width="600" height="360" alt="image" src="https://github.com/user-attachments/assets/dab03518-2dee-45b6-b068-f86ea40a76b3" />

> mengecek image
```
podman images
```
<img width="600" height="150" alt="image" src="https://github.com/user-attachments/assets/393be8cf-2f38-40f6-b79b-889367be341e" />

> Jalankan Container
```
podman run -p 8080:5000 --name flask-podman -d localhost/flaskr:1.0.0
```
<img width="600" height="75" alt="image" src="https://github.com/user-attachments/assets/6cdc09e9-391b-42e5-be06-e16511a3da01" />

```
podman build -t localhost/flaskr:1.0.0 .
```
<img width="600" height="644" alt="image" src="https://github.com/user-attachments/assets/01e73ff3-f025-46ce-8e8d-bbb17d98eeff" />

> Mengecek container
<img width="600" height="129" alt="image" src="https://github.com/user-attachments/assets/6cba2bd4-8bb2-43ff-bb05-c6ad312eb395" />

> Menjalankan Image Jalankan dengan memetakan port 8080 pada aplikasi di container ke port 5001 di localhost:
<img width="600" height="245" alt="image" src="https://github.com/user-attachments/assets/59f59c1e-2cab-4ab5-b210-e89e8ec9b4aa" />
> Pada posisi ini, CA telah berhasil dijalankan

> Gunakan perintah exec untuk membuka terminal interaktif di dalam container yang sedang berjalan:
```
podman exec -it flask-podman bash
```
> Cek posisi folder: ketik pwd di samping #
<img width="877" height="125" alt="image" src="https://github.com/user-attachments/assets/85e8927a-278a-4861-b8af-af075f1f0f64" />

> Lihat daftar file
```
ls -R
```
<img width="600" height="625" alt="image" src="https://github.com/user-attachments/assets/3a4a4f8b-f928-4e81-961c-63d3a13b4efd" />
> melihat struktur folder Flask

> Cek database
```
ls -l instance/flaskr.sqlite
```
<img width="940" height="100" alt="image" src="https://github.com/user-attachments/assets/ecfcd710-0cce-443b-ba2e-1665347992a5" />

> Memantau Log secara Real-time
<img width="600" height="290" alt="image" src="https://github.com/user-attachments/assets/33e545b7-cebf-434e-9764-cc297224434d" />
<img width="600" height="308" alt="image" src="https://github.com/user-attachments/assets/4b66b57d-f54e-4c2d-a5ff-6848aa4f3f95" />
<img width="500" height="744" alt="image" src="https://github.com/user-attachments/assets/8b938cd2-4462-4b61-8876-43a4fcb76363" />
<img width="500" height="181" alt="image" src="https://github.com/user-attachments/assets/92c97065-123c-43bd-ae7b-3be83e5e7ac1" />
<img width="500" height="621" alt="image" src="https://github.com/user-attachments/assets/1ce9c7db-4b5f-449c-a239-3f2b578c14a9" />
<img width="500" height="155" alt="image" src="https://github.com/user-attachments/assets/79233423-731d-4d5c-9129-11c24c11744b" />
> memgecek file

<img width="500" height="801" alt="image" src="https://github.com/user-attachments/assets/ddb3b1c0-414a-4a15-92f6-05c1f414ab4b" />








