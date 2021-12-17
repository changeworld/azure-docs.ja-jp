---
title: リソース プロバイダーとリソースの種類
description: Azure Resource Manager をサポートするリソース プロバイダーについて説明します。 ここでは、そのスキーマと利用可能な API バージョン、およびリソースをホストできるリージョンについて説明します。
ms.topic: conceptual
ms.date: 11/15/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c048da5d7027885bf30b512d9e16e5851697c8b4
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522652"
---
# <a name="azure-resource-providers-and-types"></a>Azure リソース プロバイダーと種類

リソースをデプロイするときに、リソース プロバイダーと種類に関する情報を取得しなければならないケースは少なくありません。 たとえば、キーとシークレットを格納するには、Microsoft.KeyVault リソース プロバイダーを使用します。 このリソース プロバイダーには、キー コンテナーを作成する vaults というリソースの種類が用意されています。

リソースの種類を表す名前は、**{resource-provider}/{resource-type}** のような形式になります。 キー コンテナーのリソースの種類は **Microsoft.KeyVault/vaults** です。

この記事では、次のことについて説明します。

* Azure のすべてのリソース プロバイダーを表示する
* リソース プロバイダーの登録ステータスを確認する
* リソース プロバイダーを登録する
* リソース プロバイダーのリソースの種類を表示する
* リソースの種類の有効な場所を表示する
* リソースの種類の有効な API のバージョンを表示する

これらの手順は、Azure Portal、Azure PowerShell、または Azure CLI を介して実行できます。

リソース プロバイダーを Azure サービスにマップされるリストについては、「[Resource providers for Azure services](azure-services-resource-providers.md)」 (Azure サービスのリソースプロバイダー) を参照してください。

## <a name="register-resource-provider"></a>リソース プロバイダーの登録

リソース プロバイダーを使用する前に、Azure サブスクリプションにリソース プロバイダーを登録する必要があります。 登録により、サブスクリプションがリソース プロバイダーと連携するように構成されます。 

> [!IMPORTANT]
> リソース プロバイダーの登録は、それを使用する準備ができた場合のみ行ってください。 登録手順により、サブスクリプション内で最小限の特権を維持できます。 悪意のあるユーザーは、登録されていないリソース プロバイダーを使用することはできません。

一部のリソース プロバイダーが既定で登録されています。 既定で登録されているリソース プロバイダーの一覧については、「[Azure サービスのリソース プロバイダー](azure-services-resource-providers.md)」を参照してください。

その他のリソース プロバイダーは、特定のアクションを実行すると自動的に登録されます。 ポータルを使用してリソースを作成すると、通常、リソース プロバイダーが自動的に登録されます。 Azure Resource Manager テンプレートまたは Bicep ファイルをデプロイすると、テンプレートに定義されているリソース プロバイダーが自動的に登録されます。 ただし、テンプレート内のリソースによって、テンプレート内にないサポート リソース (監視やセキュリティ リソースなど) を作成する場合は、これらのリソース プロバイダーを手動で登録する必要があります。

他のシナリオでは、場合によっては、リソース プロバイダーを手動で登録する必要があります。

> [!IMPORTANT]
> アプリケーション コードによって、**登録中** 状態にあるリソース プロバイダーの **リソースの作成がブロックされるべきではありません**。 リソース プロバイダーを登録すると、サポートされているリージョンごとに操作が個別に実行されます。 リージョンにリソースを作成する際は、そのリージョンでのみ登録を完了する必要があります。 登録中の状態にあるリソース プロバイダーがブロックされないようにすることで、すべてのリージョンの完了まで待機するよりもはるかに早くアプリケーションを続行できます。

リソース プロバイダーの `/register/action` 操作を実行するためのアクセス許可が必要です。 このアクセス許可は、共同作成者ロールと所有者ロールに含まれます。

サブスクリプション内に特定のリソース プロバイダーからのリソースの種類がまだある場合、そのリソース プロバイダーの登録を解除することはできません。

## <a name="azure-portal"></a>Azure portal

### <a name="register-resource-provider"></a>リソース プロバイダーの登録

すべてのリソース プロバイダー、およびサブスクリプションの登録状態を表示するには、以下を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Azure portal のメニューで **サブスクリプション** を検索します。 使用可能なオプションから、それを選択します。

   :::image type="content" source="./media/resource-providers-and-types/search-subscriptions.png" alt-text="サブスクリプションの検索":::

1. 表示するサブスクリプションを選択します。

   :::image type="content" source="./media/resource-providers-and-types/select-subscription.png" alt-text="サブスクリプションの選択":::

1. 左側のメニューの **[設定]** で、 **[リソース プロバイダー]** を選択します。

   :::image type="content" source="./media/resource-providers-and-types/select-resource-providers.png" alt-text="リソース プロバイダーの選択":::

6. 登録するリソース プロバイダーを探し、 **[登録]** を選択します。 サブスクリプションで最小限の特権を維持するには、使用する準備ができたリソース プロバイダーのみ登録します。

   :::image type="content" source="./media/resource-providers-and-types/register-resource-provider.png" alt-text="リソース プロバイダーの登録":::

> [!IMPORTANT]
> [前述のように](#register-resource-provider)、****登録中** 状態にあるリソース プロバイダーのリソースの作成をブロックしないでください。** 登録中の状態にあるリソース プロバイダーがブロックされないようにすることで、すべてのリージョンの完了まで待機するよりもはるかに早くアプリケーションを続行できます。


### <a name="view-resource-provider"></a>リソース プロバイダーの表示

特定のリソース プロバイダーの情報を表示するには、以下を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. Azure portal のメニューで、**[すべてのサービス]** を選択します。
3. **[すべてのサービス]** ボックスで **"リソース エクスプ ローラー (resource explorer)"** と入力し、**[リソース エクスプ ローラー]** を選択します。

    ![[すべてのサービス] を選択する](./media/resource-providers-and-types/select-resource-explorer.png)

4. 右矢印を選択して **[プロバイダー]** を展開します。

    ![プロバイダーの選択](./media/resource-providers-and-types/select-providers.png)

5. 表示するリソース プロバイダーとリソースの種類を展開します。

    ![リソースの種類の選択](./media/resource-providers-and-types/select-resource-type.png)

6. リソース マネージャーはすべてのリージョンでサポートされていますが、デプロイするリソースはすべてのリージョンではサポートされていない場合があります。 また、サブスクリプションでの制限により、リソースをサポートする一部のリージョンを使用できない場合があります。 リソース エクスプローラーでは、リソースの種類の有効な場所が表示されます。

    ![場所の表示](./media/resource-providers-and-types/show-locations.png)

7. API バージョンは、リリース プロバイダーがリリースする REST API のバージョンに一致します。 リソース プロバイダーは、新しい機能を有効にすると、REST API の新しいバージョンをリリースします。 リソース エクスプローラーでは、リソースの種類の有効 API のバージョンが表示されます。

    ![API のバージョンの表示](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

Azure ですべてのリソース プロバイダーおよびサブスクリプションの登録状態を表示するには、次のコマンドを使用します。

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

このコマンドによって次の情報が返されます。

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

サブスクリプションに登録されているリソース プロバイダーをすべて確認するには、次のコマンドを使用します。

```azurepowershell-interactive
 Get-AzResourceProvider -ListAvailable | Where-Object RegistrationState -eq "Registered" | Select-Object ProviderNamespace, RegistrationState | Sort-Object ProviderNamespace
```

サブスクリプションで最小限の特権を維持するには、使用する準備ができたリソース プロバイダーのみ登録します。 リソース プロバイダーを登録するには、次のコマンドを使用します。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

このコマンドによって次の情報が返されます。

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

> [!IMPORTANT]
> [前述のように](#register-resource-provider)、****登録中** 状態にあるリソース プロバイダーのリソースの作成をブロックしないでください。** 登録中の状態にあるリソース プロバイダーがブロックされないようにすることで、すべてのリージョンの完了まで待機するよりもはるかに早くアプリケーションを続行できます。

特定のリソース プロバイダーの情報を表示するには、次のコマンドを使用します。

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

このコマンドによって次の情報が返されます。

```output
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

リソース プロバイダーのリソースの種類を表示するには、次のコマンドを使用します。

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

このコマンドによって次の情報が返されます。

```output
batchAccounts
operations
locations
locations/quotas
```

API バージョンは、リリース プロバイダーがリリースする REST API のバージョンに一致します。 リソース プロバイダーは、新しい機能を有効にすると、REST API の新しいバージョンをリリースします。

リソースの種類の使用可能な API バージョンを取得するには、次のコマンドを使用します。

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

このコマンドによって次の情報が返されます。

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

リソース マネージャーはすべてのリージョンでサポートされていますが、デプロイするリソースはすべてのリージョンではサポートされていない場合があります。 また、サブスクリプションでの制限により、リソースをサポートする一部のリージョンを使用できない場合があります。

リソースの種類のサポートされている場所を取得するには、次のコマンドを使用します。

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

このコマンドによって次の情報が返されます。

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Azure ですべてのリソース プロバイダーおよびサブスクリプションの登録状態を表示するには、次のコマンドを使用します。

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

このコマンドによって次の情報が返されます。

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

サブスクリプションに登録されているリソース プロバイダーをすべて確認するには、次のコマンドを使用します。

```azurecli-interactive
az provider list --query "sort_by([?registrationState=='Registered'].{Provider:namespace, Status:registrationState}, &Provider)" --out table
```

サブスクリプションで最小限の特権を維持するには、使用する準備ができたリソース プロバイダーのみ登録します。 リソース プロバイダーを登録するには、次のコマンドを使用します。

```azurecli-interactive
az provider register --namespace Microsoft.Batch
```

登録が進行中であることを示すメッセージが返されます。

特定のリソース プロバイダーの情報を表示するには、次のコマンドを使用します。

```azurecli-interactive
az provider show --namespace Microsoft.Batch
```

このコマンドによって次の情報が返されます。

```output
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

> [!IMPORTANT]
> [前述のように](#register-resource-provider)、****登録中** 状態にあるリソース プロバイダーのリソースの作成をブロックしないでください。** 登録中の状態にあるリソース プロバイダーがブロックされないようにすることで、すべてのリージョンの完了まで待機するよりもはるかに早くアプリケーションを続行できます。

リソース プロバイダーのリソースの種類を表示するには、次のコマンドを使用します。

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

このコマンドによって次の情報が返されます。

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

API バージョンは、リリース プロバイダーがリリースする REST API のバージョンに一致します。 リソース プロバイダーは、新しい機能を有効にすると、REST API の新しいバージョンをリリースします。

リソースの種類の使用可能な API バージョンを取得するには、次のコマンドを使用します。

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

このコマンドによって次の情報が返されます。

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

リソース マネージャーはすべてのリージョンでサポートされていますが、デプロイするリソースはすべてのリージョンではサポートされていない場合があります。 また、サブスクリプションでの制限により、リソースをサポートする一部のリージョンを使用できない場合があります。

リソースの種類のサポートされている場所を取得するには、次のコマンドを使用します。

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

このコマンドによって次の情報が返されます。

```output
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>次のステップ

<<<<<<< HEAD
* リソース マネージャーのテンプレートの作成の詳細については、 [Azure Resource Manager のテンプレートの作成](../templates/template-syntax.md)に関するページを参照してください。 
=======
* リソース マネージャーのテンプレートの作成の詳細については、 [Azure リソース マネージャーのテンプレートの作成](../templates/syntax.md)に関するページを参照してください。 
>>>>>>> repo_sync_working_branch
* リソース プロバイダーのテンプレートのスキーマを表示するには、「[テンプレート リファレンス](/azure/templates/)」を参照してください。
* リソース プロバイダーを Azure サービスにマップされるリストについては、「[Resource providers for Azure services](azure-services-resource-providers.md)」 (Azure サービスのリソースプロバイダー) を参照してください。
* リソース プロバイダーの操作を表示するには、「[Azure REST API](/rest/api/)」を参照してください。