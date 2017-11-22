---
title: "Azure API Management で Azure 管理対象サービス ID を使用する | Microsoft Docs"
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
ms.openlocfilehash: ded0809fa90e98b2e845d328fbeec6d21507c46b
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2017
---
# <a name="use-azure-managed-service-identity-in-azure-api-management"></a>Azure API Management で Azure 管理対象サービス ID を使用する

> [!Note]
> 現在、Azure API Management の管理対象サービス ID はプレビュー段階です。

この記事では、API Management サービス インスタンスの管理対象サービス ID を作成する方法と、その他のリソースにアクセスする方法について説明します。 Azure Active Directory (Azure AD) によって生成された管理対象サービス ID によって、API Management インスタンスは、Azure AD で保護された他のリソース (Azure Key Vault など) に簡単かつ安全にアクセスすることができます。 この管理対象サービス ID は Azure によって管理され、ユーザーがシークレットをプロビジョニングしたりローテーションしたりする必要はありません。 Azure 管理対象サービス ID について詳しくは、[Azure リソースの管理対象サービス ID](../active-directory/msi-overview.md) に関するページをご覧ください。

## <a name="create-an-api-management-instance-with-an-identity-by-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用して、ID を持った API Management インスタンスを作成する

ID を持った API Management インスタンスは、リソース定義に次のプロパティを含めることによって作成できます。 

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

このプロパティは、API Management インスタンスの ID を作成して管理するよう Azure に命令するものです。 

たとえば、Azure Resource Manager テンプレート全体は次のようになります。

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

## <a name="obtain-a-certificate-from-azure-key-vault"></a>Azure Key Vault から証明書を取得する

次の例は、Azure Key Vault から証明書を取得する方法を示しています。 たとえば次の手順が実行されます。

1. ID を持った API Management インスタンスを作成します。
2. Azure Key Vault インスタンスのアクセス ポリシーを更新し、そこからシークレットを取得することを API Management インスタンスに許可します。
3. Key Vault インスタンスからの証明書を使用してカスタム ドメイン名を設定して API Management インスタンスを更新します。

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

Azure 管理対象サービス ID の詳細を確認します。

* [Azure リソースの管理対象サービス ID](../active-directory/msi-overview.md)
* [Azure リソース マネージャーのテンプレート](https://github.com/Azure/azure-quickstart-templates)

