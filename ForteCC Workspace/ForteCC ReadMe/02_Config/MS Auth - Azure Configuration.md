
---
## 1. Set HTTPs In NGINX:
setup the https for this:
in nginx set the header https proto
```bash
# Default server configuration

# HTTP to HTTPS redirection
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name resources.fortecc.com resources.tapelectric.net;

    # Redirect all HTTP requests to HTTPS
    location / {
        return 301 https://$host$request_uri;
    }
}

# HTTPS configuration
server {
    listen 443 ssl;
    listen [::]:443 ssl;
    server_name resources.fortecc.com resources.tapelectric.net;

    # SSL configuration
    ssl_certificate /etc/letsencrypt/live/resources.fortecc.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/resources.fortecc.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot


    # Serve the static files
    location / {
          proxy_pass http://localhost:8000;
          proxy_http_version 1.1;

          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection 'upgrade';



          proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
        proxy_set_header HTTPS on;


          proxy_cache_bypass $http_upgrade;
      }

    # Deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    # location ~ /\.ht {
    #     deny all;
    # }

}

# Catch-all server block for unmatched requests
server {
    listen 80;
    listen [::]:80;
    server_name _;

    return 404;
}
```


- here we added https on as we want the app to know that the its running on https.

---
# 2. Make Changes in the Index.php file for accepting the https:

This setup is Symfony-based, and OrangeHRM is using the Symfony HTTP `Request` object. That means the place we want to **"trick" Symfony into thinking it's HTTPS** is _before_ the `$request = Request::createFromGlobals();` line — because that’s when it reads all the `$_SERVER` vars and decides the scheme.

---

### ✅ Here's the exact change you should make

Right before this line:

```php
$request = Request::createFromGlobals();
```

Insert this block:

```php
if (
    isset($_SERVER['HTTP_X_FORWARDED_PROTO']) &&
    $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https'
) {
    $_SERVER['HTTPS'] = 'on';
    $_SERVER['SERVER_PORT'] = 443;
    $_SERVER['REQUEST_SCHEME'] = 'https';
}
```

---

### 🔁 Final snippet should look like this:

```php
$kernel = new Framework($env, $debug);

// Force HTTPS if behind proxy
if (
    isset($_SERVER['HTTP_X_FORWARDED_PROTO']) &&
    $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https'
) {
    $_SERVER['HTTPS'] = 'on';
    $_SERVER['SERVER_PORT'] = 443;
    $_SERVER['REQUEST_SCHEME'] = 'https';
}

$request = Request::createFromGlobals();
```

---

### 💡 Why this works

Symfony uses the `$_SERVER` superglobal to build the `Request` object. If we set these values early, Symfony will build the request with `https://` as its scheme — and the redirect URIs (like the one sent to MS SSO) will use `https`.

---

### 📌 Don’t forget to restart the container after editing

Since this file lives inside the Docker container, after saving the file, you’ll need to:

```bash
docker restart <container_name>
```

Let me know if you're using `docker-compose`, and I can show how to do it there too.

---

Ready to test once you’re done! Want help verifying the Azure redirect URI as well?


---
## 3. Setup the Microsoft Azure AD.
### ✅ Step-by-Step: Azure AD Social Login Setup for OrangeHRM

#### 🔹 1. Register an App in Azure AD

1. Go to [Azure Portal](https://portal.azure.com/)
    
2. Search for **“App registrations”** and click **+ New registration**
    
3. Fill in:
    
    - **Name**: e.g., `OrangeHRM Social Login`
        
    - **Supported account types**: Usually **Accounts in this organizational directory only**
        
    - **Redirect URI** (important!):
        
        - Choose type: **Web**
            
        - Enter something like:
            
            ```
            https://your-orangehrm-domain.com/oauth2/callback
            ```
            
            > Adjust this based on your OrangeHRM deployment. If you’re testing locally, use `http://localhost:xxxx/oauth2/callback`.
            
4. Click **Register**
    

---

#### 🔹 2. Get Required Info

After registration, go to the app overview and note the following:

- **Client ID** → use this in OrangeHRM
    
- **Directory (tenant) ID** → used in the provider URL
    

Now go to **Certificates & Secrets**

- Click **+ New client secret**
    
- Add a name and expiration, then **Copy the secret** (you’ll only see it once)
    

---

#### 🔹 3. Fill OrangeHRM Social Provider Fields

| OrangeHRM Field   | Value                                           |
| ----------------- | ----------------------------------------------- |
| **Name**          | `AzureAD`                                       |
| **Provider URL**  | `https://login.microsoftonline.com/{tenant_id}` |
| **Client ID**     | From Azure App registration                     |
| **Client Secret** | The client secret you just created              |

> Replace `{tenant_id}` with your actual **Directory (tenant) ID**

---

#### 🔹 4. Permissions (Optional But Important)

Go to:

- **API permissions → + Add a permission → Microsoft Graph → Delegated permissions**
    
- Add: `openid`, `email`, `profile`, `User.Read`
    

Then click **Grant admin consent**.

---
### Add the Redirect URL in the Azure Portal
- For authentication to work add the redirect URL in the Authentication section on Azure.
- Add New Link by clicking on "Add a Platform".
- Add the URL (secure only) to the application to make registration.
- We need to have this as web application not mobile or desktop application.

### Redirect URIs: 
- https://resources.fortecc.com/web/index.php/openidauth/openIdCredentials
### Front-channel logout URL
This is where we send a request to have the application clear the user's session data. This is required for single sign-out to work correctly.
- https://resources.fortecc.com/web/index.php/openidauth/logout

Select the tokens you would like to be issued by the authorization endpoint:
- ID tokens (used for implicit and hybrid flows)

---
Ref:
https://starterhelp.orangehrm.com/hc/en-us/articles/12392345874076-Set-up-Authentication-Providers

---

## DIDNT WORK : 

add the following to compose:
environment:
  - HTTPS=on
  - HTTP_X_FORWARDED_PROTO=https
