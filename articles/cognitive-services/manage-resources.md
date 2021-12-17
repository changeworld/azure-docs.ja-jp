---
title: 削除された Cognitive Services リソースの復旧
titleSuffix: Azure Cognitive Services
description: この記事では、既に削除されている Cognitive Services リソースを復旧する方法について説明します。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: nitinme
ms.openlocfilehash: 4dce35c1713a4dcb4880080d1f28ed124a2209be
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546079"
---
# <a name="recover-deleted-cognitive-services-resources"></a>削除された Cognitive Services リソースの復旧

この記事では、既に削除されている Cognitive Services リソースを復旧する方法について説明します。 また、削除されたリソースを消去する方法についても説明します。

> [!NOTE]
> この記事の手順は、マルチサービス リソースと単一サービス リソースの両方に適用されます。 マルチサービス リソースでは、1 つのキーとエンドポイントを使用して複数の Cognitive Services にアクセスできます。 一方、単一サービス リソースでは、リソースが作成された特定の Cognitive Services だけにアクセスできます。

## <a name="prerequisites"></a>前提条件

* 復旧するリソースは、過去 48 時間以内に削除されたものである必要があります。
* 復旧するリソースは、まだ消去されていない必要があります。 消去されたリソースを復旧できません。
* 削除されたリソースの復旧を試みる前に、そのアカウントのリソース グループが存在することを確認してください。 リソース グループが削除されている場合は、再作成する必要があります。 リソース グループを復旧することはできません。 詳しくは、 [リソース グループの管理](../azure-resource-manager/management/manage-resource-groups-portal.md)に関する記事をご覧ください。
* 削除されたリソースによって Azure Key Vault でカスタマー マネージド キーが使用されており、キー コンテナーも削除されている場合は、Cognitive Services リソースを復元する前に、そのキー コンテナーを復元する必要があります。 詳細については、[Azure Key Vault の復旧の管理](../key-vault/general/key-vault-recovery.md)に関する記事を参照してください。
* 削除されたリソースによってユーザーが管理するストレージが使用されており、そのストレージ アカウントも削除されている場合は、Cognitive Services リソースを復元する前に、そのストレージ アカウントを復元する必要があります。 詳細については、「[削除されたストレージ アカウントを復旧する](../storage/common/storage-account-recover.md)」を参照してください。

サブスクリプションには、[Cognitive Services 共同作成者](../role-based-access-control/built-in-roles.md#cognitive-services-contributor)または[共同作成者](../role-based-access-control/built-in-roles.md#contributor)などのリソースを消去するための `Microsoft.CognitiveServices/locations/resourceGroups/deletedAccounts/delete` アクセス許可が必要です。 

## <a name="recover-a-deleted-resource"></a>削除されたリソースを復旧する 

削除された Cognitive Services リソースを復旧するには、次のコマンドを使用します。 該当する場合は、次のように置き換えます。

* `{subscriptionID}` は Azure サブスクリプション ID
* `{resourceGroup}` はリソース グループ
* `{resourceName}` はリソース名
* `{location}` はリソースの場所

### <a name="using-the-rest-api"></a>REST API を使用して

次のコマンド `PUT` を使用します。

```rest-api
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroup}/providers/Microsoft.CognitiveServices/accounts/{resourceName}?Api-Version=2021-04-30
```

要求本文として次の JSON 形式を使用します。

```json
{ 
  "location": "{location}", 
   "properties": { 
        "restore": true 
    } 
} 
```

### <a name="using-powershell"></a>PowerShell の使用

リソースを復元するには、次のコマンドを使用します。 

```powershell
New-AzResource -Location {location} -Properties @{restore=$true} -ResourceId /subscriptions/{subscriptionID}/resourceGroups/{resourceGroup}/providers/Microsoft.CognitiveServices/accounts/{resourceName}   -ApiVersion 2021-04-30 
```

削除されたリソースの名前を検索する必要がある場合は、次のコマンドを使用して、削除されたリソース名の一覧を取得できます。 

```powershell
Get-AzResource -ResourceId /subscriptions/{subscriptionId}/providers/Microsoft.CognitiveServices/deletedAccounts -ApiVersion 2021-04-30 
```

### <a name="using-the-azure-cli"></a>Azure CLI の使用

```azurecli-interactive
az resource create --subscription {subscriptionID} -g {resourceGroup} -n {resourceName} --location {location} --namespace Microsoft.CognitiveServices --resource-type accounts --properties "{\"restore\": true}"
```

## <a name="purge-a-deleted-resource"></a>削除されたリソースを消去する 

リソースを削除すると、48 時間は同じ名前で別のリソースを作成することはでません。 同じ名前のリソースを作成するには、削除したリソースを消去する必要があります。

削除した Cognitive Services リソースを消去するには、次のコマンドを使用します。 該当する場合は、次のように置き換えます。

* `{subscriptionID}` は Azure サブスクリプション ID
* `{resourceGroup}` はリソース グループ
* `{resourceName}` はリソース名
* `{location}` はリソースの場所

> [!NOTE]
> リソースが消去されると、完全に削除され、復元できなくなります。 リソースに関連付けられているすべてのデータとキーが失われます。

### <a name="using-the-rest-api"></a>REST API を使用して

次のコマンド `DELETE` を使用します。

```rest-api
https://management.azure.com/subscriptions/{subscriptionID}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}?Api-Version=2021-04-30`
```

### <a name="using-powershell"></a>PowerShell の使用

```powershell
Remove-AzResource -ResourceId /subscriptions/{subscriptionID}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}  -ApiVersion 2021-04-30`
```

### <a name="using-the-azure-cli"></a>Azure CLI の使用

```azurecli-interactive
az resource delete --ids /subscriptions/{subscriptionId}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}
```

## <a name="see-also"></a>関連項目
* [Azure portal を使用して新しいリソースを作成する](cognitive-services-apis-create-account.md)
* [Azure CLI を使用して新しいリソースを作成する](cognitive-services-apis-create-account-cli.md)
* [クライアント ライブラリを使用して新しいリソースを作成する](cognitive-services-apis-create-account-client-library.md)
* [ARM テンプレートを使用して新しいリソースを作成する](create-account-resource-manager-template.md)
