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
    command: [ "bin/yugabyted", "start", "--daemon=false", "--listen=0.0.0.0" ]
```

---

## 6. Menjalankan YugabyteDB dengan Docker Compose

```bash
docker-compose up -d
```

```bash
docker ps
```

<img src="https://github.com/user-attachments/assets/7e6d289b-d95b-47a4-ae6a-0688d7fe0786" width="550" />

Masuk ke shell YSQL:

```bash
docker exec -it yugabyte-local bin/ysqlsh -h 127.0.0.1
```

---

## 7. Pengujian Hash dan Range Sharding

```sql
EXPLAIN ANALYZE SELECT * FROM mhs_hash WHERE nim BETWEEN 10 AND 20;
```

<img src="https://github.com/user-attachments/assets/43e48220-15de-463b-8bac-7f6d5e444b1d" width="550" />

```sql
EXPLAIN ANALYZE SELECT * FROM mhs_range WHERE nim BETWEEN 10 AND 20;
```

<img src="https://github.com/user-attachments/assets/4bd8f6d5-4ba2-4db5-b0e5-6f8a465099cf" width="550" />

---

## 8. Membuat Cluster YugabyteDB (3 Node)

### 8.1 Menghapus Container Lama

```bash
docker-compose down
```

### 8.2 Menjalankan Cluster 3 Node

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

## 9. Kesimpulan

Berdasarkan pengujian yang dilakukan, YugabyteDB berhasil dijalankan menggunakan Docker baik dalam mode **single-node** maupun **cluster multi-node**. Pengujian sharding (hash dan range) menunjukkan bahwa YugabyteDB mampu membaca data secara efisien sesuai dengan query yang diberikan tanpa harus melakukan full table scan.

Pendekatan container ini memudahkan proses instalasi, replikasi, dan pengujian database terdistribusi di berbagai lingkungan.
