# Langkah pengerjaan task1  

### Struktur direktori

<pre>
load-balancing-nginx/
├── app/
│   └── app.py
├── nginx/
│   └── nginx.conf
└── docker-compose.yml
</pre>

1. Membuat direktori
   - Membuat direktori baru dengan nama `load-balancing-nginx`  
   ```bash
   mkdir load-balancing-nginx  
   ```
   <img width="614" height="154" alt="image" src="https://github.com/user-attachments/assets/c2ae0372-642a-4b6a-89b0-1bf8fbab15a9" />

   - Membuat direktori dengan nama `app` dalam direktori `load-balancing-nginx`  
   ```bash
   mkdir load-balancing-nginx/app
   ```  
   <img width="633" height="156" alt="image" src="https://github.com/user-attachments/assets/12a1c078-8c95-4baf-b2e4-53d306ad5f8d" />

   - Membyat direktori dengan nama `nginx` dalam direktori `load-balancing-nginx`  
   ```bash
   mkdir load-balancing-nginx/nginx
   ```
   <img width="652" height="190" alt="image" src="https://github.com/user-attachments/assets/8a4a87bd-3db6-420e-83c1-d33bae1e2bfe" />  

2. Membuat file `app.py` dalam direktori `app`
   ```bash
   subl load-balancing-nginx/app/app.py
   ```  
   <img width="680" height="89" alt="image" src="https://github.com/user-attachments/assets/c16e2aa5-a7b2-4e1f-8392-2f34328e58c4" />

   **`app.py`**
   ```python
    from flask import Flask
    import socket
    
    app = Flask(__name__)
    
    @app.route("/")
    def hello():
        return f"Response from {socket.gethostname()}"
    
    if __name__ == "__main__":
        app.run(host="0.0.0.0", port=5000)
   ```

   <img width="665" height="266" alt="image" src="https://github.com/user-attachments/assets/c25f047c-a60e-46b7-99f7-f686f5b235a0" />


4. Membuat file konfigurasi nginx dengan nama **`nginx.conf`** dalam direktori **`nginx`**  
   ```bash
   subl load-balancing-nginx/nginx/nginx.conf
   ```
   <img width="720" height="47" alt="image" src="https://github.com/user-attachments/assets/fbb96302-7bd7-4067-aabd-3d8cbc4a6163" />  

   <img width="580" height="351" alt="image" src="https://github.com/user-attachments/assets/23564e40-8e8c-4132-a471-7c1a6bdd5cf2" />

5. Membuat file konfigurasi docker-compose.yml 
   ```bash
   subl load-balancing-nginx/docker-compose.yml
   ```  
   <img width="746" height="47" alt="image" src="https://github.com/user-attachments/assets/59562b21-2f75-4f1f-a7f1-8093104dc9fc" />

   **`docker-compose.yml`**  
   ```yml
   version: "3.8"
   
   services:
     app1:
       image: python:3.10-slim
       container_name: app1
       volumes:
         - ./app:/app
       working_dir: /app
       command: sh -c "pip install flask && python app.py"
   
     app2:
       image: python:3.10-slim
       container_name: app2
       volumes:
         - ./app:/app
       working_dir: /app
       command: sh -c "pip install flask && python app.py"
   
     nginx:
       image: nginx:latest
       container_name: nginx-lb
       ports:
         - "8081:80"
       volumes:
         - ./nginx/nginx.conf:/etc/nginx/nginx.conf
       depends_on:
         - app1
         - app2
   ```  
   
   <img width="742" height="578" alt="image" src="https://github.com/user-attachments/assets/43ad78d8-7552-4d59-98ab-aee171029c44" />  

6. Masuk ke direktori **`load-balancing-nginx`**   
   ```bash
   cd load-balancing-nginx
   ```  
   <img width="572" height="42" alt="image" src="https://github.com/user-attachments/assets/8e2b2302-990f-4e3d-96d3-0d135702f99f" />

7. Jalankan docker-compose
   ```bash
   docker-compose up
   ```  
   <img width="1347" height="324" alt="image" src="https://github.com/user-attachments/assets/3f04a8b9-bbeb-43f7-9311-f1a757dd0249" />

   <img width="1346" height="675" alt="image" src="https://github.com/user-attachments/assets/b8945aef-9c76-4b7e-a1f8-3fa5b3563743" />

8. Buka browser:  
   http://localhost:8081/  
   <img width="412" height="177" alt="image" src="https://github.com/user-attachments/assets/6c027c36-4077-4e40-9102-d2ca7f4889e8" />

Ketika halaman di-refresh beberapa kali, respon yang diterima berasal dari backend container yang berbeda, yang ditunjukkan melalui hostname container.  
<img alt="image" src="https://i.imgur.com/9XhbLgl.png" />

