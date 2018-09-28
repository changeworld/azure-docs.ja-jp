---
title: Azure ExpressRoute の監視、メトリック、およびアラート | Microsoft Docs
description: このページでは、ExpressRoute の監視について説明しています。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 73bacebe6edc5b1d1273a10a0db7397766c45b0c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971645"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute の監視、メトリック、およびアラート

 この記事では、ExpressRoute の監視、メトリック、およびアラートについて説明します。 Azure Monitor を使用すると、Azure 全体のすべてのメトリック、アラート、診断ログを一元的に管理できます。

## <a name="circuit-metrics"></a>回線のメトリック

**[メトリック]** に移動するには、監視する回線の ExpressRoute ページをクリックします。 **[監視]** の下に **[メトリック]** が表示されます。

![回線のメトリック](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="metrics-per-peering"></a>ピアリングごとのメトリック

プライベート、パブリック、Microsoft ピアリングのメトリック (ビット/秒) を表示できます。

![ピアリングごとのメトリック](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute ゲートウェイの接続 (ビット/秒)

![ゲートウェイの接続](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute ゲートウェイの接続のアラート

1. アラートを構成するには、**Azure Monitor** に移動し、**[アラート]** をクリックします。

  ![alerts](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. **[+ ターゲットの選択]** をクリックし、ExpressRoute ゲートウェイの接続リソースを選択します。

  ![target]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. アラートの詳細を定義します。

  ![アクション グループ](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)


4. アクション グループを定義して追加します。

  ![アクション グループの追加](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>各ピアリングに基づくアラート

 ![what](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>回線でアクティビティ ログのアラートを構成する

**[アラートの条件]** で、[シグナルの種類] に **[アクティビティ ログ]** を選択し、[シグナル] を選択することができます。

  ![another](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)

## <a name="next-steps"></a>次の手順
* ExpressRoute 接続を構成します。
  
  * [回線の作成と変更](expressroute-howto-circuit-arm.md)
  * [ピアリング構成の作成と変更](expressroute-howto-routing-arm.md)
  * [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-arm.md)
