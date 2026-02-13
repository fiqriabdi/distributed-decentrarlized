# Instalasi dan Pengujian YugabyteDB Menggunakan Docker

Dokumen ini menjelaskan proses **instalasi, konfigurasi, dan pengujian YugabyteDB** menggunakan **Docker**, mulai dari menjalankan single-node hingga cluster multi-node, serta pengujian sharding (hash dan range).

---

## 1. Instalasi YugabyteDB (Docker Image)

Unduh image resmi YugabyteDB dari registry:

```bash
docker pull software.yugabyte.com/yugabytedb/yugabyte:2025.2.0.0-b131
```

<img src="https://github.com/user-attachments/assets/20a164f3-ff23-4923-ab72-8784825780f5" width="850" />

> Proses *pull image* berhasil dilakukan.

---

## 2. Menjalankan YugabyteDB (Single Node)

Jalankan container YugabyteDB dengan pemetaan port penting:

```bash
docker run -d --name yugabyte \
-p 7000:7000 \
-p 9000:9000 \
-p 15433:15433 \
-p 5433:5433 \
-p 9042:9042 \
yugabytedb/yugabyte:2025.2.0.0-b131 \
bin/yugabyted start --background=false
```

<img src="https://github.com/user-attachments/assets/d788fe1c-87e0-49ff-af56-8bb08adce333" width="550" />

Cek status container:

```bash
docker ps
```

<img src="https://github.com/user-attachments/assets/cb8fbd5b-b4d3-4ead-81a5-b5c266f97d6b" width="550" />

---

## 3. Akses Shell YugabyteDB

Melihat isi direktori binary Yugabyte:

```bash
docker exec -it yugabyte ls -l /home/yugabyte/bin
```

<img src="https://github.com/user-attachments/assets/5d688b5a-f0dc-429a-beb1-1c3c8411d106" width="550" />

Masuk ke shell YSQL:

```bash
docker exec -it yugabyte bash -c '/home/yugabyte/bin/ysqlsh --echo-queries --host $(hostname)'
```

<img src="https://github.com/user-attachments/assets/dc3a5ac4-75e7-48d6-8d21-6e0a882a0c05" width="550" />

Membuat *symbolic link*:

```bash
docker exec -it yugabyte ln -s /home/yugabyte/bin/ysqlsh /usr/local/bin/ysql
```

---

## 4. Membuat dan Menguji Tabel (Sharding)

### 4.1 Membuat Tabel dengan 3 Tablet

```sql
CREATE TABLE produk (
    id INT PRIMARY KEY,
    nama TEXT,
    harga INT
) SPLIT INTO 3 TABLETS;
```

<img src="https://github.com/user-attachments/assets/716a94f8-a9bc-407d-bbf8-57066d9433b2" width="350" />

### 4.2 Menambahkan Data Sampel

```sql
INSERT INTO produk (id, nama, harga) VALUES
(1, 'Laptop', 15000000),
(2, 'Mouse', 200000),
(3, 'Keyboard', 500000),
(4, 'Monitor', 2500000),
(5, 'Webcam', 800000);
```

<img src="https://github.com/user-attachments/assets/71a04e99-1871-4854-99f5-f81b989cc1a8" width="450" />

### 4.3 Analisis Query

```sql
EXPLAIN ANALYZE SELECT * FROM produk;
```

```sql
EXPLAIN ANALYZE SELECT * FROM produk WHERE id = 3;
```

<img src="https://github.com/user-attachments/assets/6026f1c8-fd07-4a58-af1b-25fb2d1129cb" width="550" />
<img src="https://github.com/user-attachments/assets/2eceb5ed-1a1f-46bf-8683-786202b7a241" width="550" />

---

## 5. Menggunakan Docker Compose

### 5.1 Membuat Direktori dan File Konfigurasi

```bash
mkdir yugabyte-lab
cd yugabyte-lab
```

Buat file **.env**:

```env
CONTAINER_NAME=yugabyte-local
YB_IMAGE=yugabytedb/yugabyte:latest
YSQL_PORT=5433
YCQL_PORT=9042
MASTER_UI_PORT=7000
TSERVER_UI_PORT=9000
```

Buat file **docker-compose.yml**:

```yaml
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
    command: ["bin/yugabyted", "start", "--daemon=false", "--listen=0.0.0.0"]
```

---

## 6. Menjalankan YugabyteDB dengan Docker Compose

```bash
docker-compose up -d
docker ps
```

<img src="https://github.com/user-attachments/assets/7e6d289b-d95b-47a4-ae6a-0688d7fe0786" width="650" />

Masuk ke shell YSQL:

```bash
docker exec -it yugabyte-local bin/ysqlsh -h 127.0.0.1
```

---

## 7. Pengujian Hash dan Range Sharding

```sql
EXPLAIN ANALYZE SELECT * FROM mhs_hash WHERE nim BETWEEN 10 AND 20;
```

<img width="550" height="219" alt="image" src="https://github.com/user-attachments/assets/50053a2d-41d1-4871-b790-ef50825afc92" />


```sql
EXPLAIN ANALYZE SELECT * FROM mhs_range WHERE nim BETWEEN 10 AND 20;
```

<img width="550" height="175" alt="image" src="https://github.com/user-attachments/assets/920d0c94-1a28-4284-9e0b-3c6532128127" />

---


## Cara Mengecek Status Yugabyte

```bash
docker exec -it yugabyte-local bin/yugabyted status
```

<img width="550" height="300" alt="image" src="https://github.com/user-attachments/assets/c1026d67-0769-4916-bac1-c3a0927f0875" />

---

## 8. Membuat Cluster YugabyteDB (3 Node)

### 8.1 Menghapus Container Lama

```bash
docker-compose down
```

<img width="550" height="229" alt="image" src="https://github.com/user-attachments/assets/9e8a72be-af85-4441-8f11-8cd62a7bb1b0" />


### 8.2 Menjalankan Cluster 3 Node

```
docker-compose up -d
```

<img width="550" height="335" alt="image" src="https://github.com/user-attachments/assets/cb9eddbc-38bc-4545-b682-ec2b22ffe0b6" />


**Node 1**

```bash
docker run -d --name yb-master-n1 --network yb-net \
-p 7000:7000 -p 5433:5433 -p 9042:9042 \
yugabytedb/yugabyte:latest \
bin/yugabyted start --daemon=false --listen=yb-master-n1
```

**Node 2**

```bash
docker run -d --name yb-master-n2 --network yb-net \
yugabytedb/yugabyte:latest \
bin/yugabyted start --daemon=false --join=yb-master-n1 --listen=yb-master-n2
```

**Node 3**

```bash
docker run -d --name yb-master-n3 --network yb-net \
yugabytedb/yugabyte:latest \
bin/yugabyted start --daemon=false --join=yb-master-n1 --listen=yb-master-n3
```

---

### Mengecek Status ketiga klaster Yugabyte 

**Node 1**

```
docker exec -it yb-master-n1 bin/yugabyted status
```

<img width="550" height="273" alt="image" src="https://github.com/user-attachments/assets/ff620c1f-afc6-477c-b33b-2c8614410eff" />


**Node 2**

```
docker exec -it yb-master-n2 bin/yugabyted status
```

<img width="550" height="271" alt="image" src="https://github.com/user-attachments/assets/0e2f21d9-be32-4c30-8232-4e40737b2f83" />


**Node 3**
```
docker exec -it yb-master-n3 bin/yugabyted status
```

<img width="550" height="298" alt="image" src="https://github.com/user-attachments/assets/881fe1c8-8154-47e8-b922-cd7d8e506c7a" />

---


## 9. Pengujian Range Sharding

### Membuat Table Users

```
docker exec -it yb-master-n1 bin/ysqlsh -h yb-master-n1 -U yugabyte -d yugabyte
```

<img width="550" height="238" alt="image" src="https://github.com/user-attachments/assets/23225e63-cddf-4329-b1dc-caf1c28e8152" />



```sql
CREATE TABLE users (
    user_id INT,
    username VARCHAR,
    PRIMARY KEY (user_id ASC)
) SPLIT AT VALUES ((1000), (2000), (3000));
```

### Pengujian Query

```sql
EXPLAIN (ANALYZE, DIST, COSTS OFF) SELECT * FROM users;
```
<img width="550" height="621" alt="image" src="https://github.com/user-attachments/assets/00291b17-9e6b-4ad1-8867-69f23a8f42a6" />


```sql
EXPLAIN (ANALYZE, DIST, COSTS OFF) SELECT * FROM users WHERE user_id = 1;
```
<img width="550" height="521" alt="image" src="https://github.com/user-attachments/assets/ea39061b-0361-42cf-b3e6-cc651a1e66a4" />



```sql
EXPLAIN (ANALYZE, DIST, COSTS OFF) SELECT * FROM users WHERE user_id >= 2 AND user_id < 6;
```
<img width="550" height="408" alt="image" src="https://github.com/user-attachments/assets/59eaee06-c9fd-4dda-8ef6-f354839bf408" />



```sql
EXPLAIN (ANALYZE, DIST, COSTS OFF) SELECT * FROM users WHERE user_id BETWEEN 2 AND 6;
```
<img width="550" height="433" alt="image" src="https://github.com/user-attachments/assets/f5e15641-25fa-450e-aad1-592320450c03" />



### Hasil Pengamatan

1. Untuk query semua baris (`SELECT *`): data yang dibaca adalah seluruh baris.
2. Untuk query satu baris: data yang dibaca hanya 1 baris.
3. Untuk query range: data yang dibaca persis sesuai jumlah yang diminta (tidak membaca seluruh tabel).

---

## Mengecek Jumlah Tablet

```sql
\d
```
<img width="550" height="339" alt="image" src="https://github.com/user-attachments/assets/c73385ea-db18-45e5-bb21-3d62230c2bd6" />



## Mengecek Tabel Range

```sql
SELECT * FROM yb_table_properties('user_range'::regclass);
```
<img width="550" height="258" alt="image" src="https://github.com/user-attachments/assets/54d6318f-bb06-4660-9932-641b0a76963b" />

---

## Membuat dan Mengecek Tabel Hash

```sql
CREATE TABLE user_hash (
    id INT PRIMARY KEY,
    nama TEXT
) SPLIT INTO 3 TABLETS;
```

```sql
SELECT * FROM yb_table_properties('user_hash'::regclass);
```
<img width="550" height="258" alt="image" src="https://github.com/user-attachments/assets/d6dfa141-be7e-4ea7-8101-c4735bb6ca7b" />



---

## 10. Kesimpulan

Berdasarkan pengujian yang dilakukan, YugabyteDB berhasil dijalankan menggunakan Docker baik dalam mode **single-node** maupun **cluster multi-node**.  

Pengujian sharding (hash dan range) menunjukkan bahwa YugabyteDB mampu membaca data secara efisien sesuai dengan query yang diberikan tanpa harus melakukan full table scan.

Pendekatan container ini memudahkan proses instalasi, replikasi, dan pengujian database terdistribusi di berbagai lingkungan.
