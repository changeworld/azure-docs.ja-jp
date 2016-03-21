<properties
   pageTitle="ロール割り当て用のリソース マネージャー テンプレート | Microsoft Azure"
   description="テンプレートを使ってロールの割り当てをデプロイするためのリソース マネージャー スキーマを示します。"
   services="azure-resource-manager"
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
   ms.date="01/04/2016"
   ms.author="tomfitz"/>

# ロールの割り当てテンプレート スキーマ

ロールに指定したスコープで、ユーザー、グループ、またはサービス プリンシパルを割り当てます。

## スキーマの形式

ロールの割り当てを作成するには、テンプレートのリソース セクションに次のスキーマを追加します。
    
    {
        "type": "Microsoft.Authorization/roleAssignments",
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## 値

次の表では、スキーマに設定する必要がある値について説明します。

| 名前 | 型 | 必須 | 使用できる値 | 説明 |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | はい | **Microsoft.Authorization/roleAssignments** | 作成するリソースの種類。 |
| apiVersion | enum | はい | **2015-07-01** | リソースの作成に使用する API バージョン。 |  
| name | string | はい | グローバル一意識別子 | 新しいロールの割り当て用の識別子。 |
| dependsOn | array | いいえ | リソース名またはリソースの一意識別子のコンマ区切りリスト。 | このロールの割り当てが依存するリソースのコレクション。あるリソースに範囲指定されたロールを割り当てるとき、そのリソースが同じテンプレートにデプロイされている場合は、そのリソースが最初にデプロイされるように、この要素にそのリソース名を含めます。 | 
| properties | オブジェクト | はい | (下記参照) | ロール定義、プリンシパルおよびスコープを識別するオブジェクト。 |  

### プロパティ オブジェクト

| 名前 | 型 | 必須 | 使用できる値 | 説明 |
| ------- | ---- | ---------------- | -------- | ----------- |
| roleDefinitionId | string | はい | **/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** | ロールの割り当てで使用する既存のロール定義識別子。 |
| principalId | string | はい | グローバル一意識別子 | 既存のプリンシパルの識別子。これは、ディレクトリ内の ID にマップされ、ユーザー、サービス プリンシパルまたはセキュリティ グループを指すことができます。 |
| scope | string | はい | リソース グループの場合:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}**<br /><br />For resource:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** | このロールが割り当てられるスコープ。 |


## ロールの割り当てリソースを使用する方法

デプロイ時にロールに、ユーザー、グループ、またはサービス プリンシパルを追加する必要がある場合に、テンプレートにロール割り当てを追加します。ロール割り当ては上位レベルのスコープから継承されるので、サブスクリプション レベルで既にロールにプリンシパルを追加している場合は、そのリソース グループまたはリソースには再割り当てする必要はありません。

**name** の ID を新規に生成するには、次を使用します。

    PS C:\> $name = [System.Guid]::NewGuid().toString()

ロール定義用のグローバル一意識別子を取得するには、次を使用します。

    PS C:\> $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

プリンシパル用の ID を取得するには、次のいずれかのコマンドを使用します。

**Auditors** という名前のグループの場合:

    PS C:\> $principal = (Get-AzureRmADGroup -SearchString Auditors).id

**exampleperson** という名前のユーザーの場合:

    PS C:\> $principal = (Get-AzureRmADUser -SearchString exampleperson).id

**exampleapp** という名前のサービス プリンシパルの場合:

    PS C:\> $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 
 

## 例

次の例では、リソース グループ用のロールをグループに割り当てます。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }

## クイック スタート テンプレート

次のテンプレートでは、ロールの割り当てリソースを使用する方法を紹介しています。

- [組み込みロールをリソース グループに割り当てる](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-resourcegroup)
- [組み込みロールを既存の VM に割り当てる](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-virtualmachine)
- [既存の複数の VM に組み込みロールを割り当てる](https://github.com/Azure/azure-quickstart-templates/tree/master/201-rbac-builtinrole-multipleVMs)

## 次のステップ

- テンプレートの構造の詳細については、「[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
- ロールベースのアクセス制御の詳細については、「[Azure のロールベースのアクセス制御](active-directory/role-based-access-control-configure.md)」を参照してください。

<!---HONumber=AcomDC_0107_2016-->