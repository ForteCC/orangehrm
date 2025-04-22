# Current Docker File:
- Dont use the local directory as code reference (that is in web folder).
- download the zip file from the sourceforge and extract it.
- env, on which it does that has a debian os running php and apache web server.


---
 
 Let me search for Docker-related files in the codebase:



Search files...
Based on the search results, I can explain how the Docker images are being made in OrangeHRM. There are several key files involved:

1. **Dockerfile**:
- Located in the root directory
- Uses `php:8.3-apache-bookworm` as the base image
- Installs required PHP extensions (gd, opcache, intl, pdo_mysql, zip, ldap)
- Downloads and sets up OrangeHRM version 5.7
- Configures Apache and PHP settings
- Sets up volumes for persistent storage

2. **docker-compose.yml**:
- Defines two main services:
  a. **orangehrm** service:
     - Uses the official OrangeHRM image
     - Maps ports 8000:80 and 8443:443
     - Sets environment variables for database connection
     - Mounts volumes for persistent storage
     - Depends on the MariaDB service

  b. **mariadb** service:
     - Uses MariaDB 10.2 image
     - Sets up database credentials
     - Mounts volume for database storage

3. **Volumes**:
- Two named volumes are created:
  - `db_data`: For MariaDB data storage
  - `orangehrm_data`: For OrangeHRM application files

4. **Environment Configuration**:
- Database connection settings:
  ```
  ORANGEHRM_DATABASE_HOST=mariadb
  ORANGEHRM_DATABASE_USER=orangehrmUser
  ORANGEHRM_DATABASE_PASSWORD=your_password
  ORANGEHRM_DATABASE_NAME=orangehrmdb
  ```

5. **Build Process**:
- The Docker image is built using the Dockerfile
- The build process includes:
  - Installing PHP dependencies
  - Setting up Apache
  - Configuring PHP extensions
  - Setting up OrangeHRM files
  - Configuring permissions

6. **Deployment**:
- The system can be deployed using:
  ```bash
  docker-compose up -d
  ```
- This will:
  - Pull the required images
  - Create the containers
  - Set up the volumes
  - Start the services

7. **Customization**:
- You can customize the setup by:
  - Modifying the `docker-compose.yml` file
  - Adding custom volumes for files
  - Changing environment variables
  - Modifying port mappings

8. **Maintenance**:
- The system includes commands for:
  - Restarting containers
  - Updating images
  - Managing volumes
  - Viewing logs

Would you like me to explain any specific part of the Docker setup in more detail?
