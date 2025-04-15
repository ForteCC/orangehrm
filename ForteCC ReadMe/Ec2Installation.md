Here’s how to install Docker and Docker Compose on Ubuntu
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

ref:
[OrangeHRM (open source HRM software Docker App), Installation, Part 1](https://www.youtube.com/watch?v=FWS7KH7os2Y&t=1s&ab_channel=Datapioneer)


---

![](images/2025-03-28-15-52-19-image.png)

---

![](images/2025-03-28-18-12-57-image.png)

![](images/2025-03-28-18-13-11-image.png)

![](images/2025-03-28-18-13-26-image.png)
