<properties
   pageTitle="リソース ロックのリソース マネージャー テンプレート | Microsoft Azure"
   description="テンプレートを使ってリソース ロックをデプロイするためのリソース マネージャー スキーマを示します。"
   services="azure-resource-manager"
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
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# リソース ロック テンプレート スキーマ

リソースとその子リソースに対応する新しいロックを作成します。

## スキーマの形式

ロックを作成するには、テンプレートのリソース セクションに次のスキーマを追加します。
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## 値

次の表では、スキーマに設定する必要がある値について説明します。

| 名前 | 値 |
| ---- | ---- | 
| type | 列挙型<br />必須<br />**{namespace}/{type}/providers/locks** (リソースの場合) または <br />**Microsoft.Authorization/locks** (リソース グループの場合)<br /><br />作成するリソースの種類。 |
| apiVersion | 列挙型<br />必須<br />**2015-01-01**<br /><br />リソースの作成に使用する API バージョン。 |  
| name | 文字列<br />必須<br />**{resource}/Microsoft.Authorization/{lockname}** (リソースの場合) または <br />**{lockname}** (リソース グループの場合)<br />最大 64 文字。<、>、%、&、?、制御文字を含めることはできません。<br /><br />ロックするリソースとロックの名前の両方を指定する値。 |
| dependsOn | 配列<br />省略可能<br />リソース名またはリソースの一意識別子のコンマ区切りリスト。<br /><br />このロックが依存するリソースのコレクション。ロックするリソースを同じテンプレートでデプロイする場合は、そのリソースが最初にデプロイされるように、そのリソース名をこの要素に含めます。 | 
| properties | オブジェクト<br />必須<br />[プロパティ オブジェクト](#properties)<br /><br />ロックの種類を識別し、ロックに関するメモが記述されたオブジェクト。 |  

<a id="properties" />
### プロパティ オブジェクト

| 名前 | 値 |
| ------- | ---- |
| level | 列挙型<br />必須<br />**CannotDelete**<br /><br />スコープに適用するロックの種類。CanNotDelete を指定すると、変更はできますが、削除できなくなります。 |
| notes | 文字列<br />省略可能<br />最大 512 文字<br /><br />ロックの説明。 |


## ロック リソースの使用方法

リソースに対する指定したアクションを防ぐには、このリソースをテンプレートに追加します。ロックは、すべてのユーザーとグループに適用されます。通常は、プロセスの実行中に、組織内の他のユーザーが誤ってリソースを変更したり削除したりしないように、一定の期間のみロックを適用します。

管理ロックを作成または削除するには、**Microsoft.Authorization/*** または **Microsoft.Authorization/locks/*** アクションにアクセスできる必要があります。組み込みのロールのうち、**所有者**と**ユーザー アクセスの管理者**にのみこれらのアクションが許可されています。ロールベースのアクセス制御については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。

ロックは、指定されたリソースとすべての子リソースに適用されます。

ロックは PowerShell コマンド **Remove-AzureRmResourceLock** または REST API の [削除操作](https://msdn.microsoft.com/library/azure/mt204562.aspx)で削除することができます。

## 例

次の例では、Web アプリに削除不可のロックが適用されます。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
      			"type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

次の例では、リソース グループに削除不可のロックが適用されます。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## 次のステップ

- テンプレートの構造の詳細については、「[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
- ロックの詳細については、「[Azure リソース マネージャーによるリソースのロック](resource-group-lock-resources.md)」を参照してください。

<!---HONumber=AcomDC_0406_2016-->