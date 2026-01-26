> Instalasi Untuk instalasi, ekstraksi hasil unduhan tersebut kemudian letakkan pada subdirektori tertentu, buat symlink (jika diperlukan), buat file untuk env variables. Setelah itu, setiap akan mengaktifkan YugabyteDB pada setiap shell, source file env variables tersebut.

```
docker pull software.yugabyte.com/yugabytedb/yugabyte:2025.2.0.0-b131
```
<img width="941" height="457" alt="image" src="https://github.com/user-attachments/assets/20a164f3-ff23-4923-ab72-8784825780f5" />
> Proses Pull Image telah berhasil

> menjalankan container

```
docker run -d --name yugabyte -p7000:7000 -p9000:9000 -p15433:15433 -p5433:5433 -p9042:9042  yugabytedb/yugabyte:2025.2.0.0-b131 bin/yugabyted start  --background=false
```
<img width="600" height="160" alt="image" src="https://github.com/user-attachments/assets/d788fe1c-87e0-49ff-af56-8bb08adce333" />

```
docker ps
```
> memeriksa status klaster
<img width="600" height="200" alt="image" src="https://github.com/user-attachments/assets/cb8fbd5b-b4d3-4ead-81a5-b5c266f97d6b" />

> Ekstraksi
```
docker exec -it yugabyte ls -l /home/yugabyte/bin
```
<img width="600" height="879" alt="image" src="https://github.com/user-attachments/assets/5d688b5a-f0dc-429a-beb1-1c3c8411d106" />

```
docker exec -it yugabyte bash -c '/home/yugabyte/bin/ysqlsh --echo-queries --host $(hostname)'
```
<img width="600" height="92" alt="image" src="https://github.com/user-attachments/assets/dc3a5ac4-75e7-48d6-8d21-6e0a882a0c05" />

```
docker exec -it yugabyte ln -s /home/yugabyte/bin/ysqlsh /usr/local/bin/ysql
```

```
docker exec -it yugabyte-local bin/ysqlsh -h 172.17.0.3
```
<img width="600" height="77" alt="image" src="https://github.com/user-attachments/assets/e1c2bac4-57d4-4b14-b13d-253ca7cd8cd9" />

> membuat tabel
```
Membuat tabel dengan 3 tablet 
CREATE TABLE produk (
    id INT PRIMARY KEY,
    nama TEXT,
    harga INT
) SPLIT INTO 3 TABLETS;
```
<img width="400" height="205" alt="image" src="https://github.com/user-attachments/assets/716a94f8-a9bc-407d-bbf8-57066d9433b2" />

> masukan data sampel
```
INSERT INTO produk (id, nama, harga) VALUES 
(1, 'Laptop', 15000000),
(2, 'Mouse', 200000),
(3, 'Keyboard', 500000),
(4, 'Monitor', 2500000),
(5, 'Webcam', 800000);
```
<img width="400" height="277" alt="image" src="https://github.com/user-attachments/assets/71a04e99-1871-4854-99f5-f81b989cc1a8" />

```
EXPLAIN ANALYZE SELECT * FROM produk;
```
<img width="600" height="271" alt="image" src="https://github.com/user-attachments/assets/6026f1c8-fd07-4a58-af1b-25fb2d1129cb" />

```
EXPLAIN ANALYZE SELECT * FROM produk WHERE id = 3;
```
<img width="600" height="288" alt="image" src="https://github.com/user-attachments/assets/2eceb5ed-1a1f-46bf-8683-786202b7a241" />

> membuat folder
```
mkdir yugabyte-lab
```
<img width="600" height="456" alt="image" src="https://github.com/user-attachments/assets/f4d8697a-7bce-4e05-a4a8-fa91a526f4e5" />

```
cd yugabyte-lab
```

> Buat file .env
```
@'
CONTAINER_NAME=yugabyte-local
YB_IMAGE=yugabytedb/yugabyte:latest
YSQL_PORT=5433
YCQL_PORT=9042
MASTER_UI_PORT=7000
TSERVER_UI_PORT=9000
'@ | Out-File -FilePath .env -Encoding utf8
```
<img width="600" height="278" alt="image" src="https://github.com/user-attachments/assets/63b1cd1b-469d-4bcf-85fd-1b92fb155683" />

> buat file docker-compose.yml
```
@'
version: '3.8'
services:
  yugabyte:
    image: ${YB_IMAGE}
    container_name: ${CONTAINER_NAME}
    ports:
      - "${YSQL_PORT}:5433"
      - "${YCQL_PORT}:9042"
      - "${MASTER_UI_PORT}:7000"
      - "${TSERVER_UI_PORT}:9000"
    command: [ "bin/yugabyted", "start", "--daemon=false", "--listen=0.0.0.0" ]
'@ | Out-File -FilePath docker-compose.yml -Encoding utf8
```
<img width="600" height="342" alt="image" src="https://github.com/user-attachments/assets/06abb98f-6698-4862-8d0f-6bbf4141ca1a" />

> jalankan Docker Compose
```
docker-compose up -d
```
<img width="600" height="235" alt="image" src="https://github.com/user-attachments/assets/944252e8-dea2-4efc-b8f3-356d325002f5" />

```
docker ps
```
<img width="600" height="117" alt="image" src="https://github.com/user-attachments/assets/7e6d289b-d95b-47a4-ae6a-0688d7fe0786" />

> masuk ke Shell YSQL
```
docker exec -it yugabyte-local bin/ysqlsh -h 127.0.0.1
```
<img width="600" height="571" alt="image" src="https://github.com/user-attachments/assets/ed94292b-be3c-423f-8575-9fb5cdc9a0d4" />

```
EXPLAIN ANALYZE SELECT * FROM mhs_hash WHERE nim BETWEEN 10 AND 20;
```
> uji has sharding
<img width="600" height="219" alt="image" src="https://github.com/user-attachments/assets/43e48220-15de-463b-8bac-7f6d5e444b1d" />

 > uji range sharding
```
EXPLAIN ANALYZE SELECT * FROM mhs_range WHERE nim BETWEEN 10 AND 20;
```
<img width="600" height="175" alt="image" src="https://github.com/user-attachments/assets/4bd8f6d5-4ba2-4db5-b0e5-6f8a465099cf" />







