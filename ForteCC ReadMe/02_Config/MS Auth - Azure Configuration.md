
---

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

|OrangeHRM Field|Value|
|---|---|
|**Name**|`AzureAD`|
|**Provider URL**|`https://login.microsoftonline.com/{tenant_id}/v2.0`|
|**Client ID**|From Azure App registration|
|**Client Secret**|The client secret you just created|

> Replace `{tenant_id}` with your actual **Directory (tenant) ID**

---

#### 🔹 4. Permissions (Optional But Important)

Go to:

- **API permissions → + Add a permission → Microsoft Graph → Delegated permissions**
    
- Add: `openid`, `email`, `profile`, `User.Read`
    

Then click **Grant admin consent**.

---
# Add the Redirect URL in the Azure Portal
- For authentication to work add the redirect URL in the Authentication section on Azure.
- Add New Link by clicking on "Add a Platform".
- Add the URL (secure only) to the application to make registration.
