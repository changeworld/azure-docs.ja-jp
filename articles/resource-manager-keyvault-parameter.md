---
title: Resource Manager テンプレートでの Key Vault シークレット | Microsoft Docs
description: デプロイメント時にパラメーターとして Key Vault からシークレットを渡す方法について説明します。
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: tomfitz

---
# デプロイメント時にセキュリティで保護された値を渡す
デプロイメント時にパラメーターとしてセキュリティで保護された値 (パスワードなど) を渡す必要がある場合、[Azure Key Vault](key-vault/key-vault-whatis.md) にシークレットとしてその値を格納し、他のリソース マネージャー テンプレートで値を参照することができます。テンプレートにはシークレットの参照のみを含めるので、シークレットが明らかになることはありません。また、リソースをデプロイするたびにシークレットの値を手動で入力する必要はありません。管理者は、シークレットにアクセスできるユーザーまたはサービス プリンシパルを指定します。

## Key Vault とシークレットのデプロイ
他のリソース マネージャー テンプレートから参照できる Key Vault を作成するには、**enabledForTemplateDeployment** property を **true** に設定する必要があります。また、ユーザーまたはサービス プリンシパルに対して、シークレットを参照するデプロイメントを実行するアクセス権を付与する必要があります。

Key Vault とシークレットのデプロイについては、[Key Vault のスキーマ](resource-manager-template-keyvault.md)と [Key Vault シークレットのスキーマ](resource-manager-template-keyvault-secret.md)に関するページを参照してください。

## 固定 ID でのシークレットの参照
値をテンプレートに渡すパラメーター ファイル内から、シークレットを参照します。シークレットを参照するには、Key Vault のリソース識別子とシークレットの名前を渡します。この例では、Key Vault シークレットがあらかじめ存在しているという前提で、リソース ID には固定値を使用しています。

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

パラメーター ファイルの全体は次のような内容です。

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

この例で、シークレットを受け取るパラメーターは **securestring** です。次に、管理者のパスワードが必要な、SQL Server をデプロイするテンプレートの関連するセクションを示します。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }

## 動的 ID でのシークレットの参照
前のセクションでは、Key Vault シークレットの固定リソース ID を渡す方法を紹介しました。しかし参照すべき Key Vault シークレットがデプロイごとに変わる状況も考えられます。そのようなケースでは、パラメーター ファイルでリソース ID をハードコーディングすることはできません。あいにくパラメーター ファイルではテンプレート式が使用できないので、パラメーター ファイルでリソース ID を動的に生成することはできません。

Key Vault シークレットのリソース ID を動的に生成するには、そのシークレットを必要とするリソースを、入れ子になったテンプレートに移す必要があります。マスター テンプレートに、その入れ子になったテンプレートを追加し、動的に生成されたリソース ID をパラメーターに格納して渡します。

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "vaultName": {
          "type": "string"
        },
        "secretName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "nestedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "adminPassword": {
                "reference": {
                  "keyVault": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
                  },
                  "secretName": "[parameters('secretName')]"
                }
              }
            }
          }
        }
      ],
      "outputs": {}
    }


## 次のステップ
* Key Vault の全般的な情報については、「[Azure Key Vault の概要](key-vault/key-vault-get-started.md)」を参照してください。
* 仮想マシンで Key Vault を使用する方法については、「[Azure Resource Manager のセキュリティに関する考慮事項](best-practices-resource-manager-security.md)」をご覧ください。
* キー シークレットの詳細な参照例については、[Key Vault の例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)を参照してください。

<!---HONumber=AcomDC_0629_2016-->