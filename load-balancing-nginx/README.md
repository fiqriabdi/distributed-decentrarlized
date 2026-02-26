
# Task1: Load Balancing menggunakan Nginx

## Deskripsi
Task ini bertujuan untuk mengimplementasikan konsep load balancing sebagai bagian dari sistem terdistribusi. Nginx digunakan sebagai load balancer untuk mendistribusikan request ke beberapa backend service yang berjalan pada container Docker.

## Konsep Dasar
Load balancing adalah teknik untuk mendistribusikan beban kerja ke beberapa server agar:
- Meningkatkan ketersediaan layanan
- Menghindari beban berlebih pada satu server
- Meningkatkan performa sistem

Dalam implementasi ini:
- Nginx bertindak sebagai reverse proxy dan load balancer
- Dua backend application berjalan pada container terpisah
- Semua service berjalan dalam satu jaringan virtual Docker

## Arsitektur Sistem   
```mermaid
flowchart TD
    Client[Client] --> Nginx[Nginx Load Balancer]
    Nginx --> App1[App-1 Container]
    Nginx --> App2[App-2 Container]
    style Nginx stroke:#333,stroke-width:2px
    style App1 stroke:#333
    style App2 stroke:#333
```


## Implementasi  
Implementasi dilakukan menggunakan Docker Compose untuk menjalankan beberapa container sekaligus, yaitu:
- 1 container Nginx
- 2 container backend application

Link implementasi: [langkah-task1.md](langkah-task1.md)

Nginx dikonfigurasi menggunakan metode default (round-robin) untuk mendistribusikan request ke backend.

## Hasil
Aplikasi dapat diakses melalui:  
http://localhost:8081/  
<img width="393" height="156" alt="image" src="https://github.com/user-attachments/assets/45771e6b-ca84-44a6-a5d9-1273b6d23b3a" />

Ketika halaman di-refresh beberapa kali, respon yang diterima berasal dari backend container yang berbeda, yang ditunjukkan melalui hostname container. Hal ini membuktikan bahwa proses load balancing berjalan dengan baik.  
<img alt="image" src="https://i.imgur.com/9XhbLgl.png" />

