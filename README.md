# Azure Static Website Deployment

This repository contains the necessary files and instructions to deploy a static website using Azure Storage Account. Follow this guide to quickly and cost-effectively host your static website on Azure.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Deployment Steps](#deployment-steps)
  - [1. Create an Azure Storage Account](#1-create-an-azure-storage-account)
  - [2. Enable Static Website Hosting](#2-enable-static-website-hosting)
  - [3. Upload Your Static Website Files](#3-upload-your-static-website-files)
  - [4. Configure Custom Domain (Optional)](#4-configure-custom-domain-optional)
- [Conclusion](#conclusion)
- [Additional Resources](#additional-resources)

## Prerequisites

Before you begin, ensure you have the following:

- An Azure account (If you don't have one, [create a free account](https://azure.microsoft.com/free/))
- Azure CLI installed ([Installation guide](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli))
- Your static website files ready for deployment

## Deployment Steps

### 1. Create an Azure Storage Account

```bash
# Log in to Azure CLI
az login

# Create a resource group (if you don't have one). Replace ```myResourceGroup``` by your desired resource group name.
az group create --name myResourceGroup --location eastus

# Create a storage account. Replace ```mystorageaccount``` by your desired storage account name (must be globally unique accross all azure storage account names) and replace  ```myResourceGroup``` by your desired resource group name.
az storage account create --name mystorageaccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2
```

### 2. Enable Static Website Hosting

```bash
# Enable static website hosting
az storage blob service-properties update --account-name mystorageaccount --static-website --index-document index.html --404-document 404.html

# Get the primary endpoint for your static website
az storage account show --name mystorageaccount --resource-group myResourceGroup --query "primaryEndpoints.web" --output tsv
```

Save the primary endpoint URL for later use.

### 3. Upload Your Static Website Files

```bash
# Create a local directory for your website files (if you haven't already)
mkdir mywebsite
cd mywebsite

# Create sample index.html and 404.html files (replace with your actual website files)
echo "<html><body><h1>Welcome to my Azure Static Website!</h1></body></html>" > index.html
echo "<html><body><h1>404 - Page not found</h1></body></html>" > 404.html

# Upload your website files to the $web container
az storage blob upload-batch --account-name mystorageaccount --auth-mode key --destination '$web' --source .
```

### 4. Configure Custom Domain (Optional)

1. Add a CNAME record in your domain's DNS settings pointing to your storage account's endpoint.

2. Enable HTTPS for custom domains:

```bash
az storage account update --name mystorageaccount --resource-group myResourceGroup --custom-domain yourdomain.com --enable-https-traffic-only true
```

## Conclusion

You have now successfully set up and deployed a static website using Azure Storage Account. Your website should be accessible at the primary endpoint URL you saved earlier. With the GitHub Actions workflow in place, any changes pushed to your main branch will automatically trigger a new deployment to your Azure Static Website.

## Additional Resources

- [Azure Storage Documentation](https://docs.microsoft.com/en-us/azure/storage/)
- [Static website hosting in Azure Storage](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-static-website)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

Remember to optimize your static files, use Azure CDN for better performance, and consider implementing Azure Front Door for advanced routing and security features as your website grows.
