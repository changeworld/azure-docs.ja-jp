<properties
   pageTitle="キーコンテナー内のシークレットのためのリソース マネージャーのテンプレート | Microsoft Azure"
   description="テンプレートを使って Key Vault のシークレットをデプロイするためのリソース マネージャー スキーマを示します。"
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# キー コンテナーのシークレットのテンプレート スキーマ

キー コンテナーに格納されるシークレットを作成します。多くの場合、このリソースの種類が[キー コンテナー](resource-manager-template-keyvault.md)の子リソースとしてデプロイされます。

## スキーマの形式

キー コンテナーのシークレットを作成するには、次のスキーマをテンプレートに追加します。シークレットは、キー コンテナーの子リソースまたは最上位リソースとして定義できます。キー コンテナーが同じテンプレートにデプロイされる場合、シークレットは子リソースとして定義できます。キー コンテナーが同じテンプレートにデプロイされない場合、またはリソースの種類でループすることで複数のシークレットを作成する必要がある場合、シークレットは最上位リソースとして定義する必要があります。

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## 値

次の表では、スキーマに設定する必要がある値について説明します。

| 名前 | 値 |
| ---- | ---- | 
| type | 列挙型<br />必須<br />Key Vault の子リソースとしてデプロイする場合は **secrets**。最上位リソースとしてデプロイする場合は <br />**Microsoft.KeyVault/vaults/secrets**。<br /><br />作成するリソースの種類。 |
| apiVersion | 列挙型<br />必須<br />**2015-06-01** または **2014-12-19-preview**<br /><br />リソースの作成に使用する API バージョン。 | 
| name | 文字列<br />必須<br />Key Vault の子リソースとしてデプロイする場合は 1 つの単語。既存の Key Vault に追加する最上位リソースとしてデプロイする場合は **{key-vault-name}/{secret-name}** 形式。<br /><br />作成するシークレットの名前。 |
| properties | オブジェクト<br />必須<br />[プロパティ オブジェクト](#properties)<br /><br />作成するシークレットの値を指定するオブジェクト。 |
| dependsOn | 配列<br />省略可能<br />リソース名またはリソースの一意識別子のコンマ区切りリスト。<br /><br />このリンクが依存するリソースのコレクション。シークレットがデプロイされるキー コンテナーが同じテンプレートにデプロイされる場合は、キー コンテナーの名前をこの要素に含めて、キー コンテナーが先にデプロイされるようにします。 |

<a id="properties" />
### プロパティ オブジェクト

| 名前 | 値 |
| ---- | ---- | 
| 値 | 文字列<br />必須<br /><br />Key Vault に格納するシークレット値。このプロパティの値を渡すときは、**securestring** 型のパラメーターを使用します。 |

	
## 例

最初の例では、シークレットをキー コンテナーの子リソースとしてデプロイします。

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
            "enableVaultForVolumeEncryption": {
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
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
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

2 番目の例では、シークレットを既存のキー コンテナーに格納される最上位リソースとしてデプロイします。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
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
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## 次のステップ

- Key Vault の全般的な情報については、「[Azure Key Vault の概要](./key-vault/key-vault-get-started.md)」を参照してください。
- テンプレートをデプロイするときにキー コンテナーのシークレットを参照する例については、[デプロイ時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md)ことに関する記事を参照してください。

<!---HONumber=AcomDC_0629_2016-->