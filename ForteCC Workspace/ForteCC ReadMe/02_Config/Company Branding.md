# Colors in the dashboard.
- Primary Color: # FF7B1D 
- Primary Gradient Color 1 : # 0b2cff
- Secondary Color: # 76BC21
- Primary Gradient Color 2: # 04f4f4

---
# Login Page Logo
- Go inside the Docker image and change the image.
- sudo -s : opens shell in root mode.
- `sudo ls /var/lib/docker/volumes/ `
-  src="/var/www/html/web/images/ohrm_logo.png" : this file needs to be replaced by the Logo of Fortecc
## Where is the resource files
- /var/www/html

```bash
scp -i "/Users/mjalan/Documents/secreats/Ec2/ProdTestServer.pem" "/Users/mjalan/Documents/logoimages/ohrm_logo.png" ubuntu@ec2-13-216-49-53.compute-1.amazonaws.com:/home/ubuntu/
```
- Make changes in the Docker Container.
```bash
docker exec -it <container_name_or_id> bash

```
- Copy File from ec2 to container: 
```bash
docker cp ./ohrm_logo.png orangehrm_orangehrm_1:/var/www/html/web/images
```
---
# How to check the images in the container:

If OrangeHRM is running on a web server like Apache or Nginx, and it's serving from `/var/www/html`, then `/var/www/html/web/images` maps to:
```perl
https://resources.fortecc.com/web/images/ohrm_logo.png
```

---
```bash
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
      - ORANGEHRM_DATABASE_HOST=database-2-orangehrm.c4d62s4cknm5.us-east-1.rds.amazonaws.com
      - ORANGEHRM_DATABASE_USER=orangehrmUser
      - ORANGEHRM_DATABASE_PASSWORD=
      - ORANGEHRM_DATABASE_NAME=orangehrmdb
      - PUID=998
      - PGID=100
    volumes:
      - orangehrm_data:/orangehrm
      - ./updateFile/ohrm_logo.png:/var/www/html/web/images/ohrm_logo.png
      - ./updateFile/index.php:/var/www/html/web/index.php 
```
