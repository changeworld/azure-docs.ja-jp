---
title: "Azure マネージ アプリケーションの作成と発行 | Microsoft Docs"
description: "Azure マネージ アプリケーションを作成する方法について、ISV またはパートナーの視点で説明します"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: e7a4fb831f4712a04acd814a15d661a7137006ad
ms.contentlocale: ja-jp
ms.lasthandoff: 08/11/2017

---
# <a name="create-and-publish-a-service-catalog-managed-application"></a>サービス カタログ マネージ アプリケーションの作成と発行

Azure マネージ アプリケーションには、その利用形態を全体として見た場合に 2 つのシナリオが存在します。 1 つは、顧客向けにマネージ アプリケーションを作成する発行元 (ISV) のシナリオです。 もう 1 つは、顧客 (そのマネージ アプリケーションを利用するコンシューマー) のシナリオです。 この記事では 1 つ目のシナリオに焦点を絞り、ISV としてマネージ アプリケーションを作成して発行する方法を説明します。 詳細については、[マネージ アプリケーションの概要](managed-application-overview.md)に関するページをご覧ください。

マネージ アプリケーションを作成するには、以下を作成する必要があります。

* テンプレート ファイルを含むパッケージ。
* 顧客のサブスクリプション内のリソース グループにアクセスできるユーザー、グループ、またはアプリケーション。
* アプライアンスの定義。

サンプル ファイルについては、[マネージ アプリケーションのサンプル](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)に関するページを参照してください。

## <a name="create-a-managed-application-package"></a>マネージ アプリケーション パッケージの作成

最初に行う作業は、主要なテンプレート ファイルを含んだマネージ アプリケーション パッケージの作成です。 発行元 (ISV) は、3 つのファイルを作成します。 3 つのファイルをすべて .zip ファイルにパッケージして、アクセス可能な場所にアップロードします。

* **applianceMainTemplate.json**: この最初のテンプレート ファイルには、マネージ アプリケーションの構成要素としてプロビジョニングされる実際のリソースが定義されます。 たとえばマネージ アプリケーションを使ってストレージ アカウントを作成する場合、applianceMainTemplate.json には次の内容が記述されます。 

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

* **mainTemplate.json**: この 2 番目ファイルにはアプライアンス リソース (Microsoft.Solutions/appliances) だけが記述されます。 また、applianceMainTemplate.json 内のリソースに必要なパラメーターがすべて記述されます。 

  マネージ アプリケーションの作成中に入力として必要な重要なプロパティが 2 つ存在します。

  * **managedResourceGroupId**: このプロパティは、applianceMainTemplate.json に定義されているリソースの作成先となるリソース グループの ID です。 この ID は `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}` という形式になっています。
  * **applianceDefinitionId**: このプロパティは、マネージ アプリケーション定義リソースの ID です。 この ID は `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Solutions/applianceDefinitions/{applianceDefinitionName}` という形式になっています。

  マネージ アプリケーションの作成時にコンシューマーが指定できるよう、この 2 つの値にはパラメーターを指定します。 次の例に使用すように、こうしたプロパティに対応する 2 つのパラメーターは managedByResourceGroup と applianceDefinitonId です。

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

* **createUiDefinition.json**: このテンプレートはパッケージに必要な 3 つ目のファイルです。 マネージ アプリケーションを作成するためのユーザー インターフェイスは、Azure Portal がこのファイルを使って生成します。 マネージ アプリケーションのパラメーターを定義すると共に、各パラメーターの入力内容をどのようにしてコンシューマーに伝えるかを定義する必要があります。 各種のオプション (ドロップダウン リストなど) やテキスト ボックス、パスワード ボックスなどの入力手段を利用することができます。 マネージ アプリケーションの UI 定義ファイルの作成する方法については、「[CreateUiDefinition の基本概念](managed-application-createuidefinition-overview.md)」を参照してください。

必要なファイルがすべて整ったら、想定される実行場所からアクセスできる場所にパッケージをアップロードします。


## <a name="create-an-azure-active-directory-user-group-or-application"></a>Azure Active Directory ユーザー グループまたはアプリケーションの作成
次に、ユーザーに代わってリソースを管理するときに使うユーザー グループまたはアプリケーションを作成します。 このユーザー グループまたはアプリケーションがマネージ リソース グループに対して持つアクセス許可は、ロールによって表されます。 そのロールには、ロールベースのアクセス制御 (RBAC) の組み込みロール (所有者、共同作成者など) をどれでも使用できます。 リソースを管理するためのアクセス許可は、個々のユーザーに付与することもできますが、ユーザー グループに割り当てるのが一般的です。 新しい Active Directory ユーザー グループを作成するには、次を使用します。

```azurecli
az ad group create --display-name "name" --mail-nickname "nickname"
```

既存のグループを使うこともできます。 新しく作成したユーザー グループまたは既存のユーザー グループのオブジェクト ID が必要となります。 次の例は、グループの作成に使用された表示名からオブジェクト ID を取得する方法を示しています。

```azurecli
az ad group show --group "groupName"
```

例:

```azurecli
az ad group show --group ravAppliancetestADgroup
```

この例のコマンドを実行すると、次の出力が返されます。

```json 
{
    "displayName": "ravAppliancetestADgroup",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```
    
必要になるのは、上記の例の objectId の値です。 

## <a name="get-the-role-definition-id"></a>ロール定義 ID の取得

次に、ユーザー、ユーザー グループ、アプリケーションへのアクセス権を付与する RBAC の組み込みのロールのロール定義 ID が必要となります。 通常、所有者、共同作成者、閲覧者のいずれかのロールを使います。 次のコマンドは、所有者ロールのロール定義 ID を取得する方法を示しています。

```azurecli
az role definition list --name Owner
```

このコマンドを実行すると、次の出力が返されます。

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

最後に、マネージ アプリケーション定義リソースを作成します。 アプライアンス パッケージと承認を作成したら、次のコマンドを使ってアプライアンス定義を作成します。 

```azurecli
az managedapp definition create -n ravtestAppDef4 -l "westcentralus" 
    --resource-group ravApplianceDefRG3 --lock-level ReadOnly 
    --display-name ravtestappdef --description ravtestdescription  
    --authorizations "9aabd3ad-3716-4242-9d8e-a85df479d5d9:8e3af657-a8ff-443c-a75c-2fe8c4bcb635" 
    --package-file-uri "{path to package}" --debug 
```

この例で使われているパラメーターは次のとおりです。

- **resource-group**: アプライアンス定義の作成先となるリソース グループの名前。
- **lock-level**: マネージ リソース グループに対して設定されるロックの種類。 これによって、このリソース グループに対して問題となるような操作を顧客が実行できないようにします。 現在サポートされているロック レベルは ReadOnly だけです。 ReadOnly が指定されている場合、マネージ リソース グループに存在するリソースの読み取りしか顧客は実行できません。
- **authorizations**: マネージ リソース グループへのアクセス許可を付与する際に使うプリンシパル ID とロール定義 ID を記述します。 `<principalId>:<roleDefinitionId>` の形式で指定します。 このプロパティには複数の値を指定することができます。 複数の値が必要である場合は、`<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` という形式で指定してください。 このとき値は、スペースで区切って指定します。
- **package-file-uri**: テンプレート ファイルを含んだアプライアンス パッケージの場所。これを Azure Storage Blob にすることができます。 


## <a name="next-steps"></a>次のステップ

* マネージ アプリケーションの概要については、[マネージ アプリケーションの概要](managed-application-overview.md)に関するページをご覧ください。
* サンプル ファイルについては、[マネージ アプリケーションのサンプル](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)に関するページを参照してください。
* サービス カタログ マネージ アプリケーションの使用については、「[サービス カタログ マネージ アプリケーションの使用](managed-application-consumption.md)」を参照してください。
* マネージ アプリケーションを Azure Marketplace に発行する方法については、「[Marketplace の Azure マネージ アプリケーション](managed-application-author-marketplace.md)」を参照してください。
* Marketplace からマネージ アプリケーションを使用する方法については、「[Consume Azure managed applications in the Marketplace (Marketplace での Azure マネージ アプリケーションの使用)](managed-application-consume-marketplace.md)」を参照してください。
* マネージ アプリケーションの UI 定義ファイルの作成する方法については、「[CreateUiDefinition の基本概念](managed-application-createuidefinition-overview.md)」を参照してください。

