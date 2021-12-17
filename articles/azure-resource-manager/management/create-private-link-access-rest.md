---
title: プライベート リンクを介してリソースを管理する
description: リソースの管理アクセスをプライベート リンクに制限します
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: eb7e81ef739fdb94bc91c65b079d5b76b22636c9
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227297"
---
# <a name="use-rest-api-to-create-private-link-for-managing-azure-resources"></a>REST API を使用して Azure リソースを管理するためのプライベート リンクを作成する

この記事では、サブスクリプション内のリソースを管理するために [Azure Private Link](../../private-link/index.yml) を使用してアクセスを制限する方法について説明します。

[!INCLUDE [Create content](../../../includes/resource-manager-create-rmpl.md)]

## <a name="create-resource-management-private-link"></a>リソース管理のプライベート リンクを作成する

リソース管理のプライベート リンクを作成するには、次の要求を送信します。

```http
PUT
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01
```

要求本文に、リソースの場所を含めます。

```json
{
  "location":"{region}"
}
```

この操作によって次のものが返されます。

```json
{  
  "id": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{name}",
  "location": "{region}",
  "name": "{rmplName}",
  "properties": {
    "privateEndpointConnections": []
  },
  "resourceGroup": "{rgName}",
  "type": "Microsoft.Authorization/resourceManagementPrivateLinks"
}
```

新しいリソース管理のプライベート リンクに対して返される ID をメモしておきます。 これは、プライベート リンクの関連付けを作成するために使用します。

## <a name="create-private-link-association"></a>プライベート リンクの関連付けを作成する

プライベート リンクの関連付けを作成するには、次を使用します。

```http
PUT
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupId}/providers/Microsoft.Authorization/privateLinkAssociations/{GUID}?api-version=2020-05-01 
```

要求本文は次のようになります。

```json
{
  "properties": {
    "privateLink": "/subscriptions/{subscription-id}/resourceGroups/{rg-name}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}",
    "publicNetworkAccess": "enabled"
  }
}
```

この操作によって次のものが返されます。

```json
{
  "id": {plaResourceId},
  "name": {plaName},
  "properties": {
    "privateLink": {rmplResourceId},
    "publicNetworkAccess": "Enabled",
    "tenantId": "{tenantId}",
    "scope": "/providers/Microsoft.Management/managementGroups/{managementGroupId}"
  },
  "type": "Microsoft.Authorization/privateLinkAssociations"
}
```

## <a name="add-private-endpoint"></a>プライベート エンドポイントの追加

この記事では、既に仮想ネットワークがあることを前提としています。 プライベート エンドポイントに使用するサブネットで、プライベート エンドポイントのネットワーク ポリシーをオフにする必要があります。 プライベート エンドポイントのネットワーク ポリシーをオフにしていない場合は、[プライベート エンドポイントのネットワーク ポリシーを無効にする](../../private-link/disable-private-endpoint-network-policy.md)方法に関するページを参照してください。

プライベート エンドポイントを作成するには、次の操作を使用します。

```http
PUT
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/privateEndpoints/{privateEndpointName}?api-version=2020-11-01
```

要求本文で、`privateServiceLinkId` をリソース管理のプライベート リンクの ID に設定します。 `groupIds` には `ResourceManagement` が含まれている必要があります。 プライベート エンドポイントの場所は、サブネットの場所と同じである必要があります。

```json
{
  "location": "westus2",
  "properties": {
    "privateLinkServiceConnections": [
      {
        "name": "{connection-name}",
        "properties": {
           "privateLinkServiceId": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{name}",
           "groupIds": [
              "ResourceManagement"
           ]
         }
      }
    ],
    "subnet": {
      "id": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
    }
  }
}
```

次に行う手順は、自動承認と手動承認のどちらを使用しているかによって異なります。 承認の詳細については、「[承認ワークフローを使用したプライベート リンク リソースへのアクセス](../../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)」を参照してください。

応答には承認状態が含まれます。

```json
"privateLinkServiceConnectionState": {
    "actionsRequired": "None",
    "description": "",
    "status": "Approved"
},
```

要求が自動的に承認される場合は、次のセクションに進むことができます。 要求が手動の承認を必要としている場合は、ネットワーク管理者によってプライベート エンドポイント接続が承認されるのを待ちます。

## <a name="next-steps"></a>次のステップ

プライベート リンクの詳細については、[Azure Private Link](../../private-link/index.yml) に関するページを参照してください。