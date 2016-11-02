<properties
   pageTitle="ロール割り当て用のリソース マネージャー テンプレート | Microsoft Azure"
   description="テンプレートを使ってロールの割り当てをデプロイするためのリソース マネージャー スキーマを示します。"
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
   ms.date="10/03/2016"
   ms.author="tomfitz"/>


# <a name="role-assignments-template-schema"></a>ロールの割り当てテンプレート スキーマ

ロールに指定したスコープで、ユーザー、グループ、またはサービス プリンシパルを割り当てます。

## <a name="resource-format"></a>リソース形式

ロールの割り当てを作成するには、テンプレートのリソース セクションに次のスキーマを追加します。
    
    {
        "type": string,
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

## <a name="values"></a>値

次の表では、スキーマに設定する必要がある値について説明します。

| 名前 | 必須 | 説明 |
| ---- | -------- | ----------- |
| type | はい    | 作成するリソースの種類。<br /><br /> リソース グループの場合:<br />**Microsoft.Authorization/roleAssignments**<br /><br />リソースの場合:<br />**{provider-namespace}/{resource-type}/providers/roleAssignments** |
| apiVersion |はい | リソースの作成に使用する API バージョン。<br /><br /> **2015-07-01** を使用します。 | 
| name | はい | 新しいロールの割り当て用のグローバル一意識別子。 |
| dependsOn | いいえ | リソース名またはリソースの一意識別子のコンマ区切りの配列。<br /><br />このロールの割り当てが依存するリソースのコレクション。 あるリソースに範囲指定されたロールを割り当てるとき、そのリソースが同じテンプレートにデプロイされている場合は、そのリソースが最初にデプロイされるように、この要素にそのリソース名を含めます。 |
| プロパティ | はい | ロール定義、プリンシパルおよびスコープを識別するプロパティ オブジェクト。 |

### <a name="properties-object"></a>プロパティ オブジェクト

| 名前 | 必須 | 説明 |
| ---- | -------- | ----------- |
| roleDefinitionId | はい |  ロールの割り当てで使用する既存のロール定義識別子。<br /><br /> 次の形式を使用します。<br /> **/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** |
| principalId | はい | 既存のプリンシパルのグローバル一意識別子。 この値は、ディレクトリ内の ID にマップされ、ユーザー、サービス プリンシパルまたはセキュリティ グループを指すことができます。 |
| scope | いいえ | このロールが割り当てられるスコープ。<br /><br />リソース グループには、次を使用します。<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}**  <br /><br />リソースには、次を使用します。<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** |  |


## <a name="how-to-use-the-role-assignment-resource"></a>ロールの割り当てリソースを使用する方法

デプロイ時にロールに、ユーザー、グループ、またはサービス プリンシパルを追加する必要がある場合に、テンプレートにロール割り当てを追加します。 ロール割り当ては上位レベルのスコープから継承されるので、サブスクリプション レベルで既にロールにプリンシパルを追加している場合は、そのリソース グループまたはリソースには再割り当てする必要はありません。

ロール割り当ての作業を行う場合、指定する必要がある多くの識別子値があります。 PowerShell または Azure CLI から値を取得することができます。

### <a name="powershell"></a>PowerShell

ロール割り当ての名前には、グローバル一意識別子が必要です。 **name** の ID を新規に生成するには、次を使用します。

    $name = [System.Guid]::NewGuid().toString()

ロール定義用の識別子を取得するには、次を使用します。

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

プリンシパル用の ID を取得するには、次のいずれかのコマンドを使用します。

**Auditors**という名前のグループの場合:

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

**exampleperson**という名前のユーザーの場合:

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

**exampleapp**という名前のサービス プリンシパルの場合:

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>Azure CLI

ロール定義用の識別子を取得するには、次を使用します。

    azure role show Reader --json | jq .[].Id -r

プリンシパル用の ID を取得するには、次のいずれかのコマンドを使用します。

**Auditors**という名前のグループの場合:

    azure ad group show --search Auditors --json | jq .[].objectId -r

**exampleperson**という名前のユーザーの場合:

    azure ad user show --search exampleperson --json | jq .[].objectId -r

**exampleapp**という名前のサービス プリンシパルの場合:

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>例

次のテンプレートでは、ロールの識別子とユーザー、グループ、またはサービス プリンシパルの識別子を受け取ります。 これは、リソース グループ レベルでロールを割り当てます。

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



次のテンプレートでは、ストレージ アカウントを作成し、ストレージ アカウントにリーダー ロールを割り当てます。 2 つのグループおよびリーダー ロールの識別子には、デプロイを簡易化するためのテンプレートが含まれています。 これらの値は、スクリプトでのデプロイ時に取得して、パラメーターとして渡すことができます。

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>クイック スタート テンプレート

次のテンプレートでは、ロールの割り当てリソースを使用する方法を紹介しています。

- [組み込みロールをリソース グループに割り当てる](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [組み込みロールを既存の VM に割り当てる](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [既存の複数の VM に組み込みロールを割り当てる](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>次のステップ

- テンプレートの構造の詳細については、「 [Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
- ロールベースのアクセス制御の詳細については、「[Azure Active Directory のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」をご覧ください。



<!--HONumber=Oct16_HO2-->


