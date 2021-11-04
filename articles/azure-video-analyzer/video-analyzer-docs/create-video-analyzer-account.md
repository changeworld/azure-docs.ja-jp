---
title: Azure Video Analyzer アカウントを作成する
description: このトピックでは、Azure Video Analyzer のアカウントを作成する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ae759d67273461dff2c4fb8b37de222603bdcb7d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563988"
---
# <a name="create-a-video-analyzer-account"></a>Video Analyzer アカウントを作成する

Azure Video Analyzer の使用を開始するには、Video Analyzer アカウントを作成する必要があります。 アカウントは、ストレージ アカウントと少なくとも 1 つの[ユーザー割り当てマネージド ID][docs-uami] (UAMI) に関連付けられている必要があります。 UAMI には、ストレージ アカウントに対する[ストレージ BLOB データ共同作成者][docs-storage-access]ロールと、[閲覧者][docs-role-reader]ロールの権限が必要です。 必要に応じて、IoT Hub を Video Analyzer アカウントに関連付けできます。これは、Video Analyzer エッジ モジュールを[透過ゲートウェイ](./cloud/use-remote-device-adapter.md)として使用する場合に必要です。 その場合は、[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロール権限を持つ UAMI を追加する必要があります。 ストレージ アカウントと IoT Hub の両方に同じ UAMI を使用することも、別々の UAMI を使用することもできます。

この記事では、新しい Video Analyzer アカウントを作成する手順について説明します。 Azure portal や[Azure Resource Manager (ARM) テンプレート][docs-arm-template]を利用できます。 使用するメソッドのタブを選択します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[ポータル](#tab/portal/)

[!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Azure Portal で Video Analyzer アカウントを作成する

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. 上部にある検索バーを使用して、「**Video Analyzer**」と入力します。
1. [*Services*]\ (サービス\) の [*Video Analyzers*]\ (ビデオアナライザー\) をクリックします。
1. **Create** をクリックしてください。
1. **[Create Video Analyzer account]** \(Video Analyzer アカウントを作成する\) セクションに必要な値を入力します。

    | Name | 説明 |
    | ---|---|
    |**サブスクリプション**|複数のサブスクリプションがある場合は、アクセス権のある Azure サブスクリプションの一覧から 1 つを選択します。|
    |**リソース グループ**|既存のリソース を選択するか、新しいリソースを作成します。 リソース グループとは、ライフサイクル、アクセス許可、ポリシーを共有するリソースの集まりです。 [こちら](../../azure-resource-manager/management/overview.md#resource-groups)をご覧ください。|
    |**Video Analyzer アカウント名**|Video Analyzer アカウント名を入力します。 Video Analyzer アカウント名に使用できる文字は、小文字または数字のみで、空白を含めることはできません。長さは 3 文字から 24 文字です。|
    |**場所**|Video Analyzer アカウントのビデオとメタデータのレコードを保存する際に使用する地理的リージョンを選択します。 ドロップダウン リストのボックスには、利用可能な Video Analyzer リージョンのみが表示されます。 |
    |**ストレージ アカウント**|Video Analyzer アカウントのビデオ コンテンツの BLOB ストレージとなるストレージ アカウントを選択します。 Video Analyzer アカウントと同じリージョンにある既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。 新しいストレージ アカウントは同じリージョンに作成されます。 ストレージ アカウントの命名規則は、Video Analyzer アカウントと同じです。<br/>|
    |**管理対象 ID**|新しい Video Analyzer アカウントがストレージ アカウントにアクセスするために使用する、ユーザーが割り当てたマネージド ID を選択します。 既存のユーザー割り当てマネージド ID を選択することも、新しい ID を作成することもできます。 ユーザー割り当てマネージド ID には、ストレージ アカウントの [ストレージ Blob データ共同作成者][docs-storage-access] と [閲覧者][docs-role-reader]のロールが割り当てられます。

1. フォームの下部にある **[確認と作成]** をクリックします。

### <a name="post-deployment-steps"></a>デプロイ後の手順
既存の IoT Hub を Video Analyzer アカウントにアタッチすることもできますが、その場合は既存の UAMI が必要です。

1. リソースのデプロイが完了したら、 **[Go to resource (リソースに移動)]** をクリックします。
1. 設定で **[IoT Hub]** を選択し、 **[アタッチする]** をクリックします。  **[Attach IoT Hub]** の構成フライアウト ブレードで必要な値を入力します。
     - サブスクリプション - IoT Hub が作成された Azure サブスクリプション名を選択します
     - IoT Hub - 目的の IoT Hub を選択します
     - マネージド ID - IoT Hub へのアクセスに使用する既存の UAMI を選択します
1. **[Save (保存)]** をクリックして、IoT Hub を Video Analyzer アカウントにリンクします。

## <a name="template"></a>[テンプレート](#tab/template/)

[!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

### <a name="create-a-video-analyzer-account-using-a-template"></a>テンプレートを使用して Video Analyzer アカウントを作成する

このテンプレートでは、次のリソースが定義されています。

- [**Microsoft.Media/videoAnalyzers**](/azure/templates/Microsoft.Media/videoAnalyzers): Video Analyzer のアカウント リソース。
- [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): ビデオとメタデータを格納するために Video Analyzer によって使用されるストレージ アカウント。
- [**Microsoft.ManagedIdentity/userAssignedIdentities**](/azure/templates/Microsoft.ManagedIdentity/userAssignedIdentities): ストレージにアクセスするために Video Analyzer が使用するユーザー割り当てマネージド ID。
- [**Microsoft.Storage/storageAccounts/providers/roleAssignments**](/azure/templates/microsoft.authorization/roleassignments): Video Analyzer でストレージ アカウントにアクセスできるようにするロールの割り当て。

<!-- TODO replace with a reference like this:
:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.compute/vm-simple-linux/azuredeploy.json":::
-->

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "namePrefix": {
            "metadata": {
                "description": "Used to qualify the names of all of the resources created in this template."
            },
            "defaultValue": "avasample",
            "type": "string",
            "minLength": 3,
            "maxLength": 13
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('namePrefix'),uniqueString(resourceGroup().id))]",
        "accountName": "[concat(parameters('namePrefix'),uniqueString(resourceGroup().id))]",
        "managedIdentityName": "[concat(parameters('namePrefix'),'-',resourceGroup().name,'-storage-access-identity')]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "deploy-storage-and-identity",
            "properties": {
                "mode": "Incremental",
                "expressionEvaluationOptions": {
                    "scope": "Inner"
                },
                "parameters": {
                    "namePrefix": {
                        "value": "[parameters('namePrefix')]"
                    },
                    "managedIdentityName": {
                        "value": "[variables('managedIdentityName')]"
                    }
                },
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "namePrefix": {
                            "type": "string"
                        },
                        "managedIdentityName": {
                            "type": "string"
                        }
                    },
                    "variables": {
                        "storageAccountName": "[concat(parameters('namePrefix'),uniqueString(resourceGroup().id))]",
                        "managedIdentityName": "[parameters('managedIdentityName')]",
                        "storageBlobDataContributorAssignment": "[guid('Storage Blob Data Contributor',variables('managedIdentityName'))]",
                        "storageBlobDataContributorDefinitionId": "[concat(resourceGroup().id, '/providers/Microsoft.Authorization/roleDefinitions/', 'ba92f5b4-2d11-453d-a403-e96b0029c9fe')]",
                        "readerAssignment": "[guid('Reader',variables('managedIdentityName'))]",
                        "readerDefinitionId": "[concat(resourceGroup().id, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
                    },
                    "resources": [
                        {
                            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
                            "name": "[variables('managedIdentityName')]",
                            "apiVersion": "2015-08-31-preview",
                            "location": "[resourceGroup().location]"
                        },
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2019-04-01",
                            "name": "[variables('storageAccountName')]",
                            "location": "[resourceGroup().location]",
                            "sku": {
                                "name": "Standard_LRS"
                            },
                            "kind": "StorageV2",
                            "properties": {
                                "accessTier": "Hot"
                            }
                        },
                        {
                            "name": "[concat(variables('storageAccountName'), '/Microsoft.Authorization/', variables('storageBlobDataContributorAssignment'))]",
                            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
                            "apiVersion": "2021-04-01-preview",
                            "dependsOn": [
                                "[variables('managedIdentityName')]",
                                "[variables('storageAccountName')]"
                            ],
                            "properties": {
                                "roleDefinitionId": "[variables('storageBlobDataContributorDefinitionId')]",
                                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName')), '2018-11-30').principalId]",
                                "principalType": "ServicePrincipal"
                            }
                        },
                        {
                            "name": "[concat(variables('storageAccountName'), '/Microsoft.Authorization/', variables('readerAssignment'))]",
                            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
                            "apiVersion": "2021-04-01-preview",
                            "dependsOn": [
                                "[variables('managedIdentityName')]",
                                "[variables('storageAccountName')]"
                            ],
                            "properties": {
                                "roleDefinitionId": "[variables('readerDefinitionId')]",
                                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName')), '2018-11-30').principalId]",
                                "principalType": "ServicePrincipal"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        },
        {
            "type": "Microsoft.Media/videoAnalyzers",
            "comments": "The Azure Video Analyzer account",
            "apiVersion": "2021-05-01-preview",
            "name": "[variables('accountName')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "deploy-storage-and-identity"
            ],
            "properties": {
                "storageAccounts": [
                    {
                        "id": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
                        "identity": {
                            "userAssignedIdentity": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName'))]"
                        }
                    }
                ]
            },
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName'))]": {}
                }
            }
        }
    ],
    "outputs": { }
}
```

> [!NOTE]
> このテンプレートは、ロールの割り当てに入れ子になったデプロイを使用して、Video Analyzer アカウント リソースをデプロイする前に使用できるようにします。

### <a name="deploy-the-template"></a>テンプレートのデプロイ

1. *[Azure へのデプロイ]* ボタンをクリックして、Azure にサインインしてテンプレートを開きます。

    [![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

1. 次の値を選択または入力します。 既定値がある場合には、既定値を使用します。

    - **サブスクリプション**: Azure サブスクリプションを選択します。
    - **[リソース グループ]** : ドロップダウンから既存のリソース グループを選択するか、または **[新規作成]** を選択してリソース グループの一意の名前を入力してから、 **[OK]** をクリックします。
    - **場所**: 場所を選択します。  たとえば、**米国西部 2** にします。
    - **名前プレフィックス**: リソースの名前のプレフィックスとして使用される文字列を指定します (既定値をお勧めします)。

1. **[Review + create]\(レビュー + 作成\)** を選択します。 検証が完了したら、 **[作成する]** を選択してテンプレートを作成し、デプロイします。

上記では、テンプレートのデプロイに Azure portal を使用しています。 Azure portal だけでなく、Azure PowerShell、Azure CLI、REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../../azure-resource-manager/templates/deploy-cli.md)」を参照してください。

### <a name="post-deployment-steps"></a>デプロイ後の手順
既存の IoT Hub を Video Analyzer アカウントにアタッチすることもできますが、その場合は既存の UAMI が必要です。

1. リソースのデプロイが完了したら、 **[Go to resource (リソースに移動)]** をクリックします。
1. 設定で **[IoT Hub]** を選択し、 **[アタッチする]** をクリックします。  **[Attach IoT Hub]** の構成フライアウト ブレードで必要な値を入力します。
     - サブスクリプション - IoT Hub が作成された Azure サブスクリプション名を選択します
     - IoT Hub - 目的の IoT Hub を選択します
     - マネージド ID - IoT Hub へのアクセスに使用する既存の UAMI を選択します
1. **[Save (保存)]** をクリックして、IoT Hub を Video Analyzer アカウントにリンクします。

### <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

作成したアカウントおよびその他のリソースは、Azure portal を使用して確認できます。 デプロイが完了したら、 **[リソース グループに移動]** を選択すると、アカウントとその他のリソースが表示されます。

### <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったリソース グループは削除してください。リソース グループを削除すると、そのリソース グループ内のアカウントとすべてのリソースが削除されます。

1. **[リソース グループ]** を選択します。
1. リソース グループのページで、 **[削除]** を選択します。
1. プロンプトが表示されたら、リソース グループ名を入力して **[削除]** を選択します。

---

### <a name="next-steps"></a>次のステップ

* [IoT Edge デバイスに Video Analyzer をデプロイする][docs-deploy-on-edge]方法について説明します。
* [ビデオをキャプチャしてクラウドに直接記録する](cloud/get-started-livepipelines-portal.md)方法について説明します。

<!-- links -->
[docs-uami]: ../../active-directory/managed-identities-azure-resources/overview.md
[docs-storage-access]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor
[docs-role-reader]: ../../role-based-access-control/built-in-roles.md#reader
[docs-arm-template]: ../../azure-resource-manager/templates/overview.md
[docs-deploy-on-edge]: deploy-iot-edge-device.md
