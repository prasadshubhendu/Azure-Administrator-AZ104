# Azure Custom Script Extension — Portal Deployment Guide

The **Custom Script Extension** for Azure Virtual Machines downloads and executes scripts on Azure VMs.  
This is often used for software installation, configuration, or running post-deployment tasks.

---

##  Prerequisites

- An existing **Azure Virtual Machine** (Windows or Linux).
- The VM must have:
  - **Network access** to the script location (Azure Storage, GitHub, or public URL).
  - Proper OS type (Linux uses `Microsoft.Azure.Extensions.CustomScript`; Windows uses `Microsoft.Compute.CustomScriptExtension`).
- Script file(s) accessible via:
  - **Azure Storage Blob** (SAS token if private),
  - **GitHub raw file link**, or
  - **Publicly accessible URL**.
- (Optional) Storage account with container for scripts.

---

##  1) Upload the script (if needed)

1. Go to your **Storage Account** → **Containers** → **+ Container**.
2. Upload your `.sh` (Linux) or `.ps1` (Windows) script.
3. If container is private, generate a **SAS URL** with read permissions.

---

##  2) Add Custom Script Extension via Azure Portal

1. **Sign in** to the [Azure Portal](https://portal.azure.com).
2. Navigate to your **Virtual Machine** → **Extensions + applications**.
3. Click **+ Add** → choose:
   - **Custom Script Extension** (Windows),
   - or **Custom Script for Linux** (Linux).
4. In the **Install extension** wizard:
   - **Script location**:
     - Select **Browse** to pick from your Storage account,
     - Or enter the **URL** (e.g., GitHub raw link or blob SAS link).
   - (Optional) Add multiple script files (e.g., supporting libraries).
5. Review + Create → wait for the extension deployment to complete.

---

##  3) Example Usage

### Linux VM
- Script name: `install-nginx.sh` (must have execute permission)
```bash
#!/bin/bash
sudo apt update
sudo apt install -y nginx
```
Windows VM
Script name: InstallIIS.ps1

```
powershell

Install-WindowsFeature -name Web-Server -IncludeManagementTools
```
## 4) Key Parameters to Know
Publisher:

Linux: Microsoft.Azure.Extensions

Windows: Microsoft.Compute

Type:

Linux: CustomScript

Windows: CustomScriptExtension

FileUris: Array of URLs to script files.

commandToExecute: Command string to run after download (must match OS syntax).

## 5) Post-Deployment Verification
Go to VM → Extensions + applications → confirm Provisioning succeeded.

Check logs inside the VM:

Linux: /var/lib/waagent/custom-script/download/

Windows: C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\

## 6) Troubleshooting
Extension fails:

Verify script URL accessibility from VM.

Ensure proper file permissions (chmod +x for Linux).

Check that commandToExecute matches OS shell (Bash for Linux, PowerShell for Windows).

Network restrictions:

If VM is in a restricted subnet, allow outbound access to script location.

Multiple runs:

To rerun, remove the extension and add it again, or update its settings.

7) Clean Up
Removing the extension does not delete the files created by the script.

Delete uploaded script files from Storage if no longer needed.

Summary Checklist
 Script uploaded or hosted in accessible location

 VM network can reach the script URL

 Correct OS-specific extension chosen

 commandToExecute tested locally before deploying

 Logs verified after execution

