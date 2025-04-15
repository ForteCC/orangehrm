To **reset and reinstall OrangeHRM** with Docker Compose — wiping all existing data and starting fresh — follow these steps carefully. This will **delete your database and OrangeHRM files**, so make sure you’re okay with losing all saved data.

---

### 🔁 **Step-by-Step Reset Instructions**

#### 🧨 1. **Stop and Remove All Containers**

This stops and deletes the running OrangeHRM and MariaDB containers:

```bash
docker compose down
```

If you want to be extra sure everything is gone (including volumes):

```bash
docker compose down --volumes --remove-orphans
```

> 🔥 `--volumes` deletes the database and app data — full reset.

---

#### 🧹 2. **Remove Named Volumes Manually (Optional)**

If you want to be 1000% sure no persistent data remains:

```bash
docker volume rm $(docker volume ls -qf dangling=false)
```

Or just delete specific volumes:

```bash
docker volume rm yourprojectname_db_data yourprojectname_orangehrm_data
```

You can list volume names with:

```bash
docker volume ls
```

---

#### 🧼 3. **(Optional) Remove All Images**

If you want to redownload fresh images:

```bash
docker rmi orangehrm/orangehrm mariadb:10.2
```

---

#### 🚀 4. **Reinstall / Re-deploy**

Now re-run the containers cleanly:

```bash
docker compose up -d
```

This will:

- Pull the images again (if needed)
    
- Recreate fresh containers
    
- Reinitialize the database with a clean slate
    

---

### ✅ Check Everything Is Clean

- Visit `http://localhost:8000` → You should see the OrangeHRM setup wizard.
    
- Run `docker volume ls` to confirm new volumes were created.
    

---

Let me know if you also want to **automate this reset process** with a script or add custom setup data after reinstall!