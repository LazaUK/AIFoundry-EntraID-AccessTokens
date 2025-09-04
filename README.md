# Azure Entra ID: Decoding JWT Access Token for Azure AI Foundry authentication & authorisation

Azure Entra ID (formerly Azure Active Directory) issues JWT access tokens that contain various claims, including group memberships. This guide walks through how to obtain such a token and decode it using Python to verify if required groups are present.

> [!NOTE]
> This is especially useful when troubleshooting access issues or validating conditional access policies based on group claims.

## 📑 Table of Contents:
- (1. How to Obtain an Access Token from Entra ID)[#1-how-to-obtain-an-access-token-from-entra-id]
#2-how-to-decode-the-token-using-python
#3-how-to-interpret-group-claims
#4-troubleshooting-large-group-claims
#5-references--further-reading

## 1. How to Obtain an Access Token from Entra ID
You can obtain an access token using various flows (e.g., client credentials, authorisation code). For testing purposes, the easiest way to get a valid token for your Azure AI Foundry resource is to use the following Azure CLI command:
``` Shell
az account get-access-token --resource https://cognitiveservices.azure.com --query "accessToken" --output tsv
```





## 
