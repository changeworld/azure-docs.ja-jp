---
title: CLI を使用して Azure ExpressRoute Direct を構成する方法 | Microsoft Docs
description: このページは、CLI を使用して ExpressRoute Direct (プレビュー) を構成するときに役立ちます。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cherylmc
ms.openlocfilehash: 989e96aa00ae65d1206f961a10893e3331670553
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958306"
---
# <a name="how-to-configure-expressroute-direct-using-cli-preview"></a>CLI を使用して ExpressRoute Direct (プレビュー) を構成する方法

ExpressRoute Direct を使用すると、世界中に戦略的に分散されたピアリングの場所で Microsoft のグローバル ネットワークに直接接続できます。 詳しくは、[ExpressRoute Direct の接続](expressroute-erdirect-about.md)に関する記事をご覧ください。

> [!IMPORTANT]
> ExpressRoute Direct は現在プレビューの段階です。
>
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

## <a name="resources"></a>リソースを作成する

1. Azure にサインインしてサブスクリプションを選択します。 ExpressRoute Direct リソースおよび ExpressRoute 回線は、同じサブスクリプション内にある必要があります。

  ```azurecli
  az login
  ```

  アカウントのサブスクリプションを確認します。 

  ```azurecli
  az account list 
  ```

  ExpressRoute 回線を作成するサブスクリプションを選択します。
  ```azurecli
  az account set --subscription "<subscription ID>"
  ```
2. ExpressRoute Direct がサポートされるすべての場所を一覧表示します。
    
  ```azurecli
  az network express-route port location list
  ```

  **出力例**
  
  ```azurecli
  [
   {
    "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
    "location": null,
    "name": "Equinix-Ashburn-DC2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA3",
    "location": null,
    "name": "Equinix-Dallas-DA3",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "111 8th Avenue, New York, NY 10011",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-New-York-NY5",
    "location": null,
    "name": "Equinix-New-York-NY5",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "11 Great Oaks Blvd, SV1, San Jose, CA 95119",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-SV1",
    "location": null,
    "name": "Equinix-San-Jose-SV1",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "2001 Sixth Ave., Suite 350, SE2, Seattle, WA 98121",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Seattle-SE2",
    "location": null,
    "name": "Equinix-Seattle-SE2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   }
  ]
  ```
3. 上記で表示された場所に使用可能な帯域幅があるかどうか確認します。

  ```azurecli
  az network express-route port location show -l "Equinix-Ashburn-DC2"
  ```

  **出力例**

  ```azurecli
  {
  "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
  "availableBandwidths": [
    {
      "offerName": "100 Gbps",
      "valueInGbps": 100
    }
  ],
  "contact": "support@equinix.com",
  "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
  "location": null,
  "name": "Equinix-Ashburn-DC2",
  "provisioningState": "Succeeded",
  "tags": null,
  "type": "Microsoft.Network/expressRoutePortsLocations"
  }
  ```
4. 上記で選択した場所に基づいて ExpressRoute Direct リソースを作成します。

  ExpressRoute Direct では、QinQ と Dot1Q 両方のカプセル化がサポートされます。 QinQ を選択した場合、各 ExpressRoute 回線に S-Tag が動的に割り当てられ、ExpressRoute Direct リソース全体で一意になります。 回線上の各 C-Tag はその回線で一意である必要がありますが、ExpressRoute Direct 全体ではありません。  

  Dot1Q カプセル化を選択した場合、ExpressRoute Direct リソース全体で C-Tag (VLAN) が一意になるように管理する必要があります。  

  > [!IMPORTANT]
  > ExpressRoute Direct ではカプセル化の種類を 1 つしか選択できません。 ExpressRoute Direct を作成した後でカプセル化を変更することはできません。
  > 
 
  ```azurecli
  az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
  ```

  > [!NOTE]
  > Encapsulation 属性に Dot1Q を設定することもできます。 
  >

  **出力例:**

  ```azurecli
  {
  "allocationDate": "Wednesday, October 17, 2018",
  "bandwidthInGbps": 100,
  "circuits": null,
  "encapsulation": "Dot1Q",
  "etag": "W/\"<etagnumber>\"",
  "etherType": "0x8100",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
  "links": [
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
  ],
  "location": "westus",
  "mtu": "1500",
  "name": "Contoso-Direct",
  "peeringLocation": "Equinix-Ashburn-DC2",
  "provisionedBandwidthInGbps": 0.0,
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "resourceGuid": "02ee21fe-4223-4942-a6bc-8d81daabc94f",
  "tags": null,
  "type": "Microsoft.Network/expressRoutePorts"
  }  
  ```

## <a name="state"></a>リンクの管理状態を変更する

このプロセスは、レイヤー 1 のテストを実施して、各相互接続がプライマリとセカンダリの各ルーターに適切に接続されていることを確認するために使用する必要があります。

1. リンクを Enabled に設定します。 この手順を繰り返して、各リンクを有効に設定します。

  Links[0] はプライマリ ポート、Links[1] はセカンダリ ポートです。

  ```azurecli
  az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
  ```
  ```azurecli
  az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
  ```
  **出力例:**

  ```azurecli
  {
  "allocationDate": "Wednesday, October 17, 2018",
  "bandwidthInGbps": 100,
  "circuits": null,
  "encapsulation": "Dot1Q",
  "etag": "W/\"<etagnumber>\"",
  "etherType": "0x8100",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
  "links": [
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
  ],
  "location": "westus",
  "mtu": "1500",
  "name": "Contoso-Direct",
  "peeringLocation": "Equinix-Ashburn-DC2",
  "provisionedBandwidthInGbps": 0.0,
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "resourceGuid": "<resourceGUID>",
  "tags": null,
  "type": "Microsoft.Network/expressRoutePorts"
  }
  ```

  同じ手順を `AdminState = “Disabled”` について使用して、ポートを停止します。

## <a name="circuit"></a>回線を作成する

既定では、ExpressRoute Direct リソースがあるサブスクリプション内に 10 個の回線を作成できます。 これはサポートによって増やすことができます。 プロビジョニング済みの帯域幅と使用済みの帯域幅の両方を追跡してください。 プロビジョニング済み帯域幅は、ExpressRoute Direct リソース上のすべての回線の帯域幅の合計です。使用済み帯域幅は、基になる物理インターフェイスの物理的な使用量です。

ExpressRoute Direct には、前述したシナリオをサポートするために利用できるだけの追加の回線帯域幅があります。 40 Gbps と 100 Gbps です。

Standard 回線または Premium 回線を作成できます。 Standard 回線は料金に含まれていますが、Premium 回線には選択した帯域幅に基づいた費用がかかります。 回線は従量制のみで作成でき、無制限は ExpressRoute Direct ではサポートされません。

ExpressRoute Direct リソース上に回線を作成します。

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  その他の帯域幅には、5 Gbps、10 Gbps、40 Gbps が含まれます

  **出力例:**

  ```azurecli
  {
  "allowClassicOperations": false,
  "allowGlobalReach": false,
  "authorizations": [],
  "bandwidthInGbps": 100.0,
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"<etagnumber>\"",
  "expressRoutePort": {
    "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
    "resourceGroup": "Contoso-Direct-rg"
  },
  "gatewayManagerEtag": "",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRouteCircuits/ERDirect-ckt-cli",
  "location": "westus",
  "name": "ERDirect-ckt-cli",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "serviceKey": "<serviceKey>",
  "serviceProviderNotes": null,
  "serviceProviderProperties": null,
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "MeteredData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "stag": null,
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits"
  }  
  ```

## <a name="next-steps"></a>次の手順

ExpressRoute Direct について詳しくは、[概要](expressroute-erdirect-about.md)のページをご覧ください。
