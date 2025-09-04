# Azure Entra ID: Decoding JWT Access Token for Azure AI Foundry authentication & authorisation

Azure Entra ID (formerly Azure Active Directory) issues JWT access tokens, which contain various claims, e.g. group memberships. This guide walks you through on how to obtain such a token and decode it using Python.

> [!NOTE]
> This is especially useful when troubleshooting access issues or validating conditional access policies based on group claims.

## 📑 Table of Contents:
- [1. How to Obtain an Access Token from Entra ID](#1-how-to-obtain-an-access-token-from-entra-id)
- [2. How to Decode the Token Using Python](#2-how-to-decode-the-token-using-python)
- [3. Understanding Key JWT Claims](#3-understanding-key-jwt-claims)

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
print(json.dumps(claims, indent=4))
```

This will print all claims in a readable JSON format.

``` JSON
{
  "aud": "https://cognitiveservices.azure.com",
  "iss": "https://sts.windows.net/<tenant-id>/",
  "iat": 1756999941,
  "nbf": 1756999941,
  "exp": 1757004709,
  "acr": "1",
  "aio": "<anonymised>",
  "altsecid": "<anonymised>",
  "amr": [
    "rsa",
    "mfa"
  ],
  "appid": "<application-id>",
  "appidacr": "0",
  "deviceid": "<anonymised>",
  "email": "<user>@domain.com",
  "family_name": "<surname>",
  "given_name": "<first-name>",
  "groups": [
    "<anonymised>",
    "<anonymised>",
    "<anonymised>",
    "<anonymised>",
    "<anonymised>",
    "<anonymised>"
  ],
  "idp": "https://sts.windows.net/<directory-id>/",
  "idtyp": "user",
  "ipaddr": "<anonymised>",
  "name": "<first-name> <surname>",
  "oid": "<object-id>",
  "puid": "<anonymised>",
  "pwd_url": "https://portal.microsoftonline.com/ChangePassword.aspx",
  "rh": "<anonymised>",
  "scp": "user_impersonation",
  "sid": "<anonymised>",
  "sub": "<anonymised>",
  "tid": "<tenant-id>",
  "unique_name": "<user>@domain.com",
  "uti": "<anonymised>",
  "ver": "1.0",
  "wids": [
    "<anonymised>"
  ],
  "xms_ftd": "<anonymised>",
  "xms_idrel": "1 30"
}
```

## 3. Understanding Key JWT Claims
Below is a brief explanation of some commonly seen claims in Azure Entra ID access tokens:

| JWT Claim                                    | Description                                                                     |
| :------------------------------------------- | :------------------------------------------------------------------------------ |
| `iss`                                        | Issuer — the token authority (usually your Entra ID tenant).                    |
| `iat` / `nbf` / `exp`                        | Issued At / Not Before / Expiry — timestamps defining token validity.           |
| `appid`                                      | Application ID of the client requesting the token.                              |
| `scp`                                        | Scope — delegated permissions granted to the token (e.g. `user_impersonation`). |
| `groups`                                     | List of group object IDs the user belongs to. Used for access control.          |
| `amr`                                        | Authentication Methods References — e.g. `mfa` means multi-factor was used.     |
| `oid`                                        | Object ID of the user in Entra ID.                                              |
| `tid`                                        | Tenant ID — identifies the Azure AD directory.                                  |
| `email`, `name`, `given_name`, `family_name` | User identity attributes.                                                       |
| `xms_ftd`, `xms_idrel`, `rh`, `aio`          | Internal Microsoft claims used for token tracing and session management.        |
| `wids`                                       | Well-known IDs — e.g. directory roles like Global Administrator.                |

> [!TIP]
> You can cross-reference `groups` and `wids` using Microsoft Graph API to resolve them to human-readable names.
