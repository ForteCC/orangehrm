This file defines two services: **OrangeHRM** (the app) and **MariaDB** (the database).

---

## 🧾 Top-Level Configuration

```yaml
version: "3.3"
```

- Specifies the **Compose file format version**. Version `3.3` is compatible with Docker Compose v1.21+ and is stable for production.
    

---

## 🗃 Volumes

```yaml
volumes:
  db_data:  # Named volume for database storage
  orangehrm_data:  # Named volume for OrangeHRM files
```

- Defines **named volumes** that Docker will manage.
- `db_data`: Stores database data persistently (so data isn’t lost if the container restarts).
- `orangehrm_data`: Stores OrangeHRM files (e.g., configuration, logs, etc.).
---

## 📦 Services

### 🔸 **OrangeHRM Container**

```yaml
  orangehrm:
    image: orangehrm/orangehrm:latest
```

- Uses the latest **official OrangeHRM Docker image** from Docker Hub.

```yaml
    restart: unless-stopped
```

- Ensures the container **restarts automatically** unless you manually stop it.

```yaml
    ports:
      - "8000:80"
      - "8443:443"
```

- Maps:
    
    - Port `80` (inside container) → `8000` on your host (for HTTP).
    - Port `443` (inside container) → `8443` on your host (for HTTPS).

Access it via:  
👉 [http://localhost:8000](http://localhost:8000/) or [https://localhost:8443](https://localhost:8443/)

```yaml
    environment:
      - ORANGEHRM_DATABASE_HOST=mariadb
      - ORANGEHRM_DATABASE_USER=datapioneer
      - ORANGEHRM_DATABASE_PASSWORD=SomePassword
      - ORANGEHRM_DATABASE_NAME=orangehrm
      - PUID=998
      - PGID=100
```

- These env vars configure **how OrangeHRM connects to the database**:
    
    - `ORANGEHRM_DATABASE_HOST`: Name of the **database container**.
        
    - `PUID` and `PGID`: Optional – these set the user and group ID inside the container to avoid permission issues when writing to volumes.
        

```yaml
    volumes:
      - orangehrm_data:/orangehrm
```

- Maps the named volume `orangehrm_data` to `/orangehrm` inside the container.
    

```yaml
    depends_on:
      - mariadb
```

- Tells Docker Compose: **Start `mariadb` first**, then `orangehrm`.
    

```yaml
    links:
      - mariadb:mariadb
```

- Legacy feature, creates a network alias. Not strictly needed with modern Compose networking — `depends_on` + service name works fine.
    

---

### 🔸 **MariaDB Container**

```yaml
  mariadb:
    image: mariadb:10.2
```

- Uses version `10.2` of the **MariaDB image**, a drop-in replacement for MySQL.
    

```yaml
    restart: unless-stopped
```

- Auto-restarts unless you stop it manually.
    

```yaml
    volumes:
      - db_data:/var/lib/mysql  # Uses Docker-managed volume
```

- Maps named volume `db_data` to MariaDB’s default data directory.
    

```yaml
    environment:
      - MYSQL_ROOT_PASSWORD=SomePassword
      - MYSQL_ROOT_USER=root
      - MYSQL_DATABASE=orangehrm
```

- Sets up:
    
    - `root` user with a password.
    - A new database named `orangehrm`.
        

⚠️ **Note**: `MYSQL_ROOT_USER` is not a standard environment variable for MariaDB. This line can be removed — the default root user is `root` anyway.
- There are 2 users root user that is user for MariaDB and another user is the orangeHRM user who has permission to modify only the orangeHRM table in the database of MariaDB.

---

## 🚀 How It All Comes Together

1. Docker Compose creates a **network** where both services can communicate.
2. **MariaDB** starts up first, with a new database and user credentials.
3. **OrangeHRM** starts and connects to the MariaDB container via the name `mariadb` using the credentials you provided.
4. You can access the web UI at:
    - `http://localhost:8000`
    - Or `https://localhost:8443` (if SSL is set up inside the container)

---

### ✅ To Run This

Just place that YAML in a file named `docker-compose.yml`, then run:

```bash
docker compose up -d
```

> `-d` = detached mode (runs in the background)

You can check the logs using:

```bash
docker compose logs -f
```

---

