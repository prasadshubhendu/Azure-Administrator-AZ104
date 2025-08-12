# Adding and Removing Data Disks on Azure VM

This guide explains how to **add** and **remove** a data disk from an Azure Virtual Machine using both **Azure Portal** and **Azure CLI**.

---

## 1. Add a Data Disk

### **Using Azure Portal**
1. Sign in to [Azure Portal](https://portal.azure.com/).
2. Go to **Virtual Machines** and select your VM.
3. Stop the VM (recommended before adding/removing disks).
4. In the left menu, click on **Setting ** -> **Disks**.
5. Click **+ Create and attach a new disk** OR **+ Add data disk**.
6. Configure:
   - **Name**: Choose a name for the disk.
   - **Storage type**: Premium SSD LRS
   - **Size**: Set disk size (e.g., 128 GiB).
   - **Encryption**: Platform Managed key
7. Click **Save**.
8. Start the VM.
9. Inside the VM OS:
   - **Windows**: Open **Disk Management**, initialize the disk, create a volume, and assign a drive letter.
   - **Linux**: Use `lsblk` to view the disk, `fdisk` to partition, and `mkfs` to format.

---

### **Using Azure CLI**
```bash
# Variables
RESOURCE_GROUP="MyResourceGroup"
VM_NAME="MyVM"
DISK_NAME="myDataDisk"
DISK_SIZE=128
```

# Create the disk
	```
	az disk create \
  --resource-group $RESOURCE_GROUP \
  --name $DISK_NAME \
  --size-gb $DISK_SIZE \
  --sku Premium_LRS
	```
	
# Attach the disk
	```
	az vm disk attach \
  --resource-group $RESOURCE_GROUP \
  --vm-name $VM_NAME \
  --name $DISK_NAME
	```

## 2. Remove a Data Disk
	
	Using Azure Portal
	
	Sign in to Azure Portal.
	Go to Virtual Machines and select your VM.
	Stop the VM (recommended).
	In the left menu, click Disks.
	Find the disk you want to remove in the Data disks section.
	Click the X icon next to it.
	Click Save.
	Start the VM.
	(Optional) Go to Disks in the portal and delete the disk resource if it’s no longer needed.
	
	Using Azure CLI
	```
		# Detach the disk
	az vm disk detach \
	  --resource-group $RESOURCE_GROUP \
	  --vm-name $VM_NAME \
	  --name $DISK_NAME

	# Optional: Delete the disk
	az disk delete \
	  --resource-group $RESOURCE_GROUP \
	  --name $DISK_NAME \
	  --yes
	```

##3. Notes
	Always back up data before detaching disks.
	Premium SSD disks require the VM size to support Premium storage.
	Stopping the VM before disk changes is safer, though Azure allows hot attach/detach for some VM types.