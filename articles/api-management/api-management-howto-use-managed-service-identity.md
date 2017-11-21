---
title: "Azure API Management で Azure 管理対象サービス ID を使用する方法 | Microsoft Docs"
description: "API Management で Azure 管理対象サービス ID を使用する方法について説明します。"
services: api-management
documentationcenter: 
author: miaojiang
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: 70bf207cc173caf7d8cae3c4c9111ee2f427405b
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2017
---
# <a name="how-to-use-azure-managed-service-identity-in-azure-api-management"></a>Azure API Management で Azure 管理対象サービス ID を使用する方法

> [!Note]
> 現在、Azure API Management の管理対象サービス ID はプレビュー段階です。

このトピックでは、API Management サービス インスタンスの管理対象サービス ID を作成する方法と、その他のリソースにアクセスする方法について説明します。 Azure Active Directory (AAD) によって生成された管理対象サービス ID によって、API Management インスタンスは、AAD で保護された他のリソース (Azure Key Vault など) に簡単かつ安全にアクセスすることができます。 この管理対象サービス ID は Azure によって管理され、ユーザーがシークレットをプロビジョニングしたりローテーションしたりする必要はありません。 Azure 管理対象サービス ID について詳しくは、[管理対象サービス ID の概要](../active-directory/msi-overview.md)に関するページをご覧ください。

## <a name="creating-an-api-management-instance-with-an-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して ID を持った API Management インスタンスを作成する

API Management インスタンスは、リソース定義に次のプロパティを含めることにより、ID を使って作成できます。 

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

これは、API Management インスタンスの ID を作成して管理するよう Azure に命令するものです。 

たとえば、テンプレート全体は次のようになります。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "parameters": {
        "serviceName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The name of the api management service"
            }
        },
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "defaultValue": "admin@contoso.com",
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "minLength": 1,
            "defaultValue": "Contoso",
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "Developer",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-01",
            "name": "[parameters('serviceName')]",
            "type": "Microsoft.ApiManagement/service",
            "location": "[resourceGroup().location]",
            "tags": {},
            "sku": {
                "name": "[parameters('sku')]",
                "capacity": "[parameters('skuCount')]"
            },
            "properties": {
                "publisherEmail": "[parameters('publisherEmail')]",
                "publisherName": "[parameters('publisherName')]"
            },
            "identity": { 
                "type": "systemAssigned" 
            }
        }
    ]
}
```

## <a name="obtaining-a-certificate-from-azure-key-vault"></a>Azure Key Vault から証明書を取得する

以下の例では、Azure Key Vault から証明書を取得しています。 たとえば次の手順が実行されます。

* ID を持った API Management インスタンスを作成する
* Azure Key Vault インスタンスのアクセス ポリシーを更新し、そこからシークレットを取得することを API Management インスタンスに許可する
* Key Vault インスタンスからの証明書を使用してカスタム ドメイン名を設定して API Management インスタンスを更新する

> [!Important]
> 証明書のオブジェクト バージョンを指定しなかった場合、より新しいバージョンの証明書が Key Vault にアップロードされると、その後 API Management によって自動的に取得されます。 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
            
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",        
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    { 
      "apiVersion": "2017-05-10", 
      "name": "apimWithKeyVault", 
      "type": "Microsoft.Resources/deployments",
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": {
            "publisherEmail": { "value": "[parameters('publisherEmail')]"},
            "publisherName": { "value": "[parameters('publisherName')]"},
            "sku": { "value": "[parameters('sku')]"},
            "skuCount": { "value": "[parameters('skuCount')]"},
            "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
            "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
        }
      } 
    }]
}
```

## <a name="next-steps"></a>次のステップ

* Azure 管理対象サービス ID の詳細を確認する
  * [管理対象サービス ID の概要](../active-directory/msi-overview.md)
  * [Azure Resource Manager テンプレートの詳細情報](https://github.com/Azure/azure-quickstart-templates)

