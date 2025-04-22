`sudo ls /var/lib/docker/volumes/`Here’s how to install Docker and Docker Compose on Ubuntu
https://wiki.opensourceisawesome.com/books/open-source-personnel-management/page/install-and-configure-orangehrm

### **Step 1: Update System Packages**

```bash
sudo apt update && sudo apt upgrade -y
```
- keep system up to date ; install all the updates.
### **Step 2: Install Required Dependencies**

```bash
sudo apt install -y ca-certificates curl gnupg
```
- install certificate for https.
- install the gnupg for verifying software dependecy.
### **Step 3: Add Docker’s Official GPG Key**

```bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo tee /etc/apt/keyrings/docker.asc > /dev/null
sudo chmod a+r /etc/apt/keyrings/docker.asc
```
- Install directory for secure storage of trusted GPG keys.
- GPG helps to verify docker images are not tampered and are orignal this is good for safety.
### **Step 4: Set Up the Docker Repository**

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
- `dpkg --print-architecture`: Detects your system's architecture (e.g., amd64).
- `lsb_release -cs`: Gets your Ubuntu codename (e.g., `jammy` for Ubuntu 22.04).
- This command builds and saves the Docker APT source line to a new list file (`docker.list`).\
- 🧠 **Why?**  
	APT uses this repository to fetch Docker packages — you’re telling Ubuntu “here’s a trusted place to get Docker from.”
### **Step 5: Install Docker and Docker Compose**

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### **Step 6: Verify Installation**

Check Docker version:

```bash
docker --version
```

Check Docker Compose version:

```bash
docker compose version
```

### **Step 7: Enable & Start Docker Service**

```bash
sudo systemctl enable --now docker
```

### **Step 8: Allow Running Docker Without sudo (Optional)**

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

compose file

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
      - ORANGEHRM_DATABASE_HOST=
      - ORANGEHRM_DATABASE_USER=
      - ORANGEHRM_DATABASE_PASSWORD=
      - ORANGEHRM_DATABASE_NAME=
      - PUID=998
      - PGID=100
    volumes:
      - orangehrm_data:/orangehrm
    depends_on:
      - mariadb
    links:
      - mariadb:mariadb

  mariadb:
    image: mariadb:10.2
    restart: unless-stopped
    volumes:
      - db_data:/var/lib/mysql  # Uses Docker-managed volume
    environment:
      - MYSQL_ROOT_PASSWORD=
      - MYSQL_ROOT_USER=
      - MYSQL_DATABASE=
```

---
## Persistant Volume:
- Database data in: `/var/lib/docker/volumes/<project>_db_data/_data/`
- OrangeHRM files in: `/var/lib/docker/volumes/<project>_orangehrm_data/_data/`

## To get into the folder do this:
#### 1. **Use `sudo` to start a shell:**
`sudo -s 
cd /var/lib/docker/volumes/`
Now you're in a root shell and can navigate protected directories.
#### 2. **Use `sudo` with `ls` or other commands directly:**

If you just want to look around:
`sudo ls /var/lib/docker/volumes/`

- OrangeHRM : orangehrm_db_data
- DB: orangehrm_orangehrm_data
---

ref:
[OrangeHRM (open source HRM software Docker App), Installation, Part 1](https://www.youtube.com/watch?v=FWS7KH7os2Y&t=1s&ab_channel=Datapioneer)

---
## Database Configuration
1) Select option of existing empty Database as we already had created DB with compose.
2) Database Host Name : Name of the container running for database(`docker-compose ps`)
3) Database Name: Value of `ORANGEHRM_DATABASE_NAME` in the Compose File.
4) OrangeHRM Database Username: MYSQL_ROOT_USER
5) OrangeHRM Database Password: ORANGEHRM_DATABASE_PASSWORD

## Instance Creation:
1) Timezone: America/New_York.

## Admin User Creation
1) Create new user that will be added to the OrangeHRM database and as an admin. e.g. John Doe.
2) Uncheck Register Your System With OrangeHRM.
3) Click Next.

This will Finish your installation process.

---
