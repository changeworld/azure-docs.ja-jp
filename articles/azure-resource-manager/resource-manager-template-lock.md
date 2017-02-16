---
title: "リソース ロックの Resource Manager テンプレート | Microsoft Docs"
description: "テンプレートを使ってリソース ロックをデプロイするためのリソース マネージャー スキーマを示します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: de6c0c57-e33a-4960-98e0-900901592003
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 16f87b8859a5d98c2328388ef12b13ef0e261861


---
# <a name="resource-locks-template-schema"></a>リソース ロック テンプレート スキーマ
リソースとその子リソースに対応するロックを作成します。

## <a name="schema-format"></a>スキーマの形式
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



## <a name="values"></a>値
次の表では、スキーマに設定する必要がある値について説明します。

| 名前 | 必須 | 説明 |
| --- | --- | --- |
| type |はい |作成するリソースの種類。<br /><br />リソースの場合:<br />**{namespace}/{type}/providers/locks**<br /><br/>リソース グループの場合:<br />**Microsoft.Authorization/locks** |
| apiVersion |はい |リソースの作成に使用する API バージョン。<br /><br />次のコマンドを使用します。<br />**2015-01-01**<br /><br /> |
| name |はい |ロックするリソースと、ロックの名前の両方を指定する値。 最大 64 文字。<、> %、&、?、または制御文字を含めることはできません。<br /><br />リソースの場合:<br />**{resource}/Microsoft.Authorization/{lockname}**<br /><br />リソース グループの場合:<br />**{lockname}** |
| dependsOn |なし |リソース名またはリソースの一意識別子のコンマ区切りリスト。<br /><br />このロックが依存するリソースのコレクション。 ロックするリソースを同じテンプレートでデプロイする場合は、そのリソースが最初にデプロイされるように、そのリソース名をこの要素に含めます。 |
| プロパティ |あり |ロックの種類を識別し、ロックに関するメモが記述されたオブジェクト。<br /><br />「[プロパティ オブジェクト](#properties-object)」を参照してください。 |

### <a name="properties-object"></a>プロパティ オブジェクト
| 名前 | 必須 | Description |
| --- | --- | --- |
| level |あり |スコープに適用するロックの種類。<br /><br />**CannotDelete** - ユーザーはリソースを変更できますが、削除することはできません。<br />**ReadOnly** - 正規ユーザーはリソースの読み取りを実行できますが、リソースの削除やリソースにアクションを実行することはできません。 |
| notes |なし |ロックの説明。 最大長は 512 文字です。 |

## <a name="how-to-use-the-lock-resource"></a>ロック リソースの使用方法
リソースに対する指定したアクションを防ぐには、このリソースをテンプレートに追加します。 ロックは、すべてのユーザーとグループに適用されます。

管理ロックを作成または削除するには、**Microsoft.Authorization/*** または **Microsoft.Authorization/locks/*** アクションにアクセスできる必要があります。組み込みのロールのうち、**所有者**と**ユーザー アクセスの管理者**にのみこれらのアクションが許可されています。 ロールベースのアクセス制御については、「[Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)」を参照してください。

ロックは、指定されたリソースとすべての子リソースに適用されます。

ロックは PowerShell コマンド **Remove-AzureRmResourceLock** または REST API の [削除操作](https://msdn.microsoft.com/library/azure/mt204562.aspx) で削除することができます。

## <a name="examples"></a>例
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

## <a name="next-steps"></a>次のステップ
* テンプレートの構造の詳細については、「 [Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
* ロックの詳細については、「 [Azure リソース マネージャーによるリソースのロック](resource-group-lock-resources.md)」を参照してください。




<!--HONumber=Nov16_HO3-->


