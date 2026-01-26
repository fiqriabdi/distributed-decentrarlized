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
<img width="940" height="160" alt="image" src="https://github.com/user-attachments/assets/f31c9e41-566a-4e38-ab32-2e61a8bc5773" 




