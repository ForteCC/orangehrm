
---
Create RDS on aws: 
- create the database
- connect to ec2 - this will auto configure the vpc and security grp.
- create master password that will be used to create the orangehrmUser and start the mysql shell on ec2.

CREATE DATABASE orangehrmdb;
CREATE USER 'orangehrmUser'@'%' IDENTIFIED BY 'FKX6wqm4dtw3btr.qjq';
GRANT ALL PRIVILEGES ON orangehrmdb.* TO 'orangehrmUser'@'%';
FLUSH PRIVILEGES;


Great question — when you're using **AWS RDS with RDS Proxy**, **the credentials don’t change** from what you'd normally use to access your RDS instance directly. You just replace the host with the **RDS Proxy endpoint**.

Here’s how it works inside your `docker-compose.yml`:

---

## 🔐 You Need Three Things in Compose:

| Variable                      | Value (From RDS setup)                           |
| ----------------------------- | ------------------------------------------------ |
| `ORANGEHRM_DATABASE_HOST`     | The RDS Proxy endpoint (DNS name)                |
| `ORANGEHRM_DATABASE_USER`     | RDS database user (e.g., `admin` or `orangehrm`) |
| `ORANGEHRM_DATABASE_PASSWORD` | The corresponding password                       |
| `ORANGEHRM_DATABASE_NAME`     | The DB name you created (e.g., `orangehrmdb`)    |

---

## 🧾 Sample Compose Snippet for OrangeHRM

```yaml
version: "3.3"

volumes:
  db_data:  # Named volume for database storage
  orangehrm_data:  # Named volume for OrangeHRM files

services:
  orangehrm:
    image: orangehrm/orangehrm:latest
    restart: unless-stopped
    ports:
      - "8000:80"
      - "8443:443"
    environment:
      - ORANGEHRM_DATABASE_HOST=<RDS_DATABASE_URI>
      - ORANGEHRM_DATABASE_USER=orangehrmUser <Created_user_in_EC2>
      - ORANGEHRM_DATABASE_PASSWORD=<Password_SET_ON_EC2>
      - ORANGEHRM_DATABASE_NAME=orangehrmdb <Created_DB_IN_EC2>
      - PUID=998
      - PGID=100
    volumes:
      - orangehrm_data:/orangehrm
      - /home/ubuntu/ohrm_logo.png:/var/www/html/web/images/ohrm_logo.png
```

You do **not** need a `mariadb` service anymore — you’ve offloaded that to RDS.

---

## 👤 Root User?

You **can** use the RDS master/root user if you want to (typically `admin` or `root`), but for security:

- ✅ **Create a dedicated DB user** like `orangehrm`
- ✅ Grant that user privileges only on `orangehrmdb`

---
## 🧪 How to Create the DB/User (if not done yet)

From a MySQL/MariaDB client:
```bash
mysql -h database-1.c4d62s4cknm5.us-east-1.rds.amazonaws.com -u admin -p

```

```sql
CREATE DATABASE orangehrmdb;
CREATE USER 'orangehrmUser'@'%' IDENTIFIED BY '93RspoUaDaqcjI';
GRANT ALL PRIVILEGES ON orangehrmdb.* TO 'orangehrmUser'@'%';
FLUSH PRIVILEGES;
```
Then plug these into your Docker env vars.

---
