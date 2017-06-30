---
title: "Azure マネージ アプリケーションの作成と発行 | Microsoft Docs"
description: "Azure マネージ アプリケーションを作成する方法について、ISV またはパートナーの視点で説明します。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/08/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 56dd68e328abd6c1dacdf7a8e051ca6b3cd07083
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017


---
# <a name="create-and-publish-an-azure-managed-application"></a>Azure マネージ アプリケーションの作成と発行 

[マネージ アプリケーションの概要](managed-application-overview.md)に関する記事で説明したように、マネージ アプリケーションには、その利用形態を全体として見た場合に 2 つのシナリオが存在します。 1 つは、顧客向けにマネージ アプリケーションを作成する発行元 (ISV) のシナリオです。 もう 1 つは、そのマネージ アプリケーションを利用する顧客 (コンシューマー) のシナリオです。 この記事では 1 つ目のシナリオに焦点を絞り、ISV としてマネージ アプリケーションを作成して発行する方法を説明します。 

マネージ アプリケーションを作成するには、以下を作成する必要があります。

* テンプレート ファイルを含んだパッケージ
* 顧客のサブスクリプション内のリソース グループにアクセスできるユーザー、グループ、アプリケーション
* アプライアンス定義

サンプル ファイルについては、[マネージ アプリケーションのサンプル](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)に関するページを参照してください。

## <a name="create-managed-application-package"></a>マネージ アプリケーション パッケージの作成

最初に行う作業は、主要なテンプレート ファイルを含んだマネージ アプリケーション パッケージの作成です。 発行元 (ISV) は、3 つのファイルを作成します。 

* 1 つ目のファイルは **applianceMainTemplate.json** です。 このテンプレート ファイルには、マネージ アプリケーションの構成要素としてプロビジョニングされる実際のリソースが定義されます。 たとえばマネージ アプリケーションを使ってストレージ アカウントを作成する場合、applianceMainTemplate.json には次の内容が記述されます。 

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "storageAccountName": {
              "type": "String"
          }
      },
      "resources": [{
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('storageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "westus",
          "sku": {
              "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {       
          }
      }],
      "outputs": {      
      }
  }
  ```

* 発行元が作成する必要のある 2 つ目のファイルは、**mainTemplate.json** です。 このテンプレート ファイルにはアプライアンス リソース (Microsoft.Solutions/appliances) だけが記述されます。 また、applianceMainTemplate.json 内のリソースに必要なパラメーターがすべて記述されます。 

  マネージ アプリケーションの作成中に入力として必要な重要なプロパティが 2 つ存在します。 **managedResourceGroupId** プロパティは、applianceMainTemplate.json に定義されているリソースの作成先となるリソース グループの ID です。 この ID は `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}` という形式になっています。

  **applianceDefinitionId** プロパティは、マネージ アプリケーション定義リソースの ID です。 この ID は `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Solutions/applianceDefinitions/{applianceDefinitionName}` という形式になっています。

  マネージ アプリケーションをコンシューマーが作成する際に指定できるよう、この 2 つの値にはパラメーターを指定します。 以下の例で、これらのプロパティに対応する 2 つのパラメーターは managedByResourceGroup と applianceDefinitonId です。

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "storageAccountName": {
              "type": "String"
          },
          "applianceDefinitionId": {
              "type": "String"
          },
          "managedByResourceGroup": {
              "type": "String"
          },
          "applianceName": {
              "type": "String"
          },
      },
      "variables": {            
      },
      "resources": [{
          "type": "Microsoft.Solutions/appliances",
          "name": "[parameters('applianceName')]",
          "apiVersion": "2016-09-01-preview",
          "location": "[resourceGroup().location]",
          "kind": "ServiceCatalog",
          "properties": {
              "ManagedResourceGroupId": "[parameters('managedByResourceGroup')]",
              "applianceDefinitionId": "[parameters('applianceDefinitionId')]",
              "Parameters": {
                  "storageAccountName": {
                      "value": "[parameters('storageAccountName')]"
                  }             
              }
          }
      }]
  }
  ```

* パッケージに必要な 3 つ目のファイルは **createUiDefinition.json** です。 コンシューマーがマネージ アプリケーションを作成するためのユーザー インターフェイスは、Azure Portal がこのファイルを使って生成します。 マネージ アプリケーションのパラメーターを定義すると共に、各パラメーターの入力内容をどのようにしてコンシューマーに伝えるかを定義する必要があります。 各種のオプション (ドロップダウン セレクターなど) やテキスト ボックス、パスワード ボックスなどの入力手段を利用することができます。 マネージ アプリケーションの UI 定義ファイルの作成については、「[CreateUiDefinition の基本概念](managed-application-createuidefinition-overview.md)」を参照してください。

必要なファイルがすべて整ったら、想定される実行場所からアクセスできる場所にパッケージをアップロードします。


## <a name="create-azure-ad-user-group-or-application"></a>Azure AD のユーザー グループまたはアプリケーションの作成
次に、顧客に代わってリソースを管理するときに使うユーザー グループまたはアプリケーションを作成します。 このユーザー グループまたはアプリケーションがマネージ リソース グループに対して持つアクセス許可は、ロールによって表されます。 そのロールには、組み込みの RBAC ロール (**所有者**、**共同作成者**など) をどれでも使用できます。 リソースを管理するためのアクセス許可は個々のユーザーに付与することもできますが、ユーザー グループに割り当てるのが一般的です。 新しい Active Directory ユーザー グループを作成するには、次のように入力します。

```azurecli
az ad group create --display-name "name" --mail-nickname "nickname"
```

既存のグループを使うこともできます。 新しく作成したユーザー グループまたは既存のユーザー グループのオブジェクト ID が必要となります。 グループの作成に使用された表示名からオブジェクト ID を取得する例を次に示します。

```azurecli
az ad group show --group "groupName"
```

例:

```azurecli
az ad group show --group ravAppliancetestADgroup
```

これを実行すると、次の出力結果が返されます。

```json 
{
    "displayName": "ravAppliancetestADgroup",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```
    
必要になるのは、上記の objectId の値です。 

## <a name="get-the-role-definition-id"></a>ロール定義 ID の取得

次に、ユーザー、ユーザー グループ、アプリケーションへのアクセス権を付与する RBAC の組み込みのロールのロール定義 ID が必要となります。 通常は、"所有者"、"共同作成者"、"閲覧者" のいずれかのロールを使います。 次のコマンドは、"所有者" ロールのロール定義 ID を取得する方法を示しています。

```azurecli
az role definition list --name owner
```

これを実行すると、次の出力結果が返されます。

```json
{
    "id": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "properties": {
      "assignableScopes": [
    "/"
      ],
      "description": "Lets you manage everything, including access to resources.",
      "permissions": [
    {
      "actions": [
    "*"
      ],
      "notActions": []
    }
      ],
      "roleName": "Owner",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
}
```

必要になるのは、上の例の "name" プロパティの値です。


## <a name="create-the-managed-application-definition"></a>マネージ アプリケーション定義の作成

最後の作業は、マネージ アプリケーション定義リソースの作成です。 アプライアンス パッケージと承認を作成したら、次のコマンドを使ってアプライアンス定義を作成します。 

```azurecli
az managedapp definition create -n ravtestAppDef4 -l "westcentralus" 
    --resource-group ravApplianceDefRG3 --lock-level ReadOnly 
    --display-name ravtestappdef --description ravtestdescription  
    --authorizations "9aabd3ad-3716-4242-9d8e-a85df479d5d9:8e3af657-a8ff-443c-a75c-2fe8c4bcb635" 
    --package-file-uri "{path to package}" --debug 
```

この例で使われているパラメーターは次のとおりです。

- resource-group - アプライアンス定義の作成先となるリソース グループの名前。
- lock-level - マネージ リソース グループに対して設定されるロックの種類。 これによって、このリソース グループに対して問題となるような操作を顧客が実行できないようにします。 現在サポートされているロック レベルは **ReadOnly** だけです。 ReadOnly が指定されている場合、マネージ リソース グループに存在するリソースの読み取りしか顧客は実行できません。
- authorizations - マネージ リソース グループへのアクセス許可を付与する際に使うプリンシパル ID とロール定義 ID を記述します。 `<principalId>:<roleDefinitionId>` の形式で指定します。 このプロパティには複数の値を指定することができます。 複数の値が必要である場合は、`<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` という形式で指定してください。 このとき値は、スペースで区切って指定します。
- package-file-uri - テンプレート ファイルを含んだアプライアンス パッケージの場所。これを Azure Storage Blob にすることができます。 


## <a name="next-steps"></a>次のステップ

* マネージ アプリケーションの概要については、「[Azure マネージ アプリケーションの概要](managed-application-overview.md)」を参照してください。
* サンプル ファイルについては、[マネージ アプリケーションのサンプル](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)に関するページを参照してください。
* コンシューマーから見た利用形態については、「[Azure マネージ アプリケーションの実行](managed-application-consumption.md)」を参照してください。
* マネージ アプリケーションの UI 定義ファイルの作成については、「[CreateUiDefinition の基本概念](managed-application-createuidefinition-overview.md)」を参照してください。

