# 3-Tier-Application-using-Docker-Compose 🐳

This project demonstrates a **3-Tier Architecture** implemented using **Docker, Docker Compose, Dockerfile, and database initialization scripts (`init.sql`)**.  
Each tier runs in a separate container and communicates over isolated Docker networks.

---

## 🏗️ 3-Tier Architecture

1. **Web Tier** – Handles client requests (Frontend / NGINX)
2. **Application Tier** – Backend application logic (PHP-FPM)
3. **Database Tier** – Persistent data storage (MySQL)

![](./Image/3-tier%20arch%20.png)

---

## 📁 Project Directory Structure

      
    ── app/
        └── code/ # Backend application source code
    ─ web/
        └── code/ # Frontend application files
        └── config/ # NGINX configuration files   
    ── db/
        ├── Dockerfile # Custom MySQL image
        └── init.sql # Database initialization script


                ┌──────────────────────┐
                │      Client / User   │
                │   (Browser / HTTP)   │
                └──────────┬───────────┘
                           │
                           ▼
                ┌──────────────────────┐
                │      Web Tier        │
                │     (NGINX)          │
                │      Frontend        │
                └──────────┬───────────┘
                           │
                           ▼
                ┌──────────────────────┐
                │   Application Tier   │
                │     (PHP-FPM)        │
                │                      │
                └──────────┬───────────┘
                           │
                           ▼
                ┌──────────────────────┐
                │    Database Tier     │
                │      (MySQL)         │
                │                      │
                └──────────────────────┘

 ![](./Image/Screenshot%202026-01-16%20194840.png)   

 
---

## 🐳 Docker Compose Services

- **Web Container (NGINX)**  
  Serves frontend content and forwards requests to the application tier.

- **App Container (PHP-FPM)**  
  Runs backend business logic and communicates with the database.

- **Database Container (MySQL)**  
  Stores persistent application data using Docker volumes.

All containers communicate through Docker networks for security and scalability.

---

## ⚙️ docker-compose.yml

```bash
services:
  web:
    image: nginx
    ports:
      - "80:80"
    volumes:
      - ./web/code/:/usr/share/nginx/html/
      - ./web/config/:/etc/nginx/conf.d/
    networks:
      - webnet
    depends_on:
      - app
      - db

  app:
    image: bitnami/php-fpm
    volumes:
      - ./app/code/:/app
    networks:
      - webnet
      - dbnet
    depends_on:
      - db

  db:
    build: ./db/
    volumes:
      - vol1:/var/lib/mysql/
    networks:
      - dbnet

networks:
  webnet:
  dbnet:

volumes:
  vol1:
```

## 🐬 Database Initialization (init.sql)

The database is automatically initialized using **`init.sql`** during container startup.

This script can include:
- Database creation
- Table schemas
- Initial / sample data

This ensures the application is **ready immediately after deployment** without manual database setup.

### init.sql

```bash
USE FCT;
 
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(20),
    email VARCHAR(100),
    website VARCHAR(255),
    gender VARCHAR(6),
    comment VARCHAR(100)
);
```


---

## 🧱 Dockerfile (Database Tier)

A custom **Dockerfile** is used inside the `db/` directory to build the MySQL image and copy the `init.sql` file into the container.

### Benefits:
- Automated database setup
- Consistent environments across deployments
- Easy re-deployment and scaling

---

![](./Image/Screenshot%202026-01-16%20200536.png)

![](./Image/Screenshot%202026-01-16%20202515.png)

## 🚀 How to Run the Application

Start all services:
```bash
docker-compose up -d
```

## 🌐 Access the Application

Open your browser and visit:
```bash
http://3.3.3.3/signup
```

![](./Image/Screenshot%202026-01-16%20202059.png)

![](./Image/Screenshot%202026-01-16%20202150.png)


