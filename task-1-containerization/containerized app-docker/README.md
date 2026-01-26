containerized app-docker

Membangun Containerized App - Docker  
`docker version`

<img width="662" height="605" alt="image" src="https://github.com/user-attachments/assets/5de9c40c-e2d3-4ec8-86c7-93d6bba8d5be" />


```
git clone https://github.com/pallets/flask
```
<img width="940" height="200" alt="image" src="https://github.com/user-attachments/assets/c88f655c-8690-463e-b6a1-08b4be9445c6" />

```
cd flask  
git tag
```
<img width="483" height="61" alt="image" src="https://github.com/user-attachments/assets/21df7a8f-6e6c-46d5-bf80-13d9f00bcaf7" />


```
rm -Recurse -Force flask
```

```
git checkout 3.1.2`
```
<img width="771" height="420" alt="image" src="https://github.com/user-attachments/assets/16642e77-6c37-4271-b187-3997ba2999ed" />

```
cp -R flask/examples/tutorial flask-app
```

```
ls
```
<img width="587" height="657" alt="image" src="https://github.com/user-attachments/assets/32d8af8e-de46-4a05-9b23-adeff8f50cc0" />

```
cd flask-app
```
<img width="808" height="80" alt="image" src="https://github.com/user-attachments/assets/14890043-30b9-4704-8bb3-d1850565795f" />


```
@"
FROM python:3.13
RUN mkdir /app
WORKDIR /app
ADD . /app/
RUN pip install -e .
RUN flask --app flaskr init-db
EXPOSE 5000
CMD ["flask", "--app", "flaskr", "run", "--host=0.0.0.0", "--port=5000"]
"@ | Out-File -Encoding ascii Dockerfile
```
<img width="940" height="265" alt="image" src="https://github.com/user-attachments/assets/3f676551-305c-463c-9422-e1637df94c4c" />

```
docker build -t flaskr:1.0.0 .
```
<img width="940" height="800" alt="image" src="https://github.com/user-attachments/assets/574ac673-2a3f-4072-9870-0daacbd5cac7" />

```
docker run -p 5001:5000 flaskr:1.0.0
```
<img width="940" height="229" alt="image" src="https://github.com/user-attachments/assets/8fe8f2a2-a017-458e-8cd6-316614f1f69e" />

> Kita bisa melihat aplikasi berjalan di browser:
> Disini kita harus membuka browser dan memasukan/ketik localhost:5001
<img width="940" height="427" alt="image" src="https://github.com/user-attachments/assets/e80c072f-8cb0-4937-87db-a61d8427ed95" />


> Untuk mencoba, pilih “Register” kemudian “Log In”
> Buat Aplikasi Menjadi CA
> Buat file utk build docker: Dockerfile. File diletakkan di direktori root dari aplikasi. Isinya adalah sebagai berikut:: 

```
@"
FROM python:3.13
RUN mkdir /app
WORKDIR /app
ADD . /app/
RUN pip install -e .
RUN flask --app flaskr init-db
EXPOSE 5000
CMD ["flask", "--app", "flaskr", "run", "--host=0.0.0.0", "--port=5000"]
"@ | Out-File -Encoding ascii Dockerfile
```
<img width="940" height="290" alt="image" src="https://github.com/user-attachments/assets/7a1036e2-6ce0-4617-9df9-c3c386b3cf53" />

> Membangun Ulang Image (Jika Ada Perubahan
```
docker build -t flaskr:1.0.0 .
```
<img width="940" height="517" alt="image" src="https://github.com/user-attachments/assets/7c5ccf76-fa4b-4897-8066-0c24668edd31" />

> Menjalankan Kembali Container
```
docker run -p 5001:5000 flaskr:1.0.0
```
<img width="940" height="181" alt="image" src="https://github.com/user-attachments/assets/553d2493-85f9-4494-9933-cbc548e4d6a1" />

> Tampilan
<img width="940" height="248" alt="image" src="https://github.com/user-attachments/assets/d2a420a2-20f3-43e8-8442-a1180bb5d734" />
<img width="940" height="221" alt="image" src="https://github.com/user-attachments/assets/9500b481-e100-49a6-9783-1d71f770b05f" />

> Melihat ID container yang sedang/pernah jalan
```
docker ps -a
```
<img width="940" height="175" alt="image" src="https://github.com/user-attachments/assets/5998da3c-6f8a-4e9f-b983-6a7434e79de2" />

```
docker image ls
```
> Image telah berhasil dibuat:
<img width="940" height="152" alt="image" src="https://github.com/user-attachments/assets/b5939e0f-b4e8-4420-a94f-a98c9dc132f0" />

> melihat log aktivitas di jendela
```
dokcer ps -a
```
<img width="940" height="177" alt="image" src="https://github.com/user-attachments/assets/23586686-dbbc-41e8-859e-2facb2720c83" />


> ketika Container di berhentikan, maka tampilan akan err connet
<img width="940" height="831" alt="image" src="https://github.com/user-attachments/assets/e42d9ec4-1486-41e3-8b14-5810b3868c3b" />


> Menjalankan Image Jalankan dengan memetakan port 5000 pada aplikasi di container ke port 5001 di localhost:
```
docker run -p 5001:5000 flaskr:1.0.0
```
<img width="940" height="169" alt="image" src="https://github.com/user-attachments/assets/449f9260-a2cd-43d1-98cf-3fc760280f88" />


> Akses web browser:
<img width="940" height="367" alt="image" src="https://github.com/user-attachments/assets/9e2ce060-6f17-452a-8ecc-67795b677f0e" />
<img width="940" height="246" alt="image" src="https://github.com/user-attachments/assets/d476a34f-9b0c-4cb3-addd-c32d4b785e2b" />

> Pada posisi ini, CA telah berhasil dijalankan.
> Pengguna dapat mendaftar, masuk, membuat postingan, dan mengedit atau menghapus postingan mereka sendiri. Anda dapat mengemas dan menginstal aplikasi di komputer lain
<img width="721" height="634" alt="image" src="https://github.com/user-attachments/assets/6280eec8-6d23-42cb-8f74-9549c818b72f" />
<img width="940" height="233" alt="image" src="https://github.com/user-attachments/assets/3b8d3106-b8e3-45c9-ad9f-458339e335df" />

> login
<img width="670" height="580" alt="image" src="https://github.com/user-attachments/assets/56a44e3d-ad96-4a8d-a5d5-a3c386dc1ffa" />
<img width="940" height="46" alt="image" src="https://github.com/user-attachments/assets/464e56e3-e753-4ff6-a53a-21a28caa7204" />

> berhasil login
<img width="688" height="301" alt="image" src="https://github.com/user-attachments/assets/5bf6ee64-3b69-4ef9-a1a9-f9aa0a769356" />
<img width="940" height="69" alt="image" src="https://github.com/user-attachments/assets/7b948704-f12a-403c-97e2-60a033223fbc" />


> membuat postingan
<img width="587" height="549" alt="image" src="https://github.com/user-attachments/assets/786e85b6-2e0b-475c-afe4-3f9e9077935b" />

> setelah membuat postingan dan kita diarahkan ke button save. maka akan menampilkan postingan
<img width="600" height="364" alt="image" src="https://github.com/user-attachments/assets/2ee28607-6075-4bd5-a2d3-3799e55a17aa" />
<img width="940" height="81" alt="image" src="https://github.com/user-attachments/assets/511ef5fb-6b9a-4fdb-a930-293bab2fafbb" />







































