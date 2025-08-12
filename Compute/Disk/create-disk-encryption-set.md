# Create Azure Disk Encryption Set (Portal)

This guide walks you through creating a **Disk Encryption Set (DES)** in the Azure Portal with a customer-managed key (CMK).

---

##  Prerequisites

- An **Azure Key Vault** with:
  - **Soft delete** and **purge protection** enabled (mandatory).  
  - A key created (e.g., RSA 2048, 3072, or 4096 bits).
  - Appropriate access policies or **Azure RBAC permissions** set.:contentReference[oaicite:0]{index=0}
- The Key Vault and the Disk Encryption Set **must be in the same region**; they can reside in different subscriptions in the same tenant.:contentReference[oaicite:1]{index=1}
- Required permissions: typically **Key Vault Administrator**, **Key Vault Crypto Service Encryption User**, or via **RBAC**.:contentReference[oaicite:2]{index=2}

---

##  Steps via Azure Portal

1. **Sign in** to the [Azure portal](https://portal.azure.com).

2. **Create or verify Key Vault** (if not already):
   - Go to **Key Vaults** → **+ Create**.
   - Fill in **Subscription**, **Resource group**, **Vault name**, **Region**.
   - Select **Pricing tier** (Standard/Premium).
   - Ensure **Soft-delete** is enabled and set retention (7–90 days).
   - Enable **Purge protection** (irreversible).:contentReference[oaicite:3]{index=3}
   - Create a key in the vault (e.g., RSA 2048).:contentReference[oaicite:4]{index=4}
   - Assign proper access (Access Policies or RBAC) to allow DES to wrap/unwrap keys.:contentReference[oaicite:5]{index=5}
   - Go to keyvault -> IAM -> Add role assignment -> keyvault admin -> select member -> assign role.
   - Goto Keyvault -> Object -> Keys -> Generate

3. **Create Disk Encryption Set**:

   - Search for **Disk Encryption Sets** in the portal and select **+ Create**.:contentReference[oaicite:6]{index=6}
   - **Basics**:
     - Choose **Subscription**, **Resource group**, **Name**, and **Region** (same as Key Vault).
     - Set **Encryption type**:
       - `Encryption at-rest with a customer-managed key` or  
       - `Double encryption with platform-managed and customer-managed keys`.:contentReference[oaicite:7]{index=7}
     - Select your **Key Vault**, **Key**, and its **Key Version**.:contentReference[oaicite:8]{index=8}
     - (Optional) Enable **Auto key rotation** for CMK.:contentReference[oaicite:9]{index=9}
   - Complete the rest of the form (Tags, Review + Create) and deploy.:contentReference[oaicite:10]{index=10}

4. **Post-deployment**:
   - After deployment, go to the DES resource and click on any notification/warning to **grant Key Vault permissions** to the DES’s managed identity.:contentReference[oaicite:11]{index=11}
	- Go to disk ->detach the disk -> change the encryption of the disk -> attach the disk.
---

##  Key Permissions Flow

- When you create a Disk Encryption Set, Azure assigns a **system-assigned managed identity** to it.:contentReference[oaicite:12]{index=12}
- Grant that identity **WrapKey** and **UnwrapKey** permissions on the Key Vault key.:contentReference[oaicite:13]{index=13}
- If **Auto key rotation** is enabled, managed disks tied to the DES will automatically update to use the new key version within approximately an hour.:contentReference[oaicite:14]{index=14}

---

##  Troubleshooting Tips

- Ensure **Key Vault and DES are co-located** in the same region (and preferably subscription).:contentReference[oaicite:15]{index=15}
- Verify **permissions**: DES’s managed identity needs `wrapKey` / `unwrapKey` access.:contentReference[oaicite:16]{index=16}
- Once created, you **cannot change the encryption type** of a Disk Encryption Set—you must create a new one if needed.:contentReference[oaicite:17]{index=17}

---

##  Cleanup

- To remove the Disk Encryption Set, simply delete the resource. (Ensure no resources depend on it.)
- If testing key rotation or reuse, monitor for VM or disk downtime if keys are disabled or expire.:contentReference[oaicite:18]{index=18}

---

##  Summary Checklist

- [ ] Key Vault created with soft-delete & purge protection  
- [ ] Encryption Key created in Key Vault  
- [ ] Access policies or RBAC permissions assigned to the DES identity  
- [ ] Disk Encryption Set created with desired encryption type  
- [ ] Auto key rotation enabled (optional)  
- [ ] Permissions granted via post-deployment alert

---


