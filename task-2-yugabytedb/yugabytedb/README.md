###
Instalasi Untuk instalasi, ekstraksi hasil unduhan tersebut kemudian letakkan pada subdirektori tertentu, buat symlink (jika diperlukan), buat file untuk env variables. Setelah itu, setiap akan mengaktifkan YugabyteDB pada setiap shell, source file env variables tersebut.
###


```
docker pull software.yugabyte.com/yugabytedb/yugabyte:2025.2.0.0-b131
```
<img width="941" height="457" alt="image" src="https://github.com/user-attachments/assets/20a164f3-ff23-4923-ab72-8784825780f5" />

> proses Pull Image telah berhasil



menjalakan container 
```
docker run -d --name yugabyte -p7000:7000 -p9000:9000 -p15433:15433 -p5433:5433 -p9042:9042  yugabytedb/yugabyte:2025.2.0.0-b131 bin/yugabyted start  --background=false
```
<img width="600" height="160" alt="image" src="https://github.com/user-attachments/assets/d788fe1c-87e0-49ff-af56-8bb08adce333" />

```
docker ps
```

<sub> memeriksa status klaster
</sub>

<img width="600" height="200" alt="image" src="https://github.com/user-attachments/assets/cb8fbd5b-b4d3-4ead-81a5-b5c266f97d6b" />


<sub> ekstraksi
</sub>

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

***
membuat tabel



```
Membuat tabel dengan 3 tablet 
CREATE TABLE produk (
    id INT PRIMARY KEY,
    nama TEXT,
    harga INT
) SPLIT INTO 3 TABLETS;
```
<img width="300" height="205" alt="image" src="https://github.com/user-attachments/assets/716a94f8-a9bc-407d-bbf8-57066d9433b2" />


masukan data sampel
```
INSERT INTO produk (id, nama, harga) VALUES 
(1, 'Laptop', 15000000),
(2, 'Mouse', 200000),
(3, 'Keyboard', 500000),
(4, 'Monitor', 2500000),
(5, 'Webcam', 800000);
```
<img width="400" height="277" alt="image" src="https://github.com/user-attachments/assets/71a04e99-1871-4854-99f5-f81b989cc1a8" />

***

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
<img width="400" height="456" alt="image" src="https://github.com/user-attachments/assets/f4d8697a-7bce-4e05-a4a8-fa91a526f4e5" />

```
cd yugabyte-lab
```

<sub> buat file .env
</sub>

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
<img width="400" height="278" alt="image" src="https://github.com/user-attachments/assets/63b1cd1b-469d-4bcf-85fd-1b92fb155683" />

<sub> 
buat file docker-compose.yml
</sub>

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
<img width="400" height="342" alt="image" src="https://github.com/user-attachments/assets/06abb98f-6698-4862-8d0f-6bbf4141ca1a" />

<sub> 
jalankan Docker Compose
</sub>

```
docker-compose up -d
```
<img width="600" height="235" alt="image" src="https://github.com/user-attachments/assets/944252e8-dea2-4efc-b8f3-356d325002f5" />

```
docker ps
```
<img width="600" height="117" alt="image" src="https://github.com/user-attachments/assets/7e6d289b-d95b-47a4-ae6a-0688d7fe0786" />

<sub> 
masuk ke Shell YSQL
</sub>
    
```
docker exec -it yugabyte-local bin/ysqlsh -h 127.0.0.1
```
<img width="600" height="571" alt="image" src="https://github.com/user-attachments/assets/ed94292b-be3c-423f-8575-9fb5cdc9a0d4" />

```
EXPLAIN ANALYZE SELECT * FROM mhs_hash WHERE nim BETWEEN 10 AND 20;
```
<sub> 
uji has sharding
</sub>

<img width="600" height="219" alt="image" src="https://github.com/user-attachments/assets/43e48220-15de-463b-8bac-7f6d5e444b1d" />

 <sub> 
 uji range sharding
 </sub>

```
EXPLAIN ANALYZE SELECT * FROM mhs_range WHERE nim BETWEEN 10 AND 20;
```
<img width="600" height="175" alt="image" src="https://github.com/user-attachments/assets/4bd8f6d5-4ba2-4db5-b0e5-6f8a465099cf" />

<sub> 
menjalanlan/cek status
</sub>

```
docker exec -it yugabyte-local bin/yugabyted status
```
<img width="940" height="300" alt="image" src="https://github.com/user-attachments/assets/214b250a-7dad-4c61-99ea-0f90545039d1" />

> membuat klaster baru

> hapus container lama dan jalankan klaster 3-node ini

* hapus container lama
  ```
  docker-compose down
  ```
  <img width="940" height="229" alt="image" src="https://github.com/user-attachments/assets/b44a47a3-0aa0-48f3-8efc-380c481a01d5" />

* jalankan klaster 3 n-node
  ```
  docker-compose up -d
  ```

  
  ```
  * Node 1
  docker run -d --name yb-master-n1 --network yb-net `
  -p 7000:7000 -p 5433:5433 -p 9042:9042 `
  yugabytedb/yugabyte:latest
  bin/yugabyted start --daemon=false --listen=yb-master-n1
```
* Jalankan Node 2
  
docker run -d --name yb-master-n2 --network yb-net `
  yugabytedb/yugabyte:latest `
  bin/yugabyted start --daemon=false --join=yb-master-n1 --listen=yb-master-n2
```
```
* Jalankan Node 3
docker run -d --name yb-master-n3 --network yb-net `
  yugabytedb/yugabyte:latest `
  bin/yugabyted start --daemon=false --join=yb-master-n1 --listen=yb-master-n3
```
<img width="600" height="335" alt="image" src="https://github.com/user-attachments/assets/80627a1c-b42e-4a9f-80a2-2e4ac2ee9dd7" />


> memgecek status dengan node 1
```
docker exec -it yb-master-n1 bin/yugabyted status
```
<img width="940" height="273" alt="image" src="https://github.com/user-attachments/assets/06f23c73-5f17-4090-8f0a-814f77872586" />


> memgecek status dengan node 2
```
docker exec -it yb-master-n2 bin/yugabyted status
```
<img width="600" height="271" alt="image" src="https://github.com/user-attachments/assets/53bb4c3b-4bd3-4aba-97ba-ceb7e465abf5" />

> memgecek status dengan node 3
```
docker exec -it yb-master-n3 bin/yugabyted status
```
<img width="500" height="298" alt="image" src="https://github.com/user-attachments/assets/98f04c6e-aff5-45f3-a779-f023a89a0fac" />

**
raange sharding secara default akan membentuk hanya 1 tablet untuk 1 tabel: 
**
```
docker exec -it yb-master-n1 bin/ysqlsh -h yb-master-n1 -U yugabyte -d yugabyte
```
<img width="500" height="238" alt="image" src="https://github.com/user-attachments/assets/6905931f-29c5-4937-96ba-a41e72c3b7a7" />

<sub>
membuat nama tabel dengan nama users 
</sub>

```
CREATE TABLE users ( user_id INT, username VARCHAR, PRIMARY KEY (user_id ASC) ) SPLIT AT VALUES ((1000), (2000), (3000));
```

<sub>
setelah itu memangil/cek users dan user_id
</sub>

```
EXPLAIN (ANALYZE, DIST, COSTS OFF) SELECT * FROM users;
```
<img width="500" height="621" alt="image" src="https://github.com/user-attachments/assets/2a0adf69-04a4-4a16-a60e-c4c22680d0b6" />


```
EXPLAIN (ANALYZE, DIST, COSTS OFF) SELECT FROM users WHERE user_id = 1;
```
<img width="500" height="521" alt="image" src="https://github.com/user-attachments/assets/c946986b-9322-4446-baca-962a04268118" />

```
EXPLAIN (ANALYZE, DIST, COSTS OFF) SELECT * FROM users WHERE user_id >= 2 AND user_id < 6;
```
<img width="940" height="408" alt="image" src="https://github.com/user-attachments/assets/c34b5adc-36c6-4bdd-add6-2d502353daa7" />

```
EXPLAIN (ANALYZE, DIST, COSTS OFF) SELECT * FROM users WHERE user_id BETWEEN 2 AND 6;
```
<img width="940" height="433" alt="image" src="https://github.com/user-attachments/assets/82c76c14-1df2-48cd-b47c-cf348376e929" />

<sub
Untuk query  (semua baris): data yang dibaca semua baris 2. Untuk query satu baris: data yang dibaca 1 baris 3. Untuk query range (lebih dari 1 baris): data yang dibaca persis sama dengan jumlah yang sesuai (tidak membaca semua data). 
</sub>

<sub>
untuk melihat jumlah tablet pada tabel, gunakan perintah berikut
</sub>

```
\d
```
<img width="738" height="339" alt="image" src="https://github.com/user-attachments/assets/ed1f39f8-b065-4337-9540-55df937a2206" />
> menampilkan jumlah tablet
> jika list of relations tabel ini kosong, kita buat tabel baru
```
docker exec -it yb-master-n1 bin/ysqlsh -h yb-master-n1 -U yugabyte -d yugabyte
```

> membuat tabel users
```
CREATE TABLE users (
    id INT PRIMARY KEY,
    nama TEXT
) SPLIT INTO 3 TABLETS;

> masukan nama tabel baru
```

> membuat tabel users_hash
```
CREATE TABLE user_hash (
    id INT PRIMARY KEY,
    nama TEXT
) SPLIT INTO 3 TABLETS;
```

<sub>
mengecek tabel range dan tabel hash
</sub>

```
SELECT * FROM yb_table_properties('user_range'::regclass);
```
<img width="940" height="258" alt="image" src="https://github.com/user-attachments/assets/40a77bbc-431f-4835-84f3-4615ce7b68d6" />

```
SELECT * FROM yb_table_properties('user_hash'::regclass);
```
<img width="940" height="258" alt="image" src="https://github.com/user-attachments/assets/d8791f29-9d3c-4b03-930a-3721d03bbc7c" />



*




















