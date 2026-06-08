# Service Discovery File Hosting Guide
## Account-Driven User Enrollment — Option B

| | |
|---|---|
| **Document Type** | Supplementary Configuration Guide |
| **Version** | 2.0 |
| **Author** | Sr. Modern Work Consultant |
| **Last Updated** | May 2026 |
| **Applies To** | Account-Driven User Enrollment (Option B), iOS/iPadOS 15.0+, Microsoft Intune |

---

## Table of Contents

1. [Overview](#1-overview)
2. [Before You Start](#2-before-you-start)
3. [Option 1 — Azure Static Web Apps](#3-option-1--azure-static-web-apps)
   - [3.1 Prerequisites](#31-prerequisites)
   - [3.2 Prepare the File Structure](#32-prepare-the-file-structure)
   - [3.3 Create the GitHub Repository and Push Files](#33-create-the-github-repository-and-push-files)
   - [3.4 Create the Azure Static Web App](#34-create-the-azure-static-web-app)
   - [3.5 Configure Custom Domain and DNS](#35-configure-custom-domain-and-dns)
   - [3.6 Automate with PowerShell and GitHub CLI](#36-automate-with-powershell-and-github-cli)
   - [3.7 Verify Deployment](#37-verify-deployment)
4. [Option 2 — GitHub Pages](#4-option-2--github-pages)
   - [4.1 Prerequisites](#41-prerequisites)
   - [4.2 Create the GitHub Repository and Files](#42-create-the-github-repository-and-files)
   - [4.3 Enable GitHub Pages](#43-enable-github-pages)
   - [4.4 Configure Custom Domain and DNS](#44-configure-custom-domain-and-dns)
   - [4.5 Enable HTTPS](#45-enable-https)
   - [4.6 Automate with PowerShell and GitHub CLI](#46-automate-with-powershell-and-github-cli)
   - [4.7 Verify Deployment](#47-verify-deployment)
5. [Option 3 — Cloudflare Workers](#5-option-3--cloudflare-workers)
   - [5.1 Prerequisites](#51-prerequisites)
   - [5.2 Transfer DNS from [your-dns-provider] to Cloudflare](#52-transfer-dns-from-godaddy-to-cloudflare)
   - [5.3 Create the Cloudflare Worker](#53-create-the-cloudflare-worker)
   - [5.4 Add the Worker Route to Your Domain](#54-add-the-worker-route-to-your-domain)
   - [5.5 Automate with Wrangler CLI](#55-automate-with-wrangler-cli)
   - [5.6 Automate with PowerShell and the Cloudflare API](#56-automate-with-powershell-and-the-cloudflare-api)
   - [5.7 Verify Deployment](#57-verify-deployment)
   - [5.8 (Optional) Connect to GitHub for Source Control and Auto-Deployment](#58-optional--connect-to-github-for-source-control-and-auto-deployment)
6. [Validate the File](#6-validate-the-file)
7. [Troubleshooting](#7-troubleshooting)

---

## 1. Overview

Account-Driven User Enrollment (Option B in the BYOD Deployment Guide) requires a **service discovery file** hosted at a specific HTTPS path on your organization's domain. When a user enters their work email address during enrollment, iOS/iPadOS performs the following sequence:

1. iOS extracts the domain from the email address (the part after `@`)
2. iOS constructs the service discovery URL: `https://[domain]/.well-known/com.apple.remotemanagement`
3. iOS sends an HTTPS GET request to that URL
4. The JSON response identifies the Intune MDM server
5. iOS contacts Intune and proceeds with enrollment

**Required URL format:**

```
https://[yourdomain]/.well-known/com.apple.remotemanagement
```

For example, if users sign in as `firstname.surname@yourdomain.com`, Apple queries `https://yourdomain.com/.well-known/com.apple.remotemanagement`.

**Why DNS records alone are not enough:** DNS resolves a domain name to an IP address. Apple still needs to make an HTTPS GET request to retrieve the JSON file content. DNS by itself cannot serve HTTP responses — a hosting service is required to respond to that request.

**Hosting options at a glance:**

| Option | Keep [your-dns-provider] DNS | Cost | Skill Level | Setup Time | Best For |
|---|---|---|---|---|---|
| Azure Static Web Apps | Yes — CNAME + TXT added to [your-dns-provider] | Free tier | Beginner | 20–30 min | Organizations already using Microsoft Azure |
| GitHub Pages | Yes — 4 x A records added to [your-dns-provider] | Free | Beginner | 15–20 min | Organizations with an existing GitHub account |
| Cloudflare Workers | No — DNS moves to Cloudflare | Free tier | Intermediate | 30–45 min | Organizations that want zero ongoing infrastructure management |

---

## 2. Before You Start

Regardless of which option you choose, gather the following information before starting.

**1. Entra Directory (Tenant) ID**

- Sign in to the [Microsoft Entra admin center](https://entra.microsoft.com)
- Navigate to **Identity > Overview**
- Copy the **Tenant ID** — it is a GUID in the format `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`

**2. Your organization's email domain**

- The domain is the part after `@` in your user email addresses
- Example: if users sign in as `firstname.surname@yourdomain.com`, your domain is `yourdomain.com`

**3. Access to your DNS provider**

- For Options 1 and 2, you keep [your-dns-provider] as your DNS provider — you will add records during setup
- For Option 3, DNS management moves to Cloudflare ([your-dns-provider] remains your domain registrar)

**4. The service discovery file content**

The file you will publish contains a single JSON object. Select the correct version for your environment and replace `REPLACE:YOUR_ENTRA_TENANT_ID` with your actual Tenant ID before publishing:

**Standard Intune (commercial cloud):**

```json
{"Servers":[{"Version":"mdm-byod","BaseURL":"https://manage.microsoft.com/EnrollmentServer/PostReportDeviceInfoForUEV2?aadTenantId=REPLACE:YOUR_ENTRA_TENANT_ID"}]}
```

**US Government (GCC High / DoD):**

```json
{"Servers":[{"Version":"mdm-byod","BaseURL":"https://manage.microsoft.us/EnrollmentServer/PostReportDeviceInfoForUEV2?aadTenantId=REPLACE:YOUR_ENTRA_TENANT_ID"}]}
```

> **Note:** The file has no file extension and contains no surrounding whitespace or newlines. Copy the JSON exactly as shown on a single line.

---

## 3. Option 1 — Azure Static Web Apps

Azure Static Web Apps is the recommended option for organizations in the Microsoft ecosystem. It has a free tier, provisions HTTPS automatically, and allows you to keep [your-dns-provider] as your DNS provider — you only need to add one CNAME record and one TXT record.

**When to choose this option:** Your organization already uses Azure or Microsoft 365, or you want the closest alignment between your hosting infrastructure and your identity provider.

**How it works:** Azure Static Web Apps uses a GitHub repository as its deployment source. Files pushed to the `main` branch are automatically deployed to Azure via GitHub Actions. You create the files locally, push them to a private GitHub repository, then link that repository to an Azure Static Web App.

---

### 3.1 Prerequisites

Install the following tools before starting. Skip any tool you already have installed.

| Tool | Purpose | Install Command (Windows) |
|---|---|---|
| Git | Push files to GitHub | `winget install Git.Git` |
| GitHub CLI | Create and configure the repository from the command line | `winget install GitHub.cli` |
| Azure PowerShell (Az module) | Create and configure the Static Web App from the command line | `Install-Module -Name Az -Scope CurrentUser -Force` |

After installing, authenticate each tool:

```powershell
# Authenticate GitHub CLI (opens a browser prompt)
gh auth login

# Authenticate Azure PowerShell (opens a browser prompt)
Connect-AzAccount
```

> **Note:** `Install-Module -Name Az` installs the complete Az module set, including `Az.Websites`, which provides the `New-AzStaticWebApp` cmdlet. If Az is already installed, run `Update-Module -Name Az` to ensure you have a current version.

---

### 3.2 Prepare the File Structure

> Create the following folder structure on your computer. The folder name can be anything — use `apple-mdm-service-discovery` for clarity.

> **Note:** The `.well-known` folder name must be exactly named `.well-known` Apple's service discovery query always targets `/.well-known/com.apple.remotemanagement` on your domain. Any other folder name will cause enrollment to fail with HTTP 404.

```
apple-mdm-service-discovery/
├── .well-known/
│   └── com.apple.remotemanagement
└── staticwebapp.config.json
```

**File 1: `.well-known/com.apple.remotemanagement`**

Create this file with no file extension. Paste the JSON for your environment (from Section 2), replacing `REPLACE:YOUR_ENTRA_TENANT_ID` with your actual Tenant ID:

```json
{"Servers":[{"Version":"mdm-byod","BaseURL":"https://manage.microsoft.com/EnrollmentServer/PostReportDeviceInfoForUEV2?aadTenantId=REPLACE:YOUR_ENTRA_TENANT_ID"}]}
```

**File 2: `staticwebapp.config.json`**

This file instructs Azure Static Web Apps to serve the extensionless service discovery file with a `Content-Type: application/json` header. Without it, Azure may serve extensionless files as `application/octet-stream`, which Apple will reject and enrollment will fail:

```json
{
  "routes": [
    {
      "route": "/.well-known/com.apple.remotemanagement",
      "headers": {
        "Content-Type": "application/json"
      }
    }
  ]
}
```

---

### 3.3 Create the GitHub Repository and Push Files

#### Manual

1. Sign in to [github.com](https://github.com)
2. Select the **+** icon in the top-right corner and choose **New repository**
3. Fill in the repository details:
   - **Owner:** Select your organization or personal account
   - **Repository name:** `apple-mdm-service-discovery`
   - **Visibility:** Private
4. Select **Create repository**
5. Upload files using the GitHub web interface:
   - On the empty repository page, select **uploading an existing file**
   - Drag and drop `staticwebapp.config.json`
   - To create the `.well-known` folder: select **Add file > Create new file**, type `.well-known/com.apple.remotemanagement` in the name field (GitHub creates the folder automatically when you type the `/`), paste the JSON content, then select **Commit new file**

#### Using Git from the command line

After creating the repository, push both files from your terminal:

```powershell
# Navigate to the folder you created in Section 3.2
Set-Location "apple-mdm-service-discovery"

git init
git add .
git commit -m "Add Apple MDM service discovery files"
git branch -M main
git remote add origin "https://github.com/REPLACE:YOUR_ORG/apple-mdm-service-discovery.git"
git push -u origin main
```

---

### 3.4 Create the Azure Static Web App

#### Manual

1. Sign in to the [Azure portal](https://portal.azure.com)
2. In the top search bar, search for **Static Web Apps** and select it from the results
3. Select **Create**
4. Fill in the **Basics** tab:
   - **Subscription:** Select your subscription
   - **Resource group:** Select an existing resource group or select **Create new** (e.g., `rg-apple-mdm-discovery`)
   - **Name:** Enter a name (e.g., `apple-mdm-discovery`)
   - **Plan type:** Free
   - **Region:** Select the region closest to your users
5. Under **Deployment details**, select **GitHub** as the source
6. Select **Sign in with GitHub** and authorize Azure to access your GitHub account
7. Set the repository details:
   - **Organization:** Your GitHub organization or personal account name
   - **Repository:** `apple-mdm-service-discovery`
   - **Branch:** `main`
8. Under **Build details**:
   - **Build presets:** Custom
   - **App location:** `/`
   - **Output location:** Leave blank
9. Select **Review + create**, review the settings, then select **Create**

Azure creates a GitHub Actions workflow file in your repository and deploys the files automatically within 1–2 minutes. Monitor progress in your GitHub repository under the **Actions** tab.

#### Using PowerShell

Run the following commands after completing Sections 3.2 and 3.3. Replace the placeholder values with your own before running.

```powershell
# Create the resource group (skip if it already exists)
New-AzResourceGroup -Name 'rg-apple-mdm-discovery' -Location 'eastus'

# Retrieve the GitHub token stored by the GitHub CLI — used by Azure to set up the Actions workflow
$GitHubToken = gh auth token

# Create the Static Web App linked to the GitHub repository
$WebApp = New-AzStaticWebApp `
    -Name              'apple-mdm-discovery' `
    -ResourceGroupName 'rg-apple-mdm-discovery' `
    -Location          'eastus' `
    -RepositoryUrl     'https://github.com/REPLACE:YOUR_ORG/apple-mdm-service-discovery' `
    -RepositoryToken   $GitHubToken `
    -Branch            'main' `
    -AppLocation       '/' `
    -SkuName           'Free'

Write-Host "Static Web App hostname: $($WebApp.DefaultHostname)"
```

> **Note:** `-AppLocation '/'` points to the repository root where your files are located. `-OutputLocation` is not needed here — there is no build step; the files are served directly. Azure provisions the GitHub Actions workflow automatically using the token from `gh auth token`. Monitor the first deployment under the **Actions** tab in your GitHub repository (allow 1–2 minutes).

---

### 3.5 Configure Custom Domain and DNS

After deployment, add your organization's domain to the Static Web App and create the corresponding DNS records in [your-dns-provider].

#### Add the custom domain in Azure

1. In the Azure portal, open your Static Web App (`apple-mdm-discovery`)
2. In the left menu, select **Custom domains**
3. Select **Add** and then select **Custom domain on other DNS**
4. Enter your domain (e.g., `yourdomain.com`) and select **Next**
5. Azure generates and displays:
   - A **TXT record** value for domain ownership verification
   - A **CNAME record** value pointing to the Static Web App hostname
6. Copy both values — you need them for the next step

#### Add DNS records in [your-dns-provider]

1. Sign in to [your-dns-provider] and navigate to **My Products**
2. Find your domain and select **DNS** or **Manage DNS**
3. Add the **TXT record** first — Azure requires this to verify domain ownership before it activates HTTPS:
   - **Type:** TXT
   - **Name:** `@` (or as specified by Azure)
   - **Value:** The TXT value from the Azure portal
   - **TTL:** 1 Hour
4. Add the **CNAME record**:
   - **Type:** CNAME
   - **Name:** The value Azure specifies (typically `www` or `@`)
   - **Value:** The hostname Azure provided (e.g., `lively-meadow-abc123.azurestaticapps.net`)
   - **TTL:** 1 Hour
5. Select **Save**

> **Important — apex domain limitation:** Some DNS providers do not support CNAME records at the root/apex of a domain (the `@` record). If Azure requires an apex CNAME, you have two choices: (1) use the `www` subdomain with Azure and configure a redirect from the root domain to `www`, noting that Apple's service discovery query targets the root domain (not `www`), so the redirect must preserve the `/.well-known/` path and return a `200` — not a redirect — for that specific path; or (2) use Option 2 (GitHub Pages with A records) or Option 3 (Cloudflare Workers), both of which support root domain hosting without this restriction.

#### Verify the custom domain in Azure

1. Return to the Azure portal → your Static Web App → **Custom domains**
2. Wait for DNS propagation (typically 5–30 minutes, up to 48 hours in some cases)
3. Once Azure detects the DNS records, select **Validate** to complete domain verification
4. Azure automatically provisions and renews a TLS certificate — no certificate management is required

---

### 3.6 Automate with PowerShell and GitHub CLI

The script below performs all setup steps end to end: creates the files locally, creates the GitHub repository, pushes the files, creates the Azure resource group and Static Web App, and prints the DNS values you need to add to [your-dns-provider].

Edit the variables at the top before running. Run it from PowerShell after completing Section 3.1.

```powershell
# -----------------------------------------------------------------------
# CONFIGURE THESE VARIABLES BEFORE RUNNING
# -----------------------------------------------------------------------
$TenantId      = "REPLACE:YOUR_ENTRA_TENANT_ID"          # Entra Directory (Tenant) ID
$Domain        = "REPLACE:yourdomain.com"                  # Your organization domain
$GitHubOrg     = "REPLACE:YOUR_GITHUB_ORG_OR_USERNAME"    # GitHub org or personal account name
$RepoName      = "apple-mdm-service-discovery"             # GitHub repository name
$ResourceGroup = "rg-apple-mdm-discovery"                  # Azure resource group name
$Location      = "eastus"                                  # Azure region (change if needed)
$AppName       = "apple-mdm-discovery"                     # Azure Static Web App name
# -----------------------------------------------------------------------

# Service discovery JSON
$ServiceDiscoveryJson = '{"Servers":[{"Version":"mdm-byod","BaseURL":"https://manage.microsoft.com/EnrollmentServer/PostReportDeviceInfoForUEV2?aadTenantId=' + $TenantId + '"}]}'

# Static Web App routing config — sets Content-Type for the extensionless service discovery file
$StaticWebAppConfig = @'
{
  "routes": [
    {
      "route": "/.well-known/com.apple.remotemanagement",
      "headers": {
        "Content-Type": "application/json"
      }
    }
  ]
}
'@

# --- Step 1: Create local file structure ---
Write-Host "Creating local file structure..."
New-Item -ItemType Directory -Force -Path "$RepoName\.well-known" | Out-Null
Set-Content -Path "$RepoName\.well-known\com.apple.remotemanagement" `
            -Value $ServiceDiscoveryJson -Encoding utf8NoBOM
Set-Content -Path "$RepoName\staticwebapp.config.json" `
            -Value $StaticWebAppConfig -Encoding utf8NoBOM
Write-Host "Files created."

# --- Step 2: Create private GitHub repository ---
Write-Host "Creating GitHub repository '$GitHubOrg/$RepoName'..."
gh repo create "$GitHubOrg/$RepoName" --private `
    --description "Apple MDM Service Discovery for Intune Account-Driven User Enrollment"

# --- Step 3: Initialize Git and push ---
Set-Location $RepoName
git init
git add .
git commit -m "Add Apple MDM service discovery files"
git branch -M main
git remote add origin "https://github.com/$GitHubOrg/$RepoName.git"
git push -u origin main
Set-Location ..
Write-Host "Files pushed to GitHub."

# --- Step 4: Retrieve GitHub token for Azure integration ---
$GitHubToken = gh auth token

# --- Step 5: Connect to Azure ---
Write-Host "Connecting to Azure (browser window will open if not already signed in)..."
Connect-AzAccount | Out-Null

# --- Step 6: Create resource group ---
Write-Host "Creating resource group '$ResourceGroup' in '$Location'..."
New-AzResourceGroup -Name $ResourceGroup -Location $Location | Out-Null

# --- Step 7: Create Azure Static Web App linked to the GitHub repository ---
Write-Host "Creating Azure Static Web App '$AppName'..."
$WebApp = New-AzStaticWebApp `
    -Name              $AppName `
    -ResourceGroupName $ResourceGroup `
    -Location          $Location `
    -RepositoryUrl     "https://github.com/$GitHubOrg/$RepoName" `
    -Branch            "main" `
    -AppLocation       "/" `
    -SkuName           "Free" `
    -RepositoryToken   $GitHubToken

Write-Host ""
Write-Host "--- Deployment complete ---"
Write-Host "Static Web App hostname : $($WebApp.DefaultHostname)"
Write-Host ""
Write-Host "Next steps:"
Write-Host "  1. In the Azure portal, open '$AppName' > Custom domains > Add"
Write-Host "  2. Enter '$Domain' and copy the TXT and CNAME values Azure provides"
Write-Host "  3. Add those two records to [your-dns-provider] DNS for '$Domain'"
Write-Host "  4. Return to Azure and select Validate to complete domain verification"
Write-Host "  5. Run the validation commands in Section 6 of this guide"
```

> **Note:** GitHub Actions deploys the files to Azure automatically after the push. Allow 1–2 minutes for the first deployment to complete before running validation commands.

---

### 3.7 Verify Deployment

Once DNS has propagated and the Azure custom domain shows as **Verified**, proceed to [Section 6 — Validate the File](#6-validate-the-file).

---

## 4. Option 2 — GitHub Pages

GitHub Pages is a free static hosting service built into GitHub. It supports custom domains and provisions HTTPS automatically via Let's Encrypt. You keep your own DNS provider and add A records to point your root domain directly at GitHub's servers.

**When to choose this option:** Your organization already has a GitHub account, or you want to avoid Azure entirely and keep the deployment as simple as possible.

> **Content-Type warning:** GitHub Pages serves extensionless files with `Content-Type: application/octet-stream` by default. Apple will reject a response with this header and enrollment will fail silently. Before directing any users to enroll, validate the `Content-Type` header using the commands in [Section 6](#6-validate-the-file). If the value is `application/octet-stream` instead of `application/json`, switch to Option 1 (Azure Static Web Apps) or Option 3 (Cloudflare Workers) — both support explicit `Content-Type` configuration.

**Requirement:** A public GitHub repository is required for GitHub Pages on the free plan. The repository content — including the service discovery JSON file — will be publicly accessible on the internet.

---

### 4.1 Prerequisites

| Tool | Purpose | Install Command (Windows) |
|---|---|---|
| Git | Push files to GitHub | `winget install Git.Git` |
| GitHub CLI | Create the repository, enable Pages, and configure the custom domain from the command line | `winget install GitHub.cli` |

After installing, authenticate GitHub CLI:

```powershell
gh auth login
```

---

### 4.2 Create the GitHub Repository and Files

The repository must contain exactly three files.

#### Manual

1. Sign in to [github.com](https://github.com)
2. Select the **+** icon and choose **New repository**
3. Fill in the repository details:
   - **Repository name:** Your domain, e.g., `yourdomain.com` (any name works; using the domain is a common convention that makes the repository's purpose immediately clear)
   - **Visibility:** Public — required for GitHub Pages on the free plan
4. Select **Create repository**

Create the three files in the GitHub web interface using **Add file > Create new file**:

**File 1: `.nojekyll`** (empty file at the repository root)

- **Name:** `.nojekyll`
- **Content:** Leave empty

This file disables Jekyll processing on GitHub Pages. Without it, GitHub Pages silently ignores all directories whose names begin with `.` — including `.well-known`. The service discovery URL returns HTTP 404 even after successful deployment if this file is missing.

**File 2: `.well-known/com.apple.remotemanagement`**

- **Name:** `.well-known/com.apple.remotemanagement`

  When you type `.well-known/` in the GitHub filename field and press `/`, GitHub automatically creates the folder.

- **Content:** The JSON for your environment (from Section 2), with your Tenant ID substituted:

```json
{"Servers":[{"Version":"mdm-byod","BaseURL":"https://manage.microsoft.com/EnrollmentServer/PostReportDeviceInfoForUEV2?aadTenantId=REPLACE:YOUR_ENTRA_TENANT_ID"}]}
```

**File 3: `CNAME`** (no file extension)

- **Name:** `CNAME`
- **Content:** Your domain on a single line with no trailing spaces:

```
yourdomain.com
```

This file tells GitHub Pages which custom domain to serve this repository on.

---

### 4.3 Enable GitHub Pages

1. In the repository, navigate to **Settings**
2. In the left sidebar, under **Code and automation**, select **Pages**
3. Under **Source**, select **Deploy from a branch**
4. Set **Branch** to `main` and **Folder** to `/ (root)`
5. Select **Save**

GitHub Pages publishes the site within 1–2 minutes. A confirmation banner shows the assigned URL (e.g., `https://your-org.github.io/yourdomain.com/`).

> **Note:** If you included the `CNAME` file in Section 4.2, GitHub Pages automatically detects and applies the custom domain. If not, enter your domain under **Custom domain** in Settings > Pages and select **Save**.

---

### 4.4 Configure Custom Domain and DNS

#### GitHub custom domain setting

1. Go to **Settings > Pages** in the repository
2. Under **Custom domain**, confirm your domain appears (e.g., `yourdomain.com`)
3. If it is blank, enter your domain and select **Save**
4. GitHub runs a DNS check and displays a warning until the DNS records are configured

#### Add DNS records in [your-dns-provider]

GitHub Pages requires four A records pointing the root domain at GitHub's servers. A CNAME record is not sufficient for a root/apex domain.

1. Sign in to [your-dns-provider] and navigate to **My Products**
2. Find your domain and select **DNS** or **Manage DNS**
3. Add the following four A records:

| Type | Name | Value | TTL |
|---|---|---|---|
| A | `@` | `185.199.108.153` | 1 Hour |
| A | `@` | `185.199.109.153` | 1 Hour |
| A | `@` | `185.199.110.153` | 1 Hour |
| A | `@` | `185.199.111.153` | 1 Hour |

4. Select **Save**

> **Note:** These are GitHub's current IP addresses for Pages custom domain hosting. If this guide is more than six months old, verify the addresses against GitHub's official documentation before adding them.

---

### 4.5 Enable HTTPS

1. Return to **Settings > Pages** in the repository
2. Wait for GitHub to detect the A records and provision a TLS certificate (5–30 minutes after DNS propagates)
3. Once **Enforce HTTPS** becomes available, select it

> **Note:** The **Enforce HTTPS** checkbox remains greyed out until the TLS certificate is provisioned. The certificate is only issued after GitHub detects the A records in DNS.

---

### 4.6 Automate with PowerShell and GitHub CLI

The script below creates the repository, writes all required files, pushes them, enables GitHub Pages, and sets the custom domain. After running the script, add the four A records to [your-dns-provider] manually — DNS provider changes cannot be automated without API credentials for your registrar account.

```powershell
# -----------------------------------------------------------------------
# CONFIGURE THESE VARIABLES BEFORE RUNNING
# -----------------------------------------------------------------------
$TenantId  = "REPLACE:YOUR_ENTRA_TENANT_ID"          # Entra Directory (Tenant) ID
$Domain    = "REPLACE:yourdomain.com"                  # Your organization domain
$GitHubOrg = "REPLACE:YOUR_GITHUB_ORG_OR_USERNAME"    # GitHub org or personal account name
$RepoName  = $Domain                                   # Repository name (using domain is a common convention)
# -----------------------------------------------------------------------

# Service discovery JSON
$ServiceDiscoveryJson = '{"Servers":[{"Version":"mdm-byod","BaseURL":"https://manage.microsoft.com/EnrollmentServer/PostReportDeviceInfoForUEV2?aadTenantId=' + $TenantId + '"}]}'

# --- Step 1: Create public repository ---
Write-Host "Creating public GitHub repository '$GitHubOrg/$RepoName'..."
gh repo create "$GitHubOrg/$RepoName" --public `
    --description "Apple MDM Service Discovery for Intune Account-Driven User Enrollment"

# --- Step 2: Clone and populate ---
gh repo clone "$GitHubOrg/$RepoName"
Set-Location $RepoName

# .nojekyll — disables Jekyll so GitHub Pages serves the .well-known directory
New-Item -Name ".nojekyll" -ItemType File | Out-Null

# .well-known directory and service discovery file
New-Item -ItemType Directory -Name ".well-known" | Out-Null
Set-Content -Path ".well-known\com.apple.remotemanagement" `
            -Value $ServiceDiscoveryJson -Encoding utf8NoBOM

# CNAME file — sets the GitHub Pages custom domain
Set-Content -Path "CNAME" -Value $Domain -Encoding utf8NoBOM

# --- Step 3: Commit and push ---
git add .
git commit -m "Add Apple MDM service discovery files"
git push origin main
Write-Host "Files pushed to GitHub."

# --- Step 4: Enable GitHub Pages ---
Write-Host "Enabling GitHub Pages on '$RepoName'..."
gh api "repos/$GitHubOrg/$RepoName/pages" `
    --method POST `
    -f "source[branch]=main" `
    -f "source[path]=/"

# --- Step 5: Set custom domain ---
Write-Host "Setting custom domain to '$Domain'..."
gh api "repos/$GitHubOrg/$RepoName/pages" `
    --method PUT `
    -f "cname=$Domain"

Set-Location ..

Write-Host ""
Write-Host "--- Deployment complete ---"
Write-Host "Repository       : https://github.com/$GitHubOrg/$RepoName"
Write-Host "Custom domain    : $Domain"
Write-Host ""
Write-Host "Next steps — add these four A records to [your-dns-provider] DNS for '$Domain':"
Write-Host ""
Write-Host "  Type  Name  Value"
Write-Host "  A     @     185.199.108.153"
Write-Host "  A     @     185.199.109.153"
Write-Host "  A     @     185.199.110.153"
Write-Host "  A     @     185.199.111.153"
Write-Host ""
Write-Host "Then return to Settings > Pages in the repository and enable 'Enforce HTTPS'"
Write-Host "once the TLS certificate is provisioned (5-30 minutes after DNS propagates)."
Write-Host "Finally, run the validation commands in Section 6 of this guide."
```

---

### 4.7 Verify Deployment

Proceed to [Section 6 — Validate the File](#6-validate-the-file). Pay close attention to the `Content-Type` header — if it returns `application/octet-stream` instead of `application/json`, switch to Option 1 (Azure Static Web Apps) or Option 3 (Cloudflare Workers).

---

## 5. Option 3 — Cloudflare Workers

Cloudflare Workers serves the service discovery file as a serverless function on your domain — there are no files to host, no repository to maintain, and no static web app to manage. The Worker is a small JavaScript function that returns the JSON response directly with the correct `Content-Type` header.

**When to choose this option:** You want zero ongoing infrastructure, you want native `Content-Type` control without configuring a web framework, or you are already using Cloudflare for other infrastructure.

> **Important:** Cloudflare Workers can only serve traffic on domains where Cloudflare is the authoritative DNS provider. To use this option, you must change your domain's nameservers from [your-dns-provider] to Cloudflare. [your-dns-provider] remains your domain registrar and you continue to renew the domain through [your-dns-provider] — only DNS management moves to Cloudflare, which is free.

This section is divided into two phases: **DNS transfer** (manual — required first) and **Worker deployment** (manual or automated).

---

### 5.1 Prerequisites

Cloudflare Workers can be deployed using either of two approaches. Choose one.

#### Path A — Wrangler CLI (recommended)

Wrangler is Cloudflare's official CLI for deploying Workers. It is the most reliable approach and the standard tool for Cloudflare Worker development.

| Tool | Purpose | Install Command (Windows) |
|---|---|---|
| Node.js LTS | Required runtime for Wrangler | `winget install OpenJS.NodeJS.LTS` |
| Wrangler | Deploy Cloudflare Workers from the command line | `npm install -g wrangler` |

After installing, authenticate Wrangler with your Cloudflare account:

```powershell
wrangler login
```

This opens a browser window to authorize Wrangler. Complete the authorization in the browser, then return to the terminal.

#### Path B — Cloudflare REST API via PowerShell

Use this path if you prefer not to install Node.js or Wrangler. Everything runs through PowerShell using `Invoke-RestMethod`.

**What you need before running the script:**

- A Cloudflare API Token with the following permissions:
  - `Account > Workers Scripts > Edit`
  - `Zone > Workers Routes > Edit`
- Your Cloudflare **Account ID**
- Your Cloudflare **Zone ID**

**Creating an API token:**
1. In the Cloudflare dashboard, select your account name (top right) → **My Profile**
2. Go to **API Tokens** → **Create Token**
3. Select **Create Custom Token**
4. Add the two permissions listed above, scoped to your specific account and zone
5. Select **Continue to summary** → **Create Token**
6. Copy the token — it is shown only once

**Finding Account ID and Zone ID:**
Both values appear in the right sidebar of the Cloudflare dashboard when you select your domain. Copy them before running the script.

---

### 5.2 Transfer DNS from [your-dns-provider] to Cloudflare

> **Warning:** This step changes the authoritative DNS for your entire domain. Missing or incorrect DNS records after the nameserver switch can cause email delivery, websites, and other services on the domain to stop working. Take a screenshot of your complete [your-dns-provider] DNS record list before starting.

> **Note:** Nameserver changes cannot be automated without API credentials for your registrar account. This step must be completed manually through your DNS provider and Cloudflare portals. Worker deployment automation in Sections 5.5 and 5.6 requires this step to be fully complete first.

#### Step 1 — Add your domain to Cloudflare

1. Go to [cloudflare.com](https://cloudflare.com) and sign in (or create a free account)
2. From the dashboard home, select **Add a domain** (or **Add a site** on older dashboard layouts)
3. Enter your domain (e.g., `yourdomain.com`) and select **Continue**
4. Select the **Free** plan and select **Continue**

#### Step 2 — Review imported DNS records

Cloudflare automatically scans and imports your existing [your-dns-provider] DNS records. Review every record carefully before proceeding:

- Compare the imported list against your [your-dns-provider] DNS settings screenshot
- Confirm all **MX records** are present — these control email delivery
- Confirm all **SPF, DKIM, and DMARC TXT records** are present
- Confirm any **A records** for your domain's website are present
- Add any missing records manually before proceeding

> **Note:** The automatic DNS import is thorough but not always complete. Manual verification against your [your-dns-provider] settings is required. A missing record that is not noticed here will cause a service outage after the nameserver switch.

#### Step 3 — Copy the Cloudflare nameservers

After reviewing the DNS records, Cloudflare displays two nameserver addresses assigned to your account. They look similar to:

```
ada.ns.cloudflare.com
brad.ns.cloudflare.com
```

The exact names are unique to your Cloudflare account. Copy both values.

#### Step 4 — Update nameservers in [your-dns-provider]

1. Sign in to [your-dns-provider] and go to **My Products**
2. Find your domain and select the three-dot menu → **Manage DNS** or **Edit DNS**
3. Scroll to the **Nameservers** section and select **Change**
4. Select **Enter my own nameservers (advanced)**
5. Replace the existing [your-dns-provider] nameservers (the current nameserver addresses are shown in your DNS provider's dashboard) with the two Cloudflare nameservers copied in Step 3
6. Select **Save**

#### Step 5 — Wait for propagation

DNS propagation typically takes 1–24 hours, though Cloudflare often detects the change within minutes. Cloudflare sends an email confirmation when your domain becomes active.

In the Cloudflare dashboard, the domain status changes from **Pending** to **Active** once propagation is complete. Do not proceed to Worker deployment until the status is **Active**.

---

### 5.3 Create the Cloudflare Worker

> **Note:** Complete Section 5.2 and confirm the domain status is **Active** in Cloudflare before creating the Worker.

#### Manual

1. In the Cloudflare dashboard, select your domain
2. In the left navigation menu, go to **Workers & Pages**
3. Select **Create** → **Create Worker**
4. Enter a name for the Worker (e.g., `apple-mdm-discovery`) and select **Deploy**
5. Select **Edit code** to open the online code editor
6. Select all existing code in the editor and replace it with the following:

```javascript
export default {
  async fetch(request) {
    const url = new URL(request.url);

    if (url.pathname === '/.well-known/com.apple.remotemanagement') {
      const body = '{"Servers":[{"Version":"mdm-byod","BaseURL":"https://manage.microsoft.com/EnrollmentServer/PostReportDeviceInfoForUEV2?aadTenantId=REPLACE:YOUR_ENTRA_TENANT_ID"}]}';

      return new Response(body, {
        headers: {
          'Content-Type': 'application/json',
        },
      });
    }

    return new Response('Not found', { status: 404 });
  },
};
```

Replace `REPLACE:YOUR_ENTRA_TENANT_ID` with your actual Entra Directory (Tenant) ID.

**For US Government environments (GCC High / DoD):** replace `manage.microsoft.com` with `manage.microsoft.us` in the URL.

7. Select **Deploy** in the top-right corner of the code editor

---

### 5.4 Add the Worker Route to Your Domain

The Worker is deployed but responds only on a `*.workers.dev` URL. Attach it to your domain by adding a route.

#### Manual

1. In the Cloudflare dashboard, go to **Workers & Pages**
2. Select your Worker (`apple-mdm-discovery`)
3. Select the **Settings** tab → **Domains & Routes**
4. Select **Add**

**If your domain has no other hosted content (no existing website):**
- Select **Custom domain**
- Enter your root domain (e.g., `yourdomain.com`) and select **Add custom domain**
- Cloudflare adds the necessary DNS record automatically and routes all traffic for the domain through the Worker

**If your domain already serves other content:**
- Select **Route** to intercept only the service discovery path, leaving all other requests unaffected
- **Route:** `yourdomain.com/.well-known/com.apple.remotemanagement`
- **Zone:** Select your domain
- Select **Add route**

#### Confirm HTTPS

Cloudflare provisions HTTPS for all domains on the free plan automatically. No certificate configuration or management is required.

---

### 5.5 Automate with Wrangler CLI

The script below creates the Wrangler project directory, writes all required files, and deploys the Worker to your Cloudflare zone. Run it after completing Section 5.2 and confirming the domain is **Active** in Cloudflare.

```powershell
# -----------------------------------------------------------------------
# CONFIGURE THESE VARIABLES BEFORE RUNNING
# -----------------------------------------------------------------------
$TenantId   = "REPLACE:YOUR_ENTRA_TENANT_ID"   # Entra Directory (Tenant) ID
$Domain     = "REPLACE:yourdomain.com"           # Your organization domain (must be Active in Cloudflare)
$ScriptName = "apple-mdm-discovery"              # Cloudflare Worker name
# -----------------------------------------------------------------------

$ProjectDir = $ScriptName

# --- Step 1: Create project directory and src subfolder ---
New-Item -ItemType Directory -Force -Path "$ProjectDir\src" | Out-Null

# --- Step 2: Create wrangler.toml (Worker configuration) ---
$WranglerToml = @"
name = "$ScriptName"
main = "src/worker.js"
compatibility_date = "2024-01-01"

[[routes]]
pattern = "$Domain/.well-known/com.apple.remotemanagement"
zone_name = "$Domain"
"@
Set-Content -Path "$ProjectDir\wrangler.toml" -Value $WranglerToml -Encoding utf8NoBOM

# --- Step 3: Create the Worker script ---
$WorkerJs = @"
export default {
  async fetch(request) {
    const url = new URL(request.url);

    if (url.pathname === '/.well-known/com.apple.remotemanagement') {
      const body = JSON.stringify({
        Servers: [{
          Version: 'mdm-byod',
          BaseURL: 'https://manage.microsoft.com/EnrollmentServer/PostReportDeviceInfoForUEV2?aadTenantId=$TenantId'
        }]
      });

      return new Response(body, {
        headers: { 'Content-Type': 'application/json' },
      });
    }

    return new Response('Not found', { status: 404 });
  },
};
"@
Set-Content -Path "$ProjectDir\src\worker.js" -Value $WorkerJs -Encoding utf8NoBOM

# --- Step 4: Deploy the Worker ---
Write-Host "Deploying Worker to Cloudflare..."
Set-Location $ProjectDir
wrangler deploy
Set-Location ..

Write-Host ""
Write-Host "--- Deployment complete ---"
Write-Host "Worker name : $ScriptName"
Write-Host "Route       : $Domain/.well-known/com.apple.remotemanagement"
Write-Host ""
Write-Host "Run the validation commands in Section 6 of this guide to confirm the deployment."
```

> **What the script creates — and what it does not do automatically:**
>
> The script creates a local project folder (named after your Worker) containing two files: `wrangler.toml` (Worker configuration) and `src/worker.js` (the Worker code). These local files are used by `wrangler deploy` to upload the Worker to Cloudflare.
>
> After deployment completes, the local files and the live Worker on Cloudflare are **not linked**. Cloudflare hosts and runs the Worker independently — changes to the local files do not automatically appear in Cloudflare, and the Worker does not read from your local machine after the initial deploy.
>
> To update the Worker in the future (for example, to change the Tenant ID), you must either:
> - Edit the local files and run `wrangler deploy` again from the project folder, **or**
> - Connect the project to a GitHub repository and push the change — Cloudflare then redeploys automatically (see [Section 5.8](#58-optional--connect-to-github-for-source-control-and-auto-deployment))
>
> If you delete or move the local project folder, the Worker continues running on Cloudflare without interruption. You would lose the ability to redeploy changes from the command line unless you still have the files elsewhere (such as in a GitHub repository).

---

### 5.6 Automate with PowerShell and the Cloudflare API

Use this approach if you prefer not to install Node.js or Wrangler. Run it after completing Section 5.2 and creating an API token as described in Section 5.1 Path B.

```powershell
# -----------------------------------------------------------------------
# CONFIGURE THESE VARIABLES BEFORE RUNNING
# -----------------------------------------------------------------------
$TenantId   = "REPLACE:YOUR_ENTRA_TENANT_ID"          # Entra Directory (Tenant) ID
$Domain     = "REPLACE:yourdomain.com"                  # Your organization domain
$ApiToken   = "REPLACE:YOUR_CLOUDFLARE_API_TOKEN"       # Cloudflare API token
$AccountId  = "REPLACE:YOUR_CLOUDFLARE_ACCOUNT_ID"      # Right sidebar on your zone's overview page
$ZoneId     = "REPLACE:YOUR_CLOUDFLARE_ZONE_ID"         # Right sidebar on your zone's overview page
$ScriptName = "apple-mdm-discovery"                     # Cloudflare Worker script name
# -----------------------------------------------------------------------

$AuthHeaders = @{
    "Authorization" = "Bearer $ApiToken"
}

# Worker JavaScript — returns the JSON response with the correct Content-Type header
$WorkerScript = @"
export default {
  async fetch(request) {
    const url = new URL(request.url);
    if (url.pathname === '/.well-known/com.apple.remotemanagement') {
      const body = '{"Servers":[{"Version":"mdm-byod","BaseURL":"https://manage.microsoft.com/EnrollmentServer/PostReportDeviceInfoForUEV2?aadTenantId=$TenantId"}]}';
      return new Response(body, { headers: { 'Content-Type': 'application/json' } });
    }
    return new Response('Not found', { status: 404 });
  }
};
"@

# --- Step 1: Upload the Worker script ---
Write-Host "Uploading Worker script '$ScriptName'..."
$UploadResponse = Invoke-RestMethod `
    -Uri     "https://api.cloudflare.com/client/v4/accounts/$AccountId/workers/scripts/$ScriptName" `
    -Method  PUT `
    -Headers ($AuthHeaders + @{ "Content-Type" = "application/javascript" }) `
    -Body    $WorkerScript

if (-not $UploadResponse.success) {
    Write-Error "Worker upload failed: $($UploadResponse.errors | ConvertTo-Json)"
    exit 1
}
Write-Host "Worker script uploaded."

# --- Step 2: Add a route to bind the Worker to the service discovery path ---
Write-Host "Adding route for '$Domain/.well-known/com.apple.remotemanagement'..."
$RouteBody = @{
    pattern = "$Domain/.well-known/com.apple.remotemanagement"
    script  = $ScriptName
} | ConvertTo-Json

$RouteResponse = Invoke-RestMethod `
    -Uri     "https://api.cloudflare.com/client/v4/zones/$ZoneId/workers/routes" `
    -Method  POST `
    -Headers ($AuthHeaders + @{ "Content-Type" = "application/json" }) `
    -Body    $RouteBody

if (-not $RouteResponse.success) {
    Write-Error "Route creation failed: $($RouteResponse.errors | ConvertTo-Json)"
    exit 1
}
Write-Host "Route added (ID: $($RouteResponse.result.id))."

Write-Host ""
Write-Host "--- Deployment complete ---"
Write-Host "Worker name : $ScriptName"
Write-Host "Route       : $Domain/.well-known/com.apple.remotemanagement"
Write-Host ""
Write-Host "Run the validation commands in Section 6 of this guide to confirm the deployment."
```

---

### 5.7 Verify Deployment

Proceed to [Section 6 — Validate the File](#6-validate-the-file) once the Worker is deployed and the route shows as active in the Cloudflare dashboard.

---

### 5.8 (Optional) — Connect to GitHub for Source Control and Auto-Deployment

This section is optional. If you deployed your Worker using Section 5.5 (Wrangler CLI), your Worker is already live and running on Cloudflare — no further action is required to keep it operational.

Connecting to GitHub provides two additional benefits:

- **Source control:** Your Worker code and configuration are stored in a private repository, giving you a full change history and a recoverable copy independent of your local machine.
- **Auto-deployment:** When you push a change to the repository (for example, updating the Tenant ID), Cloudflare automatically redeploys the Worker. You do not need to run `wrangler deploy` manually again.

> **Important — deployment source:** Once you connect a GitHub repository to a Worker, Cloudflare uses GitHub as the deployment source going forward. Manual `wrangler deploy` commands from your local machine will still work, but the GitHub connection is the primary pipeline. Use one consistently to avoid confusion.

---

#### How the deployed Worker relates to your local files and GitHub

Once deployed, the Worker runs entirely on Cloudflare's edge network. It does not depend on your local project folder or GitHub to remain operational. The relationship between the three is:

| | Worker stays running | Can redeploy changes |
|---|---|---|
| Local files deleted or moved | Yes | No — unless GitHub has a copy |
| GitHub repository deleted | Yes | No — unless you have local files or re-create the repo |
| Both local files and GitHub deleted | Yes | No — code must be re-written from scratch |

This is why connecting to GitHub is recommended: it ensures you always have a recoverable, version-controlled copy of the Worker code separate from your local machine.

---

#### Prerequisites

| Tool | Purpose | Install Command (Windows) |
|---|---|---|
| Git | Initialize and manage the local repository | `winget install Git.Git` |
| GitHub CLI | Create the GitHub repository from the command line | `winget install GitHub.cli` |

After installing, authenticate GitHub CLI:

```powershell
gh auth login
```

---

#### Step 1 — Prepare the project for Git

If you used the automated script in Section 5.5, your project folder already contains `wrangler.toml` and `src/worker.js`. Navigate to that folder and create a `.gitignore` file to exclude local cache and secret files from being committed:

```powershell
Set-Location "apple-mdm-discovery"

Set-Content -Path ".gitignore" -Encoding utf8NoBOM -Value @'
.wrangler/
node_modules/
.env
'@
```

> **Note:** The `.wrangler/` directory is created locally by Wrangler during deployment and contains cached account credentials. It must not be committed to GitHub.

---

#### Step 2 — Initialize the repository and create the initial commit

```powershell
git init
git add .
git commit -m "Add Apple MDM service discovery Worker"
git branch -M main
```

> **Email privacy note:** If your GitHub account has email privacy protection enabled, Git commits that contain your real email address will be rejected on push. GitHub provides a no-reply address for this purpose in the format `ID+username@users.noreply.github.com`. You can find your specific address at **github.com → Settings → Emails → Keep my email addresses private**. Configure it before pushing:
>
> ```powershell
> git config user.email "YOUR_ID+YOUR_USERNAME@users.noreply.github.com"
> git config user.name "your-github-username"
> git commit --amend --reset-author --no-edit
> ```

---

#### Step 3 — Create a private GitHub repository and push

The following command creates a private repository under your GitHub account or organization, sets it as the remote, and pushes the `main` branch in one step:

```powershell
gh repo create apple-mdm-discovery --private --source . --remote origin --push
```

Replace `apple-mdm-discovery` with a repository name that suits your organization's naming convention. The repository can be named anything — it does not affect how Cloudflare or Apple resolve the service discovery URL.

After the push completes, your Worker code is stored at `https://github.com/your-org/apple-mdm-discovery` (private — not publicly accessible).

---

#### Step 4 — Connect the Worker to GitHub in the Cloudflare dashboard

This step links Cloudflare directly to the GitHub repository. From this point on, every push to the `main` branch triggers an automatic redeployment of the Worker.

1. In the Cloudflare dashboard, go to **Workers & Pages** and select your Worker (`apple-mdm-discovery`)
2. Select the **Settings** tab and scroll to the **Build** section
3. Under **Git repository**, select **GitHub**
4. GitHub opens an authorization page — **Install & Authorize Cloudflare Workers and Pages**:
   - Select **Only select repositories**
   - Use the **Select repositories** dropdown to choose your repository (`apple-mdm-discovery`)
   - Select **Install & Authorize**
5. Cloudflare redirects back to the dashboard and prompts for build configuration:
   - **Branch:** `main`
   - **Build command:** leave blank — no build step is required for plain JavaScript Workers
   - **Deploy command:** leave blank — Cloudflare handles deployment automatically
6. Save the configuration

Cloudflare performs an initial deployment from the connected repository to confirm the connection is working.

---

#### Making changes after connecting to GitHub

To update the Worker after the GitHub connection is established — for example, to change the Tenant ID:

1. Open `src/worker.js` in your editor and update the Tenant ID on the `BaseURL` line
2. Commit and push the change:

```powershell
git add src/worker.js
git commit -m "Update Entra Tenant ID"
git push
```

Cloudflare detects the push and redeploys the Worker automatically. The updated response is live within approximately 1 minute. No manual `wrangler deploy` command is needed.

---

## 6. Validate the File

Before directing any users to enroll, confirm the service discovery file is reachable, returns the correct headers, and contains the correct content. Run the following checks from any machine with `curl` installed (macOS Terminal, Linux, Windows Terminal, or PowerShell 7+).

**Check headers only:**

```
curl -I "https://yourdomain.com/.well-known/com.apple.remotemanagement"
```

**Check headers, following any redirects:**

```
curl -IL "https://yourdomain.com/.well-known/com.apple.remotemanagement"
```

Use `-L` to detect silent redirects (for example, the root domain redirecting to `www`) that would cause Apple's enrollment request to fail at the final URL.

**Simulate Apple's enrollment query (includes user and device parameters):**

```
curl -I "https://yourdomain.com/.well-known/com.apple.remotemanagement?user-identifier=firstname.surname@yourdomain.com&model-family=iPhone"
```

**Check the file content:**

```
curl "https://yourdomain.com/.well-known/com.apple.remotemanagement"
```

**PowerShell equivalent (if curl is not available):**

```powershell
$Uri      = "https://yourdomain.com/.well-known/com.apple.remotemanagement"
$Response = Invoke-WebRequest -Uri $Uri -Method Head
$Response.StatusCode
$Response.Headers["Content-Type"]
```

**Expected results checklist:**

- [ ] `HTTP/2 200` — the file is found and served successfully
- [ ] `content-type: application/json` — Apple will accept the response
- [ ] Response body contains the correct JSON with your actual Tenant ID (not a placeholder)
- [ ] No unexpected redirect in the `-IL` output, or the final destination after redirect is correct

---

## 7. Troubleshooting

| Symptom | Likely Cause | Fix |
|---|---|---|
| `curl` returns HTTP 404 | File path is wrong or deployment has not completed | Verify the file is at `/.well-known/com.apple.remotemanagement` with no `.json` extension; allow 1–5 minutes for GitHub Actions, GitHub Pages, or Wrangler deployments to complete |
| `curl` returns HTTP 200 but `Content-Type: application/octet-stream` | Web server is not serving the file as JSON | Add the `Content-Type` configuration: `staticwebapp.config.json` for Azure Static Web Apps; Worker response headers for Cloudflare Workers; GitHub Pages does not support custom response headers natively — switch to Option 1 or Option 3 |
| `curl` returns HTTP 301 or HTTP 302 redirect | Domain is redirecting (e.g., to `www`) before serving the file | Configure your hosting to serve the root domain directly, or ensure the redirect preserves the `/.well-known/` path and the final destination returns HTTP 200 |
| User sees **"Your Apple ID does not support the expected services on this device"** | File is unreachable, wrong `Content-Type`, or Tenant ID is incorrect | Run the `curl` validation above; confirm the Tenant ID in the JSON matches the value shown in the Entra admin center under **Identity > Overview** |
| [your-dns-provider] CNAME or A records not propagating | DNS TTL has not expired | Wait up to 48 hours; use [dnschecker.org](https://dnschecker.org) to check propagation from multiple geographic locations |
| Cloudflare domain still showing **Pending** | [your-dns-provider] nameservers have not been updated, or propagation is still in progress | Confirm the nameservers shown in [your-dns-provider] match exactly what Cloudflare provided; allow up to 24 hours for full propagation |
| Azure custom domain validation fails | TXT record has not propagated yet | Wait 30–60 minutes after adding the TXT record to [your-dns-provider] DNS before retrying validation in the Azure portal |
| GitHub Pages returns HTTP 404 even after DNS records are set | `.nojekyll` file is missing from the repository root | Without `.nojekyll`, Jekyll silently ignores all directories beginning with `.`, including `.well-known`; add an empty `.nojekyll` file to the repository root, commit, and push |
| Wrangler `deploy` returns an authentication error | Wrangler session has expired or the account has changed | Run `wrangler login` again to re-authenticate with your Cloudflare account |
| Cloudflare API script returns `Route creation failed` | Worker script upload failed, or Account ID / Zone ID is incorrect | Confirm `AccountId` and `ZoneId` match the values in the Cloudflare dashboard right sidebar; verify the API token has both `Workers Scripts:Edit` and `Workers Routes:Edit` permissions scoped to the correct zone |

---

*This document is part of the UniFy Endpoint – Microsoft Intune iOS/iPadOS Baseline series. Document maintained by Yoennis Olmo - Sr. Modern Work Consultant.*
