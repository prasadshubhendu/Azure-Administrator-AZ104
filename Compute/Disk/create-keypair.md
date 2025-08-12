# Azure Key Vault Creation

This guide walks you through creating an Azure Key Vault using the Azure Portal, Azure CLI, or Azure PowerShell.

---

## 1. Azure Portal

1. Sign in to the [Azure portal](https://portal.azure.com).
2. Go to **Create a resource**, search for **Key Vault**, then click **Create**.
3. Enter:
  
   - **Subscription**: *your subscription*
   - **Resource Group**: *new or existing*
    - **key Vault Name**: *your-unique-name*
   - **Region**: *e.g., EastUS*
    - **Pricing Tier**: *Standard*
	-** soft delete** : *Enabled*
	-** days of retain deleted vault** : *90*
	-** Purge Protection ** : *Enabled*
4. Click **Review + Create**, then **Create**.
5. Once deployed, check the **Vault Name** and **Vault URI**.

---

## 2. Azure CLI

```
# Sign in (if not already)
az login

# Create resource group
az group create --name myResourceGroup --location EastUS

# Create Key Vault
az keyvault create --name <your-unique-keyvault-name> \
                   --resource-group myResourceGroup

# Note Vault URI from output
```