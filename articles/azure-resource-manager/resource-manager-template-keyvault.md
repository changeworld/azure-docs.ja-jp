---
title: "Key Vault の Resource Manager テンプレート | Microsoft Docs"
description: "テンプレートを使って Key Vault をデプロイするためのリソース マネージャー スキーマを示します。"
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: wpickett
editor: 
ms.assetid: 96433b1a-68ee-4292-85b6-a581618b921b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: f2d009477a614c3b2876ce98a355d3775abf772b
ms.openlocfilehash: 04f2d5d8e501ebf41cf95ea925d238f64b096c1d
ms.lasthandoff: 02/27/2017


---
# <a name="key-vault-template-schema"></a>Key Vault テンプレートのスキーマ
Key Vault を作成します。

## <a name="schema-format"></a>スキーマの形式
Key Vault を作成するには、テンプレートのリソース セクションに次のスキーマを追加します。

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForDiskEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## <a name="values"></a>値
次の表では、スキーマに設定する必要がある値について説明します。

| 名前 | 値 |
| --- | --- |
| type |列挙型<br />必須<br />**Microsoft.KeyVault/vaults**<br /><br />作成するリソースの種類。 |
| apiVersion |列挙型<br />必須<br />**2015-06-01** または **2014-12-19-preview**<br /><br />リソースの作成に使用する API バージョン。 |
| 名前 |string<br />必須<br />Azure 全体で一意の名前。<br /><br />作成する Key Vault 名。 以下の例に示すように、名前付け規則で [uniqueString](resource-group-template-functions.md#uniquestring) 関数を使用して一意の名前を作成することを検討してください。 |
| location |string<br />必須<br />Key Vault の有効なリージョン。 有効なリージョンを確認するには、「[サポートされているリージョン](resource-manager-supported-services.md#supported-regions)」を参照します。<br /><br />Key Vault をホストするリージョン。 |
| プロパティ |オブジェクト<br />必須<br />[プロパティ オブジェクト](#properties)<br /><br />作成する Key Vault の種類を指定するオブジェクト。 |
| resources |array<br />省略可能。<br />使用できる値: [Key Vault のシークレット リソース](resource-manager-template-keyvault-secret.md)<br /><br />Key Vault の子リソース。 |

<a id="properties" />

### <a name="properties-object"></a>プロパティ オブジェクト
| 名前 | 値 |
| --- | --- |
| enabledForDeployment |Boolean<br />省略可能。<br />**true** または **false**<br /><br />仮想マシン デプロイまたは Service Fabric デプロイで Key Vault が有効かどうかを指定します。 |
| enabledForTemplateDeployment |Boolean<br />省略可能。<br />**true** または **false**<br /><br />Resource Manager テンプレート デプロイで Key Vault を使用できるかどうかを指定します。 詳細については、「 [」デプロイメント時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md) |
| enabledForDiskEncryption |BOOLEAN<br />省略可能。<br />**true** または **false**<br /><br />Key Vault でボリュームの暗号化が有効かどうかを指定します。 |
| tenantId |String<br />必須<br />**Globally-unique identifier**<br /><br />サブスクリプションのテナント ID。 これは、 [Get-AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) PowerShell コマンドレットまたは **azure account show** Azure CLI コマンドで取得できます。 |
| accessPolicies |array<br />必須<br />[accessPolicies object](#accesspolicies)<br /><br />ユーザーまたはサービス プリンシパルのアクセス許可を指定した、最大 16 オブジェクトの配列。 |
| sku |オブジェクト<br />必須<br />[sku object](#sku)<br /><br />Key Vault の SKU。 |

<a id="accesspolicies" />

### <a name="propertiesaccesspolicies-object"></a>properties.accessPolicies オブジェクト
| 名前 | 値 |
| --- | --- |
| tenantId |String<br />必須<br />**Globally-unique identifier**<br /><br />このアクセス ポリシーの **objectId** を含む Azure Active Directory テナントのテナント ID。 |
| objectId |String<br />必須<br />**Globally-unique identifier**<br /><br />Key Vault にアクセスできるようになる Azure Active Directory ユーザーまたはサービス プリンシパルのオブジェクト ID。 この値は、[Get-AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) または [Get-AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) PowerShell コマンドレットか、**azure ad user** または **azure ad sp** Azure CLI コマンドで取得できます。 |
| アクセス許可 |オブジェクト<br />必須<br />[アクセス許可オブジェクト](#permissions)<br /><br />この Key Vault に付与される、Active Directory オブジェクトに対するアクセス許可。 |

<a id="permissions" />

### <a name="propertiesaccesspoliciespermissions-object"></a>properties.accessPolicies.permissions オブジェクト
| 名前 | 値 |
| --- | --- |
| キー |array<br />必須<br />**all**、**backup**、**create**、**decrypt**、**delete**、**encrypt**、**get**、**import**、**list**、**restore**、**sign**、**unwrapkey**、**update**、**verify**、**wrapkey**<br /><br />この Key Vault 内のキーに付与される、Active Directory オブジェクトに対するアクセス許可。 この値は、1 つ以上の許可される値の配列として指定する必要があります。 |
| secrets |array<br />必須<br />**all**、**delete**、**get**、**list**、**set**<br /><br />この Key Vault 内のシークレットに付与される、Active Directory オブジェクトに対するアクセス許可。 この値は、1 つ以上の許可される値の配列として指定する必要があります。 |

<a id="sku" />

### <a name="propertiessku-object"></a>properties.sku オブジェクト
| 名前 | 値 |
| --- | --- |
| 名前 |列挙型<br />必須<br />**standard** または **premium** <br /><br />使用する KeyVault のサービス レベル。  Standard は、シークレットとソフトウェアで保護されたキーをサポートしています。  Premium は、さらに HSM で保護されたキーもサポートしています。 |
| family |列挙型<br />必須<br />**A** <br /><br />使用する SKU ファミリ。 |

## <a name="examples"></a>例
次に、Key Vault とシークレットをデプロイする例を示します。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForDiskEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForDiskEncryption": "[parameters('enableVaultForDiskEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

## <a name="quickstart-templates"></a>クイック スタート テンプレート
Key Vault は、次のクイック スタート テンプレートによってデプロイされます。

* [Key Vault の作成](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)

## <a name="next-steps"></a>次のステップ
* Key Vault の全般的な情報については、「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md)」をご覧ください。
* テンプレートをデプロイメントするときに Key Vault のシークレットを参照する例については、「 [デプロイ時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md)」を参照してください。


