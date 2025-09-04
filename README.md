# Azure Entra ID: Decoding JWT Access Token for Azure AI Foundry authentication & authorisation

Azure Entra ID (formerly Azure Active Directory) issues JWT access tokens, which contain various claims, e.g. group memberships. This guide walks you through on how to obtain such a token and decode it using Python.

> [!NOTE]
> This is especially useful when troubleshooting access issues or validating conditional access policies based on group claims.

## 📑 Table of Contents:
- (1. How to Obtain an Access Token from Entra ID)[#1-how-to-obtain-an-access-token-from-entra-id]
- (2. How to Decode the Token Using Python)[#2-how-to-decode-the-token-using-python]

## 1. How to Obtain an Access Token from Entra ID
You can obtain an access token using various flows (e.g., client credentials, authorisation code). For testing purposes, the easiest way to get a valid token for your Azure AI Foundry resource is to use the following Azure CLI command:

``` Shell
az account get-access-token --resource https://cognitiveservices.azure.com --query "accessToken" --output tsv
```

## 2. How to Decode the Token Using Python
Once you have the token, you can decode it using Python:

``` Python
import base64
import json

token = "<your_access_token_here>"
payload = token.split('.')[1]
payload += '=' * (-len(payload) % 4)
decoded = base64.urlsafe_b64decode(payload)
claims = json.loads(decoded)
print(json.dumps(claims, indent=2))
```

This will print all claims in a readable JSON format.
``` JSON

```
