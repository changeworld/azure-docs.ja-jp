---
title: "Azure サービス カタログ マネージ アプリケーションの作成と発行 | Microsoft Docs"
description: "組織のメンバーを対象とする Azure マネージ アプリケーションを作成する方法について説明します。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/09/2017
ms.author: gauravbh
ms.openlocfilehash: 3ff2108d08bacc4bc5f79a768b9c131aa7e6fceb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>社内従量課金プラン向けマネージ アプリケーションの発行

組織のメンバーを対象とする Azure [マネージ アプリケーション](managed-application-overview.md)を作成し、発行することができます。 たとえば、IT 部門が、組織標準に確実に準拠するマネージ アプリケーションを発行できます。 こうしたマネージ アプリケーションは、Azure Marketplace ではなく、サービス カタログを通して入手できます。

サービス カタログ用のマネージ アプリケーションを発行するには、次の操作を行う必要があります。

* 2 つの必要なテンプレート ファイルを含む .zip パッケージを作成する。
* どのユーザー、グループ、またはアプリケーションがユーザーのサブスクリプションのリソース グループにアクセスする必要があるかを決める。
* .zip パッケージを指定して ID アクセスを要求するマネージ アプリケーション定義を作成する。

## <a name="create-a-managed-application-package"></a>マネージ アプリケーション パッケージの作成

まず、2 つの必要なテンプレート ファイルを作成します。 この 2 つのファイルすべてを .zip ファイルにパッケージし、ストレージ アカウントなど、アクセス可能な場所にアップロードします。 この .zip ファイルへのリンクを、マネージ アプリケーション定義を作成しているときに渡します。

* **mainTemplate.json**: このファイルには、マネージ アプリケーションの構成要素としてプロビジョニングされる Azure リソースが定義されます。 テンプレートは、通常の Resource Manager テンプレートと違いはありません。 たとえば、マネージ アプリケーションでストレージ アカウントを作成するために、mainTemplate.json には次の内容が記述されます。

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]",
            "apiVersion": "2016-01-01",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {}
  }
  ```

* **createUiDefinition.json**: Azure Portal では、このファイルを使用して、マネージ アプリケーションを作成するユーザー用のユーザー インターフェイスを生成します。 ユーザーが各パラメーターの入力をどのように提供するかを定義します。 各種のオプション (ドロップダウン リストなど) やテキスト ボックス、パスワード ボックスなどの入力手段を利用することができます。 マネージ アプリケーションの UI 定義ファイルの作成する方法については、「[CreateUiDefinition の基本概念](managed-application-createuidefinition-overview.md)」を参照してください。

  次に createUiDefinition.json ファイルの例を示します。このファイルにより、ユーザーがテキスト ボックスでストレージ アカウント名のプレフィックスを指定できます。

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {
                "name": "storageAccounts",
                "type": "Microsoft.Common.TextBox",
                "label": "Storage account name prefix",
                "defaultValue": "storage",
                "toolTip": "Provide a value that is used for the prefix of your storage account. Limit to 11 characters.",
                "constraints": {
                    "required": true,
                    "regex": "^[a-z0-9A-Z]{1,11}$",
                    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-11 characters long."
                },
                "visible": true
            }
        ],
        "steps": [],
        "outputs": {
            "storageAccountNamePrefix": "[basics('storageAccounts')]"
        }
    }
  }
  ```

必要なファイルがすべて整ったら、.zip ファイルとしてパッケージ化します。 2 つのファイルは .zip ファイルのルートに配置する必要があります。 このファイルをフォルダーに配置すると、マネージ アプリケーション定義を作成するときに、必要なファイルが存在しないことを示すエラーが発生します。 パッケージは、想定される実行場所からアクセスできる場所にアップロードしてください。 以降の説明では、パブリックにアクセスできるストレージの BLOB コンテナーに .zip ファイルが存在することを前提としています。

サービス カタログのマネージ アプリケーションの作成には、Azure CLI またはポータルを使うことができます。 この記事では、両方の方法を紹介します。

## <a name="create-managed-application-with-azure-cli"></a>Azure CLI を使用してマネージ アプリケーションを作成する

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Azure Active Directory ユーザー グループまたはアプリケーションの作成

次に、顧客に代わってリソースを管理するためのユーザー グループまたはアプリケーションを選択します。 このユーザー グループまたはアプリケーションには、割り当てられているロールに従って、マネージ リソース グループに対するアクセス許可が付与されています。 そのロールには、ロールベースのアクセス制御 (RBAC) の組み込みロール (所有者、共同作成者など) をどれでも使用できます。 リソースを管理するためのアクセス許可は、個々のユーザーに付与することもできますが、ユーザー グループに割り当てるのが一般的です。 新しい Active Directory ユーザー グループの作成する場合は、「[Azure Active Directory でグループを作成し、メンバーを追加する](../active-directory/active-directory-groups-create-azure-portal.md)」を参照してください。

リソースの管理に使用するユーザー グループのオブジェクト ID が必要です。 次の例は、グループの表示名からオブジェクト ID を取得する方法を示しています。

```azurecli-interactive
az ad group show --group exampleGroupName
```

この例のコマンドを実行すると、次の出力が返されます。

```azurecli
{
    "displayName": "exampleGroupName",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```

オブジェクト ID のみを取得するには、次を使用します。

```azurecli-interactive
groupid=$(az ad group show --group exampleGroupName --query objectId --output tsv)
```

### <a name="get-the-role-definition-id"></a>ロール定義 ID の取得

次に、ユーザー、ユーザー グループ、アプリケーションへのアクセス権を付与する RBAC の組み込みのロールのロール定義 ID が必要となります。 通常、所有者、共同作成者、閲覧者のいずれかのロールを使います。 次のコマンドは、所有者ロールのロール定義 ID を取得する方法を示しています。

```azurecli-interactive
az role definition list --name owner
```

このコマンドを実行すると、次の出力が返されます。

```azurecli
{
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
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

必要になるのは、上の例の "name" プロパティの値です。 プロパティのみを取得するには、次を使用します。

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

### <a name="create-the-managed-application-definition"></a>マネージ アプリケーション定義の作成

マネージ アプリケーション定義を保存するリソース グループがまだない場合は、ここで作成します。

```azurecli-interactive
az group create --name managedApplicationGroup --location westcentralus
```

次に、マネージ アプリケーション定義のリソースを作成します。

```azurecli-interactive
az managedapp definition create \
  --name storageApp \
  --location "westcentralus" \
  --resource-group managedApplicationGroup \
  --lock-level ReadOnly \
  --display-name myteststorageapp \
  --description storageapp \
  --authorizations "$groupid:$roleid" \
  --package-file-uri <uri-path-to-zip-file>
```

この例で使われているパラメーターは次のとおりです。

* **resource-group**: マネージ アプリケーション定義が作成されるリソース グループの名前。
* **lock-level**: マネージ リソース グループに対して設定されるロックの種類。 これによって、このリソース グループに対して問題となるような操作を顧客が実行できないようにします。 現在サポートされているロック レベルは ReadOnly だけです。 ReadOnly が指定されている場合、マネージ リソース グループに存在するリソースの読み取りしか顧客は実行できません。
* **authorizations**: マネージ リソース グループへのアクセス許可を付与する際に使うプリンシパル ID とロール定義 ID を記述します。 `<principalId>:<roleDefinitionId>` の形式で指定します。 このプロパティには複数の値を指定することができます。 複数の値が必要である場合は、`<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` という形式で指定してください。 このとき値は、スペースで区切って指定します。
* **package-file-uri**: テンプレート ファイルを含むマネージ アプリケーション定義パッケージの場所。これを Azure Storage Blob にできます。

## <a name="create-managed-application-with-portal"></a>ポータルを使用してマネージ アプリケーションを作成する

1. 必要に応じて、リソースを管理する Azure Active Directory ユーザー グループを作成します。 詳細については、「[Azure Active Directory でグループを作成し、メンバーを追加する](../active-directory/active-directory-groups-create-azure-portal.md)」を参照してください。
1. 左上にある **[+ 新規]** を選択します。

   ![新しいサービス](./media/managed-application-publishing/new.png)

1. **サービス カタログ**を検索します。

1. 結果から、**[Service Catalog Managed Application Definition]\(サービス カタログのマネージ アプリケーション定義\)** までスクロールします。 それを選択します。

   ![マネージ アプリケーション定義を検索する](./media/managed-application-publishing/select-managed-apps-definition.png)

1. **[Create]\(作成\)** を選択し、マネージ アプリケーション定義を作成するプロセスを開始します。

   ![マネージ アプリケーション定義を作成する](./media/managed-application-publishing/create-definition.png)

1. 名前、表示名、説明、場所、サブスクリプション、リソース グループの値を指定します。 パッケージ ファイルの URI については、作成した zip ファイルのパスを指定します。

   ![値を指定する](./media/managed-application-publishing/fill-application-values.png)

1. [Authentication and Lock Level]\(認証とロックのレベル\) セクションで、**[Add Authorization]\(承認の追加\)** を選択します。

   ![承認を追加する](./media/managed-application-publishing/add-authorization.png)

1. リソースを管理する Azure Active Directory グループを選択し、**[OK]** を選択します。

   ![承認グループを追加する](./media/managed-application-publishing/add-auth-group.png)

1. すべての値を指定したら、**[Create]\(作成\)** を選択します。

   ![マネージ アプリケーションを作成する](./media/managed-application-publishing/create-app.png)

## <a name="next-steps"></a>次のステップ

* マネージ アプリケーションの概要については、[マネージ アプリケーションの概要](managed-application-overview.md)に関するページをご覧ください。
* サンプル ファイルについては、[マネージ アプリケーションのサンプル](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)に関するページを参照してください。
* サービス カタログ マネージ アプリケーションの使用については、「[サービス カタログ マネージ アプリケーションの使用](managed-application-consumption.md)」を参照してください。
* マネージ アプリケーションを Azure Marketplace に発行する方法については、「[Marketplace の Azure マネージ アプリケーション](managed-application-author-marketplace.md)」を参照してください。
* Marketplace からマネージ アプリケーションを使用する方法については、「[Consume Azure managed applications in the Marketplace (Marketplace での Azure マネージ アプリケーションの使用)](managed-application-consume-marketplace.md)」を参照してください。
* マネージ アプリケーションの UI 定義ファイルの作成する方法については、「[CreateUiDefinition の基本概念](managed-application-createuidefinition-overview.md)」を参照してください。
