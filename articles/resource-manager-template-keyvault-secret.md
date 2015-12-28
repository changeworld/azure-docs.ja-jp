<properties
   pageTitle="キーコンテナー内のシークレットのためのリソース マネージャーのテンプレート | Microsoft Azure"
   description="キー コンテナーのシークレット用のリソース マネージャーのスキーマを示します。"
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
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

| 名前 | 型 | 必須 | 使用できる値 | 説明 |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | あり | キー コンテナーの子ソースとして:<br />**secrets**<br /><br />最上位リソースとして:<br />**Microsoft.KeyVault/vaults/secrets** | 作成するリソースの種類。 |
| apiVersion | enum | あり | **2015-06-01** <br /> **2014-12-19-preview** | リソースの作成に使用する API バージョン。 | 
| name | string | あり | | 作成するシークレットの名前。シークレットをキー コンテナーの子リソースとしてデプロイする場合は、シークレットの名前を指定するだけです。シークレットを最上位リソースとしてデプロイする場合、名前の形式は **{key-vault-name}/{secret-name}** にする必要があります。 |
| プロパティ | オブジェクト | あり | (下記参照) | 作成するシークレットの値を指定するオブジェクト。 |
| dependsOn | array | いいえ | リソース名またはリソースの一意識別子のコンマ区切りリスト。 | このリンクが依存するリソースのコレクション。シークレットがデプロイされるキー コンテナーが同じテンプレートにデプロイされる場合は、キー コンテナーの名前をこの要素に含めて、キー コンテナーが先にデプロイされるようにします。 |

### プロパティ オブジェクト

| 名前 | 型 | 必須 | 使用できる値 | 説明 |
| ---- | ---- | -------- | ---------------- | ----------- |
| 値 | string | あり | | キー コンテナーに格納するシークレットの値。このプロパティの値を渡すときは、**securestring** 型のパラメーターを使用します。 |

	
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
                   "description": "Tenant Id for the subscription and use assigned access to the vault. Available from the Get-AzureRMSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRMADUser or the Get-AzureRMADServicePrincipal cmdlets"
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
                "tags": { "displayName": "secret" },
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

- キー コンテナーの概要については、「[Azure Key Vault の概要](./key-vault/key-vault-get-started.md)」を参照してください。
- テンプレートをデプロイするときにキー コンテナーのシークレットを参照する例については、[デプロイ時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md)ことに関する記事を参照してください。

<!---HONumber=AcomDC_1217_2015-->