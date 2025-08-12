# Creating a Snapshot of a Data Disk and Attaching it to a New VM

This guide explains how to:
1. Create a **snapshot** of an existing Azure VM's data disk.
2. Create a **new VM**.
3. Create a disk from the snapshot and **attach it** to the new VM.

---

## 1. Create Snapshot of Existing Data Disk

### **Using Azure Portal**
1. Sign in to [Azure Portal](https://portal.azure.com/).
2. Go to **Virtual Machines** → select the VM that has the data disk.
3. Stop the VM (optional but recommended).
4. In the left menu, click **Disks**.
5. Under **Data disks**, click the data disk name.
6. In the disk's page, click **Create snapshot**.
7. Enter:
   - **Name**: e.g., `myDataDiskSnapshot`
   - **Snapshot type**: Full
   - **Storage type**: Standard HDD/SSD or Premium SSD
8. Click **Review + Create** → **Create**.

---

### **Using Azure CLI**
```bash
# Variables
RESOURCE_GROUP="MyResourceGroup"
DISK_NAME="myDataDisk"
SNAPSHOT_NAME="myDataDiskSnapshot"
```
# Create snapshot
```
az snapshot create \
  --resource-group $RESOURCE_GROUP \
  --name $SNAPSHOT_NAME \
  --source $DISK_NAME \
  --sku Standard_LRS
```

##2. Create New VM
Using Azure Portal
In the Azure Portal, click Create a resource → Virtual Machine.
Configure basic settings (name, region, size, OS image, authentication).
Create the VM without the extra data disk for now.
Once VM is deployed, go to the VM resource.

Using Azure CLI
```bash

# Create VM (example with Ubuntu)
az vm create \
  --resource-group $RESOURCE_GROUP \
  --name NewVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```
## 3. Create a Disk from the Snapshot
Using Azure Portal
	Search for Snapshots → select your snapshot.
	Click Create disk.
	Configure:
	Name: e.g., myDataDiskFromSnapshot
	Size: Keep same as original or increase.
	Performance tier: Match your needs.
	Click Review + Create → Create.

Using Azure CLI
```bash

NEW_DISK_NAME="myDataDiskFromSnapshot"

az disk create \
  --resource-group $RESOURCE_GROUP \
  --name $NEW_DISK_NAME \
  --source $SNAPSHOT_NAME \
  --sku Premium_LRS
```

## 4. Attach the New Disk to the New VM
Using Azure Portal
Go to the new VM → Disks.

Click + Add data disk.

Select the disk created from the snapshot.

Click Save.

Using Azure CLI
```bash

az vm disk attach \
  --resource-group $RESOURCE_GROUP \
  --vm-name NewVM \
  --name $NEW_DISK_NAME
```

## 5. Initialize the Disk in the New VM OS
Windows: Use Disk Management to bring the disk online and assign a drive letter.

Linux:

```bash

# List disks
lsblk

# Mount example (replace with actual device name)
sudo mkdir /mnt/mydisk
sudo mount /dev/sdc1 /mnt/mydisk

```

Notes
Snapshots are point-in-time backups and are charged based on size.

You can also use snapshots to migrate disks across regions (with additional steps).

Always ensure VM size supports the disk type (Premium/Standard).