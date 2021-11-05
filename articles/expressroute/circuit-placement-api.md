---
title: Azure ExpressRoute の CrossConnnections 回線配置 API
description: この記事では、ExpressRoute パートナ向けに ExpressRoute の CrossConnections 回線配置 API に関する詳しい概要を示します。
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: mialdrid
ms.openlocfilehash: 3b464e0d9bb5c770109899f7aba8c9e0934d2064
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092555"
---
# <a name="expressroute-circuit-placement-api"></a>ExpressRoute 回線配置 API

ExpressRoute のパートナー向け回線配置 API を使用すると、ExpressRoute パートナーは特定のポート ペアで回線接続をプロビジョニングできます。 具体的には、ExpressRoute パートナーが 1 つのピアリングの場所で複数のポート ペアを管理している場合、この API を使用すると、どのポート ペアで ExpressRoute 回線を促進するかを選択することができます。

この API は、expressRouteCrossConnection リソースの種類を使用します。 詳細については、[ExpressRoute の CrossConnection API の開発と統合](cross-connections-api-development.md)に関するページを参照してください。

## <a name="workflow"></a>ワークフロー

1. ExpressRoute のお客様は、ターゲットとなる ExpressRoute 回線のサービス キーを共有します。

1. ExpressRoute パートナーは、expressRouteProviderPorts API を使用して GET を実行し、ターゲットのポート ペアの **PortPairDescription** を特定します。 ExpressRoute パートナーは、サブスクリプション内のすべてのポート ペアの PortPairDescriptions の一覧に対してクエリを実行したり、特定のピアリングの場所に対してクエリのスコープを指定したりすることができます。

1. ターゲットの PortPairDescription を特定すると、ExpressRoute パートナーは GET/PUT expressRouteCrossConnection を実行して ExpressRoute 回線をターゲットのポート ペアに移動します。

ExpressRoute パートナーは、expressRouteCrossConnections リソースに対して REST 操作を発行することで、レイヤー 2 とレイヤー 3 構成を管理します。

## <a name="api-development-and-integration-steps"></a>API の開発と統合の手順

### <a name="get-using-the-expressrouteproviderports-api-to-list-port-pairs"></a>expressRouteProviderPorts API を使って GET を実行してポート ペアを一覧表示する

ExpressRoute パートナーは、ターゲットとなるプロバイダー サブスクリプション内のすべてのポート ペアを一覧表示したり、特定のピアリングの場所にあるポート ペアを一覧表示したりすることができます。

### <a name="to-get-a-list-of-all-port-pairs-for-a-provider"></a>プロバイダーのすべてのポート ペアの一覧を取得する方法

https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/expressRouteProviderPorts 

#### <a name="get-operation"></a>GET 操作

```rest
{
    "parameters": {
      "api-version": "2020-03-01",
      "subscriptionId": "subid"
    },
    "responses": {
        "200": {
          "body": {
            "value": [
              {
                "portPairDescriptor": "bvtazureixpportpair1",
                "id": "/subscriptions/subid/providers/Microsoft.Network/ExpressRouteProviderPort/bvtazureixpportpair1",
                "type": "Microsoft.Network/expressRouteProviderPort",
                "location": "uswest",
                "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
                "properties": {
                    "portPairDescriptor": "bvtazureixpportpair",
                    "primaryAzurePort": "bvtazureixp01a",
                    "secondaryAzurePort": "bvtazureixp01b",
                    "peeringLocation": "SiliconValley",
                    "overprovisionFactor": 4,
                    "portBandwidthInMbps": 4000,
                    "usedBandwidthInMbps": 2500,
                    "remainingBandwidthInMbps": 1500
                }
              },
              {
                "portPairDescriptor": "bvtazureixpportpair2",
                "id": "/subscriptions/subid/providers/Microsoft.Network/ ExpressRouteProviderPort/bvtazureixpportpair2",
                "type": "Microsoft.Network/expressRouteProviderPort",
                "location": "uswest",
                "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
                "properties": {
                    "portPairDescriptor": "bvtazureixpportpair2",
                    "primaryAzurePort": "bvtazureixp02a",
                    "secondaryAzurePort": "bvtazureixp02b",
                    "peeringLocation": "seattle",
                    "overprovisionFactor": 4,
                    "portBandwidthInMbps": 4000,
                    "usedBandwidthInMbps": 1200,
                    "remainingBandwidthInMbps": 1800
                }
              }
            ]
          }
        }
      }
    }
  }
}
```

**応答の状態コード**

* 200 (OK) 要求が成功しました。ポートの一覧が取得されます。
* 4XX (無効な要求) いずれかの検証が失敗しました。たとえば、プロバイダーの subid が無効です。

### <a name="list-of-all-port-for-a-provider-for-a-particular-peering-location"></a>特定のピアリングの場所にあるプロバイダーの全ポートの一覧

#### <a name="get"></a>GET

https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/expressRouteProviderPorts?location={locationName}

#### <a name="get-operation"></a>GET 操作

```rest
{
  "parameters": {
    "api-version": "2020-03-01",
    "locationName": "SiliconValley",
    "subscriptionId": "subid"
  },
  "responses": {
    "200": {
      "body": {
        "value": [
          {
            "portPairDescriptor": "bvtazureixpportpair1",
            "id": "/subscriptions/subid/providers/Microsoft.Network/ ExpressRouteProviderPort /bvtazureixpportpair1",
            "type": "Microsoft.Network/expressRouteProviderPort",
            "location": "uswest",
            "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
            "properties": {
              "portPairDescriptor": "bvtazureixpportpair",
              "primaryAzurePort": "bvtazureixp01a",
              "secondaryAzurePort": "bvtazureixp01b",
              "peeringLocation": "SiliconValley",
              "overprovisionFactor": 4,
              "portBandwidthInMbps": 4000,
              "usedBandwidthInMbps": 2500,
              "remainingBandwidthInMbps": 1500
            }
          }
        ]
      }
    }
  }
}
```

**応答の状態コード**

* 200 (OK) 要求が成功しました。 ポートの一覧が取得されます。
* 4XX (無効な要求) いずれかの検証が失敗しました。たとえば、プロバイダーの subid が無効か、場所が無効です。

ポート ペアの記述子 ID を使用して特定のポートの詳細を取得します。

#### <a name="get"></a>GET

https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/expressRouteProviderPorts/{portPairDescriptor}

#### <a name="get-operation"></a>GET 操作

```rest
{
  "parameters": {
    "api-version": "2020-03-01",
    "portPairDescriptor": " bvtazureixpportpair1",
    "subscriptionId": "subid"
  },
  "responses": {
    "200": {
      "body": {
        "value": [
          {
            "portPairDescriptor": "bvtazureixpportpair1",
            "id": "/subscriptions/subid/providers/Microsoft.Network/ExpressRouteProviderPort/bvtazureixpportpair1",
            "type": "Microsoft.Network/expressRouteProviderPort",
            "location": "uswest",
            "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
            "properties": {
              "portPairDescriptor": "bvtazureixpportpair",
              "primaryAzurePort": "bvtazureixp01a",
              "secondaryAzurePort": "bvtazureixp01b",
              "peeringLocation": "SiliconValley",
              "overprovisionFactor": 4,
              "portBandwidthInMbps": 4000,
              "usedBandwidthInMbps": 2500,
              "remainingBandwidthInMbps": 15
            }
          }
        ]
      }
    }
  }
}
```

**状態コード   説明**

* 200 (OK) 要求が成功しました。 ポートの詳細が取得されます。
* 204 指定した記述子 ID を持つポート ペアは使用できません。
* 4XX (無効な要求) いずれかの検証が失敗しました。たとえば、プロバイダーの subid が無効です。

### <a name="put-expressroutecrossconnection-api-to-move-a-circuit-to-a-specific-port-pair"></a>expressRouteCrossConnection API を PUT して、特定のポート ペアに回線を移動する

ターゲット ポート ペアの portPairDescriptor を特定すると、ExpressRoute パートナーは [ExpressRouteCrossConnection API](/rest/api/expressroute/express-route-cross-connections/create-or-update) を使用して、ExpressRoute 回線を特定のポート ペアに移動できます。

現在、この API は、回線のプロビジョニング状態を更新するためにプロバイダーによって使用されています。 この同じ API は、回線のポート ペアを更新するためにプロバイダーによって使用されます。

現在、primaryAzurePort と secondaryAzurePort は読み取り専用のプロパティです。 今回、これらのポートの読み取り専用プロパティを無効にしました。

#### <a name="put"></a>PUT

https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/expressRouteCrossConnections/{crossConnectionName}?api-version=2021-02-01

#### <a name="put-operation"></a>PUT 操作

```rest
{
"parameters": {
    "api-version": "2020-03-01",
    "crossConnectionName": "The name of the cross connection",
    "subscriptionId": "subid"
  }
},
{
Request   "body": {
    " primaryAzurePort ": " bvtazureixp03a"
     "secondaryAzurePort": "bvtazureixp03b",
  }
}
Response:
{
  "name": "<circuitServiceKey>",
  "id": "/subscriptions/subid/resourceGroups/CrossConnectionSiliconValley/providers/Microsoft.Network/expressRouteCrossConnections/<circuitServiceKey>",
  "type": "Microsoft.Network/expressRouteCrossConnections",
  "location": "brazilsouth",
  "properties": {
    "provisioningState": "Enabled",
    "expressRouteCircuit": {
      "id": "/subscriptions/subid/resourceGroups/ertest/providers/Microsoft.Network/expressRouteCircuits/er1"
    },
    "peerings": [],
    "peeringLocation": "SiliconValley",
    "bandwidthInMbps": 1000,
    "primaryAzurePort": "bvtazureixp03a",
    "secondaryAzurePort": "bvtazureixp03b",
    "sTag": 2,
    "serviceProviderProvisioningState": "NotProvisioned"
  }
}
```

## <a name="next-steps"></a>次のステップ

すべての ExpressRoute REST API に関する詳細については、「[ExpressRoute REST API](/rest/api/expressroute/)」を参照してください。