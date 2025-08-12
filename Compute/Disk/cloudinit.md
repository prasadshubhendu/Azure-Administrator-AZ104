# Azure Cloud-Init — Portal Deployment Guide

**Cloud-Init** is a widely used method for cloud instance initialization, mainly on Linux VMs, to automate configuration tasks at first boot.  
Azure supports Cloud-Init on most Linux distributions (Ubuntu, CentOS, RHEL, Debian, etc.).

---

##  Prerequisites

- Azure subscription with permission to create resources.
- A Linux OS image that supports Cloud-Init (e.g., Ubuntu 20.04+, CentOS 7.5+, RHEL 7.5+).
- Your **Cloud-Init script** (`#cloud-config` format).
- Understanding of YAML indentation — incorrect spacing will cause Cloud-Init to fail.

---

##  1) Prepare Cloud-Init Script

- Save your Cloud-Init configuration locally as a `.txt` or `.yml` file.
- **Example** — install Apache and create an index page:
```yaml
#cloud-config
package_upgrade: true
packages:
  - apache2
runcmd:
  - systemctl enable apache2
  - systemctl start apache2
  - echo "<h1>Hello from Azure Cloud-Init</h1>" > /var/www/html/index.html


## 2) Deploy VM with Cloud-Init via Azure Portal
Sign in to the Azure Portal.

Create a VM:

Click Create a resource → Virtual Machine.

Basics tab:

Subscription: select yours

Resource group: new or existing

Virtual machine name: unique name

Region: select a supported Azure region

Image: Select a Linux image that supports Cloud-Init (e.g., Ubuntu LTS).

Size: choose your VM size.

Authentication: SSH public key or password.

Advanced tab:

Custom data → paste your Cloud-Init script contents.

(Optional) Enable Boot Diagnostics and other advanced options.

Networking tab:

Configure Virtual Network, Subnet, Public IP, and NSG rules.

Review + Create:

Validate settings.

Click Create to deploy.

## 3) Validate Cloud-Init Execution
Check provisioning logs inside the VM:

bash
Copy
Edit
# View cloud-init logs
sudo cat /var/log/cloud-init.log
sudo cat /var/log/cloud-init-output.log
Test if packages/services were installed:

bash
Copy
Edit
systemctl status apache2
Access the VM's public IP in a browser to verify the index page.

## 4) Common Cloud-Init Fields
package_update / package_upgrade: keep packages updated.

packages: list of packages to install.

runcmd: list of commands to run after boot.

write_files: create or modify files.

users: manage additional users.

apt / yum_repos: configure repositories.

## 5) Troubleshooting
YAML indentation issues will break execution — use spaces, not tabs.

Check logs in /var/log/cloud-init.log and /var/log/cloud-init-output.log.

Ensure the VM image actually supports Cloud-Init.

Large scripts should be tested on a smaller VM first to verify syntax and execution.

Summary Checklist
 Linux image supports Cloud-Init

 YAML script validated for syntax

 Script pasted into Advanced → Custom data during VM creation

 Logs checked after deployment

 Services/packages verified

