---
title: データ サイエンス仮想マシンにアクセス資格情報を安全に保存する - Azure | Microsoft Docs
description: データ サイエンス仮想マシンにアクセス資格情報を安全に保存します。
keywords: ディープ ラーニング, AI, データ サイエンス ツール, データ サイエンス仮想マシン, 地理空間分析, チーム データ サイエンス プロセス
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 9ec734cf456050250396b00aa09b61bace7e9aa0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830274"
---
# <a name="store-access-credentials-on-the-data-science-virtual-machine-securely"></a>データ サイエンス仮想マシンにアクセス資格情報を安全に保存する

クラウド アプリケーションの構築時における一般的な課題は、クラウド サービスへの認証用のコードに必要な資格情報を管理する方法です。 これらの資格情報を安全に保つことは重要なタスクです。 こうした資格情報が開発者ワークステーションに表示されず、ソース管理で検査されないことが理想的です。 

[管理対象サービス ID (MSI)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) は、Azure Active Directory (Azure AD) で自動的に管理されている ID を Azure サービスに付与することで、この問題を簡単に解決します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに対して認証することができます。 資格情報をセキュリティ保護する 1 つの一般的なパターンは MSI と、シークレットおよび暗号化キーを安全に保存するための管理された Azure サービスである [Azure Keyvault](https://docs.microsoft.com/azure/key-vault/) を組み合わせて使用することです。 管理されたサービス ID を使用して Key Vault にアクセスし、Key Vault から承認済みのシークレットと暗号化キーを取得することができます。 

MSI と Key Vault のドキュメントは、これらのサービスの詳細についての包括的なリソースです。 この記事の残りの部分は、データ サイエンス仮想マシン (DSVM) での MSI と Key Vault の基本的な使用方法を示す入門書になります。 

## <a name="walkthrough-of-using-msi-to-securely-access-azure-resources"></a>MSI を使用して Azure リソースに安全にアクセスするためのチュートリアル

## <a name="1-create-msi-on-the-dsvm"></a>1.DSVM に MSI を作成する 


```
# Pre-requisites: You have already created a Data Science VM in the usual way

# Create identity principal for the VM
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal id of the DSVM
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="2-assign-keyvault-access-permission-to-vm-principal"></a>2.VM プリンシパルに Keyvault アクセス許可を割り当てる
```
# Pre-requisites: You have already create an empty Key Vault resource on Azure using Portal or Azure CLI 

# Assign only get and set permission but not the capability to list the keys
az keyvault set-policy --object-id <PrincipalID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="3-access-a-secret-in-the-keyvault-from-the-dsvm"></a>手順 3.DSVM から Keyvault 内のシークレットにアクセスする

```
# Get the access token for VM
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access Keyvault using access token. 
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="4-access-storage-keys-from-the-dsvm"></a>4.DSVM からストレージ キーにアクセスする

```
# Pre-requisites: You have granted your VM's MSI access to use storage account access keys based on instruction from this article:https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage that describes this process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

#Now you can access the data in the storage account from the retrieved Storage account keys
```
## <a name="5-access-keyvault-from-python"></a>5.Python から Keyvault にアクセスする

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a KeyVault client
key_vault_client = KeyVaultClient(
credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
key_vault_uri,  # Your KeyVault URL
"SQLPasswd",       # Name of your secret that already exists in the Key Vault
""              # The version of the secret. Empty string for latest
)
print("My secret value is {}".format(secret.value))
```

## <a name="6-azure-cli-access-from-vm"></a>6.VM からの Azure CLI のアクセス

```
# With a Managed Service Identity setup on the DSVM, users on the DSVM can execute Azure CLI to perform the authorized functions. Here are commands to access Key Vault from a Azure CLI without having to login to an Azure account. 
# Pre-requisites: MSI is already setup on the DSVM as indicated earlier and specific permission like accessing storage account keys, reading specific secrets and writing new secrets is provided to the MSI . 

# Authenticate to Azure CLI without requiring an Azure Account. 
az login --msi

# Retrieve a secret from Key Vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new Secret in Key Vault
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List Storage Account Access Keys
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
