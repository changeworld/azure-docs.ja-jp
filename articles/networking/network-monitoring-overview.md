---
title: "Log Analytics でのネットワーク監視について | Microsoft Docs"
description: "クラウド、オンプレミス、およびハイブリッド環境でネットワークを管理するための、NPM などのネットワーク監視ソリューションの概要を説明します。"
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 6d93821b59e1f69a48c3d5eeda96dad2edddb188
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="network-monitoring-solutions"></a>ネットワーク監視ソリューション 

Azure では、ネットワーク資産を監視するためのソリューションのホストが提供されます。 Azure には、ネットワークの接続性と ExpressRoute 回線の正常性を監視し、クラウド内のネットワーク トラフィックを分析するための、ソリューションとユーティリティがあります。

## <a name="network-performance-monitor-npm"></a>ネットワーク パフォーマンス モニター (NPM)

Network Performance Monitor (NPM) は機能のスイートであり、各機能は、ネットワークの正常性とアプリケーションへのネットワーク接続性を監視し、ネットワークのパフォーマンスに関する洞察を提供します。 NPM はクラウド ベースであり、次のものの間の接続性を監視するハイブリッド ネットワーク監視ソリューションを提供します。
 
* クラウドの展開とオンプレミスの場所
* 複数のデータ センターとブランチ オフィス
* ミッション クリティカルな多層アプリケーション/マイクロサービス
* ユーザーの場所と Web ベースのアプリケーション (HTTP/HTTPS) 

## <a name="performance-monitor"></a>パフォーマンスの監視

Performance Monitor は NPM の一部であり、クラウド、ハイブリッド、オンプレミスの環境のネットワーク監視を行います。 リモート ブランチとフィールド オフィス、店舗の場所、データ センター、クラウドの間のネットワーク接続性を監視できます。 ユーザーから不満があがる前に、ネットワークの問題を検出できます。 主な利点は次のとおりです。

* 各種サブネット間の損失と待ち時間を監視し、アラートを設定する
* ネットワーク上のすべてのパス (冗長パスを含む) を監視する
* 再現が難しい、ネットワークに関する一過性の問題と特定時点の問題をトラブルシューティングする
* パフォーマンス低下の原因となっている特定のセグメントをネットワーク上で見つけ出す
* SNMP を必要とせずに、ネットワークの正常性を監視する

![NPM のトポロジ マップ](./media/network-monitoring-overview/npm-topology-map.png) 

詳しくは、次の記事をご覧ください。

* [Log Analytics で Network Performance Monitor ソリューションを構成する](../log-analytics/log-analytics-network-performance-monitor.md) 
* [ユース ケース](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  製品の更新プログラム: [2017 年 2 月](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/)、[2017 年 8 月](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>ExpressRoute モニター

ExpressRoute 用の NPM は、プライベート ピアリング接続の包括的な ExpressRoute 監視を提供します。 ExpressRoute 経由のブランチ オフィスと Azure 間の E2E 接続性とパフォーマンスを監視できます。 主な機能は次のとおりです。

* サブスクリプションに関連付けられている ER 回線の自動検出
* オンプレミスからクラウド アプリケーションへのネットワーク トポロジの検出
* 容量計画、使用率分析
* プライマリ パスとセカンダリ パス両方の監視とアラート
* VNet への接続性の低下の検出

詳細については、次の記事を参照してください。

* [ExpressRoute に使用する Network Performance Monitor の構成](../expressroute/how-to-npm.md)
* [ブログの投稿](https://aka.ms/NPMExRmonitorGA)

## <a name="service-endpoint-monitor"></a>サービス エンドポイント モニター

サービス エンドポイントの監視では、アプリケーションの到達可能性をテストし、オンプレミス、通信事業者ネットワーク、クラウド/プライベート データ センターの間のパフォーマンス ボトルネックを検出できます。

* アプリケーションへのエンド ツー エンドのネットワーク接続性を監視する
* アプリケーションの配信とネットワークのパフォーマンスを関連付け、ユーザーとアプリケーションの間のパスに沿った低下の正確な場所を検出する
* 世界中の複数のユーザーの場所からアプリケーションへの到達可能性をテストする
* 基幹業務アプリケーションと SaaS アプリケーションのネットワーク待機時間とパケット損失を特定する
* アプリケーション パフォーマンス低下の原因になっている可能性があるネットワーク上のホット スポットを特定する
* Microsoft Office 365、Dynamics 365、Skype for Business、その他の Microsoft サービス用の組み込みテストを使って、Office 365 アプリケーションへの到達可能性を監視する

詳細については、次の記事を参照してください。

* [サービス エンドポイントを監視するための Network Performance Monitor の構成](https://aka.ms/applicationconnectivitymonitorguide)
* [ブログの投稿](https://aka.ms/svcendptmonitor)

## <a name="next-steps"></a>次の手順

* [Network Performance Monitor の構成](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor)
* [ExpressRoute に使用する Network Performance Monitor の構成](../expressroute/how-to-npm.md)
