# Azure VNet with Two Subnets and VM Communication

This guide explains how to create the network architecture shown in the diagram, where:
- A single **VNet** contains **two subnets**.
- Each subnet has a **VM with NIC and storage**.
- **NSGs** control traffic flow.
- A user connects from the internet to Subnet 1, and VMs in both subnets can communicate.

---

## **Architecture Diagram**

![vnet] (vnet_1.png)

---
## **Architecture Overview**
Internet --> VM-Subnet1 --> VM-Subnet2
- **VNet:** `VNet-Demo`
- **Subnet 1:** `10.0.1.0/24` (Public facing)
- **Subnet 2:** `10.0.2.0/24` (Private)
- **NSGs:** One per subnet
- **NICs:** Automatically created for each VM

---

## **Step-by-Step Setup**

### 1. Create the VNet and Subnets
1. In Azure Portal, click **Create a Resource** → **Virtual Network**.
2. **Basics**:
   - Name: `VNet-Demo`
   - Resource group: `RG-NIC-Demo`
   - Region: `East US` (or preferred)
3. **IP Addresses**:
   - Address space: `10.0.0.0/16`
   - Add `Subnet-1` → `10.0.1.0/24`
   - Add `Subnet-2` → `10.0.2.0/24`
4. **Review + Create**.

---

### 2. Create Network Security Groups (NSGs)
1. Create **NSG-Subnet1** and **NSG-Subnet2** in the same region.
2. In NSG Subnet1 → **Inbound security rules**:
   - Source: Any
   - Destination: <publicip>
   - Service: **ssh** port 22 or **rdp** port 3389
   - Action: **Allow**
3. Associate:
   - NSG-Subnet1 → `Subnet-1`
   - NSG-Subnet2 → `Subnet-2`

---

### 3. Create VM in Subnet 1
1. **Create VM**:
   - Name: `VM-Subnet1`
   - Region: same as VNet
   - Image: Windows/Linux (choice)
2. **Networking**:
   - VNet: `VNet-Demo`
   - Subnet: `Subnet-1`
   - Public IP: **Yes**
   - NIC NSG: **None** (using subnet NSG)
3. Optionally attach a data disk.
4. **Review + Create**.

---

### 4. Create VM in Subnet 2
1. **Create VM**:
   - Name: `VM-Subnet2`
   - VNet: `VNet-Demo`
   - Subnet: `Subnet-2`
   - Public IP: **No**
2. Optionally attach a data disk.
3. **Review + Create**.
4. **install nginx**

---

### 5. Configure NICs (Optional)
1. Go to each VM → Networking → Click NIC name.
2. Under **IP Configurations** → Set private IP to **Static** if required.

---

### 6. Test the Setup
- From your local machine:
  - Connect to `VM-Subnet1` using its Public IP.
- From inside `VM-Subnet1`:
  - Ping the private IP of `VM-Subnet2`.
  - Communication should succeed.
  - Curl http://<vm2 private ip>.

---

## **Result**
- User accesses VM in Subnet 1 from the internet.
- VMs in both subnets can communicate.
- Traffic flow is controlled by NSGs.

---

