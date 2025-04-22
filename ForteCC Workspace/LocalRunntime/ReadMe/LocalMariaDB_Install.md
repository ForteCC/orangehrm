To install **MariaDB** (a MySQL-compatible database) locally on your system, follow these steps based on your operating system:

---

### **1. Linux (Ubuntu/Debian)**
#### Install MariaDB:
```bash
sudo apt update
sudo apt install mariadb-server
```

#### Secure the Installation (Set Root Password):
```bash
sudo mysql_secure_installation
```
- Follow prompts to set a root password and secure the installation.

#### Start & Enable MariaDB:
```bash
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

#### Check Status:
```bash
sudo systemctl status mariadb
```

#### Log in to MariaDB:
```bash
sudo mysql -u root -p
```

---

### **2. Linux (CentOS/RHEL/Fedora)**
#### Install MariaDB:
```bash
sudo dnf install mariadb-server  # Fedora/CentOS 8+
# or
sudo yum install mariadb-server  # CentOS 7
```

#### Start & Enable MariaDB:
```bash
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

#### Secure Installation:
```bash
sudo mysql_secure_installation
```

#### Log in:
```bash
sudo mysql -u root -p
```

---

### **3. macOS (Using Homebrew)**
#### Install MariaDB:
```bash
brew install mariadb
```

#### Start MariaDB:
```bash
brew services start mariadb
```

#### Secure Installation:
```bash
sudo mysql_secure_installation
```

#### Log in:
```bash
sudo mysql -u root -p
```

---

### **4. Windows**
#### Download & Install:
1. Download the installer from [MariaDB Downloads](https://mariadb.org/download/).
2. Run the installer and follow the setup wizard.
3. Set a **root password** when prompted.
4. (Optional) Enable **"Use UTF8 as default server's character set"**.

#### Start MariaDB:
- From **Command Prompt (Admin)**:
  ```cmd
  net start mysql
  ```
- Or use **Windows Services** (`services.msc`) to start `MariaDB`.

#### Log in:
```cmd
mysql -u root -p
```

---

### **Post-Installation Steps**
1. **Create a New User (Optional):**
   ```sql
   CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
   GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost' WITH GRANT OPTION;
   FLUSH PRIVILEGES;
   ```

2. **Create a Database:**
   ```sql
   CREATE DATABASE testdb;
   USE testdb;
   ```

3. **Test Connection:**
   ```bash
   mysql -u username -p
   ```

---

### **Uninstall MariaDB (If Needed)**
- **Ubuntu/Debian**:
  ```bash
  sudo apt purge mariadb-server
  sudo rm -rf /var/lib/mysql/
  ```
- **CentOS/Fedora**:
  ```bash
  sudo dnf remove mariadb-server
  ```
- **macOS**:
  ```bash
  brew services stop mariadb
  brew uninstall mariadb
  ```
- **Windows**: Use **Control Panel > Uninstall a Program**.



# while we setup the orangehrm database username password are the root username and root password.