---
title: リソース管理のプライベート リンクを管理する
description: REST API を使用して既存のリソース管理のプライベート リンクを管理する
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 376fa600cce4479a0ed2c04e9d3d7b3fbeb9aeaa
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227299"
---
# <a name="manage-resource-management-private-links-with-rest-api"></a>REST API を使用してリソース管理のプライベート リンクを管理する

この記事では、既存のリソース管理のプライベート リンクを操作する方法について説明します。 ここでは、既存のリソースを取得および削除するための REST API 操作を示します。

リソース管理のプライベート リンクを作成する必要がある場合は、[ポータルを使用して Azure リソースを管理するためのプライベート リンクを作成する](create-private-link-access-portal.md)、または [REST API を使用して Azure リソースを管理するためのプライベート リンクを作成する](create-private-link-access-rest.md)に関連するページを参照してください。

## <a name="resource-management-private-links"></a>リソース管理のプライベート リンク

**特定** のリソース管理のプライベート リンクを取得するには、次の要求を送信します。

```http
GET
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01 
```

この操作によって次のものが返されます。

```json
{
  "properties": {
    "privateEndpointConnections": []
  },
  "id": {rmplResourceId},
  "name": {rmplName},
  "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
  "location": {region}
}
```

サブスクリプション内の **すべて** のリソース管理のプライベート リンクを取得するには、次のものを使用します。

```http
GET
https://management.azure.com/subscriptions/{subscriptionID}/providers/Microsoft.Authorization/resourceManagementPrivateLinks?api-version=2020-05-01
```

この操作によって次のものが返されます。

```json
[
  {
    "properties": {
      "privateEndpointConnections": []
    },
    "id": {rmplResourceId},
    "name": {rmplName},
    "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
    "location": {region}
  },
  {
    "properties": {
      "privateEndpointConnections": []
    },
    "id": {rmplResourceId},
    "name": {rmplName},
    "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
    "location": {region}
  }
]
```

**特定** のリソース管理のプライベート リンクを削除するには、次のものを使用します。

```http
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01
```

この操作によって `Status 200 OK` が返されます。

## <a name="private-link-association"></a>プライベート リンクの関連付け

管理グループの **特定** のプライベート リンクの関連付けを取得するには、次のものを使用します。

```http
GET
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupID}/providers/Microsoft.Authorization/privateLinkAssociations?api-version=2020-05-01 
```

この操作によって次のものが返されます。

```json
{
  "value": [
    {
      "properties": {
        "privateLink": {rmplResourceID},
        "tenantId": {tenantId},
        "scope": "/providers/Microsoft.Management/managementGroups/{managementGroupId}"
      },
      "id": {plaResourceId},
      "type": "Microsoft.Authorization/privateLinkAssociations",
      "name": {plaName}
    }
  ]
}
```

プライベート リンクの関連付けを **削除** するには、次のものを使用します。

```http
DELETE 
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupID}/providers/Microsoft.Authorization/privateLinkAssociations/{plaID}?api-version=2020-05-01
```

この操作によって `Status 200 OK` が返されます。

## <a name="private-endpoints"></a>プライベート エンドポイント

サブスクリプション内の **すべて** のプライベート エンドポイントを取得するには、次のものを使用します。

```http
GET 
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/privateEndpoints?api-version=2020-04-01
```

この操作によって次のものが返されます。

```json
{
  "value": [
    {
      "name": {privateEndpointName},
      "id": {privateEndpointResourceId},
      "etag": {etag},
      "type": "Microsoft.Network/privateEndpoints",
      "location": {region},
      "properties": {
        "provisioningState": "Updating",
        "resourceGuid": {GUID},
        "privateLinkServiceConnections": [
          {
            "name": {connectionName},
            "id": {connectionResourceId},
            "etag": {etag},
            "properties": {
              "provisioningState": "Succeeded",
              "privateLinkServiceId": {rmplResourceId},
              "groupIds": [
                "ResourceManagement"
              ],
              "privateLinkServiceConnectionState": {
                "status": "Approved",
                "description": "",
                "actionsRequired": "None"
              }
            },
            "type": "Microsoft.Network/privateEndpoints/privateLinkServiceConnections"
          }
        ],
        "manualPrivateLinkServiceConnections": [],
        "subnet": {
          "id": {subnetResourceId}
        },
        "networkInterfaces": [
          {
            "id": {networkInterfaceResourceId}
          }
        ],
        "customDnsConfigs": [
          {
            "fqdn": "management.azure.com",
            "ipAddresses": [
              "10.0.0.4"
            ]
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

* プライベート リンクの詳細については、[Azure Private Link](../../private-link/index.yml) に関するページを参照してください。
* リソース管理のプライベート リンクを作成するには、[ポータルを使用して Azure リソースを管理するためのプライベート リンクを作成する](create-private-link-access-portal.md)、または [REST API を使用して Azure リソースを管理するためのプライベート リンクを作成する](create-private-link-access-rest.md)に関連するページを参照してください。