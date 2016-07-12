<properties
   pageTitle="Key Vault のリソース マネージャー テンプレート | Microsoft Azure"
   description="テンプレートを使って Key Vault をデプロイするためのリソース マネージャー スキーマを示します。"
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
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# Key Vault テンプレートのスキーマ

Key Vault を作成します。

## スキーマの形式

Key Vault を作成するには、テンプレートのリソース セクションに次のスキーマを追加します。

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
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

## 値

次の表では、スキーマに設定する必要がある値について説明します。

| 名前 | 値 |
| ---- | ---- | 
| type | 列挙型<br />必須<br />**Microsoft.KeyVault/vaults**<br /><br />作成するリソースの種類。 |
| apiVersion | 列挙型<br />必須<br />**2015-06-01** または **2014-12-19-preview**<br /><br />リソースの作成に使用する API バージョン。 | 
| name | 文字列<br />必須<br />Azure 全体で一意の名前。<br /><br />作成する Key Vault の名前。以下の例に示すように、名前付け規則で [uniqueString](resource-group-template-functions.md#uniquestring) 関数を使用して一意の名前を作成することを検討してください。 |
| location | 文字列<br />必須<br />Key Vault の有効なリージョン。有効なリージョンを確認するには、「[サポートされているリージョン](resource-manager-supported-services.md#supported-regions)」を参照してください。<br /><br />Key Vault をホストするリージョン。 |
| properties | オブジェクト<br />必須<br />[properties オブジェクト](#properties)<br /><br />作成する Key Vault の種類を指定するオブジェクト。 |
| resources | 配列<br />省略可能<br />許可される値: [Key Vault のシークレット リソース](resource-manager-template-keyvault-secret.md)<br /><br />Key Vault の子リソース。 |

<a id="properties" />
### プロパティ オブジェクト

| 名前 | 値 |
| ---- | ---- | 
| enabledForDeployment | ブール値<br />省略可能<br />**true** または **false**<br /><br />仮想マシン デプロイまたは Service Fabric デプロイで Key Vault が有効かどうかを指定します。 |
| enabledForTemplateDeployment | ブール値<br /><br />省略可能**true** または **false**<br /><br />Resource Manager テンプレート デプロイで Key Vault を使用できるかどうかを指定します。詳細については、「[」デプロイメント時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md)」を参照してください。 |
| enabledForVolumeEncryption | ブール値<br />省略可能<br />**true** または **false**<br /><br />Key Vault でボリュームの暗号化が有効かどうかを指定します。 |
| tenantId | 文字列<br />必須<br />**グローバル一意識別子**<br /><br />サブスクリプションのテナント ID。これは、[Get-AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) PowerShell コマンドレットまたは **azure account show** Azure CLI コマンドで取得できます。 |
| accessPolicies | 配列<br />必須<br />[accessPolicies オブジェクト](#accesspolicies)<br /><br />ユーザーまたはサービス プリンシパルのアクセス許可を指定した、最大 16 オブジェクトの配列。 |
| sku | オブジェクト<br />必須<br />[sku オブジェクト](#sku)<br /><br />Key Vault の SKU。 |

<a id="accesspolicies" />
### properties.accessPolicies オブジェクト

| 名前 | 値 |
| ---- | ---- | 
| tenantId | 文字列<br />必須<br />**グローバル一意識別子**<br /><br />このアクセス ポリシーの **objectId** を含む Azure Active Directory テナントのテナント ID。 |
| objectId | 文字列<br />必須<br />**グローバル一意識別子**<br /><br />Key Vault にアクセスできるようになる Azure Active Directory ユーザーまたはサービス プリンシパルのオブジェクト ID。この値は、[Get-AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) または [Get-AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) PowerShell コマンドレットか、**azure ad user** または **azure ad sp** Azure CLI コマンドで取得できます。 |
| アクセス許可 | オブジェクト<br />必須<br />[permissions オブジェクト](#permissions)<br /><br />この Key Vault に付与される、Active Directory オブジェクトに対するアクセス許可。 |

<a id="permissions" />
### properties.accessPolicies.permissions オブジェクト

| 名前 | 値 |
| ---- | ---- | 
| キー | 配列<br />必須<br />**all**、**backup**、**create**、**decrypt**、**delete**、**encrypt**、**get**、**import**、**list**、**restore**、**sign**、**unwrapkey**、**update**、**verify**、**wrapkey**<br /><br />この Key Vault 内のキーに付与される、Active Directory オブジェクトに対するアクセス許可。この値は、1 つ以上の許可される値の配列として指定する必要があります。 |
| secrets | 配列<br />必須<br />**all**、**delete**、**get**、**list**、**set**<br /><br />この Key Vault 内のシークレットに付与される、Active Directory オブジェクトに対するアクセス許可。この値は、1 つ以上の許可される値の配列として指定する必要があります。 |

<a id="sku" />
### properties.sku オブジェクト

| 名前 | 値 |
| ---- | ---- | 
| name | 列挙型<br />必須<br />**standard**、または **premium** <br /><br />使用する KeyVault のサービス レベル。Standard は、シークレットとソフトウェアで保護されたキーをサポートしています。Premium は、さらに HSM で保護されたキーもサポートしています。 |
| family | 列挙型<br />必須<br />**A** <br /><br />使用する SKU ファミリ。 |
 
	
## 例

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

## クイック スタート テンプレート

Key Vault は、次のクイック スタート テンプレートによってデプロイされます。

- [Key Vault の作成](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)


## 次のステップ

- Key Vault の全般的な情報については、「[Azure Key Vault の概要](./key-vault/key-vault-get-started.md)」を参照してください。
- テンプレートをデプロイメントするときに Key Vault のシークレットを参照する例については、「[デプロイ時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md)」を参照してください。

<!---HONumber=AcomDC_0629_2016-->