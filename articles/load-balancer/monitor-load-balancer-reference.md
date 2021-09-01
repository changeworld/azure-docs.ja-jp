---
title: Load Balancer データの監視のリファレンス
titleSuffix: Azure Load Balancer
description: Load Balancer を監視する際に必要となる重要なリファレンス資料
author: asudbring
ms.topic: reference
ms.author: allensu
ms.service: load-balancer
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: ac5a7394b13ac34ad9d398458627765316369de9
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296511"
---
# <a name="monitoring-load-balancer-data-reference"></a>Load Balancer データの監視のリファレンス

Load Balancer の監視データの収集と分析の詳細については、[Load Balancer の監視](monitor-load-balancer.md)に関するページをご覧ください。

## <a name="metrics"></a>メトリック

### <a name="load-balancer-metrics"></a>Load Balancer のメトリック 

| メトリック | リソースの種類 | 説明 | 推奨される集計 |
| ------ | ------------- | ----------- | ----------------------- |
| データ パスの可用性 | パブリックおよび内部ロード バランサー |  Standard Load Balancer は、リージョン内からロード バランサー フロントエンドを経て、VM をサポートする SDN スタックに至るまでのデータ パスを継続的に学習します。 正常なインスタンスが保持されていれば、測定ではアプリケーションの負荷分散されたトラフィックと同じパスに従います。 顧客が使用しているデータ パスも検証されます。 測定はアプリケーションには見えないので、他の操作と干渉することはありません。 | Average |
| 正常性プローブの状態 | パブリックおよび内部ロード バランサー | Standard Load Balancer では、構成設定に従ってアプリケーション エンドポイントの正常性を監視する、分散型の正常性プローブ サービスが使われます。 このメトリックは、ロード バランサー プールのインスタンス エンドポイントの集計ビューまたはエンドポイントごとのフィルター ビューを提供します。 正常性プローブ構成で示されているアプリケーションの正常性を、Load Balancer がどのように表示するのかを確認できます。 | Average |
| SYN (同期) 数 | パブリックおよび内部ロード バランサー | Standard Load Balancer では、構成設定に従ってアプリケーション エンドポイントの正常性を監視する、分散型の正常性プローブ サービスが使われます。 このメトリックは、ロード バランサー プールのインスタンス エンドポイントの集計ビューまたはエンドポイントごとのフィルター ビューを提供します。 正常性プローブ構成で示されているアプリケーションの正常性を、Load Balancer がどのように表示するのかを確認できます。 | Average |
| SNAT 接続数 | パブリック ロード バランサー | Standard Load Balancer は、パブリック IP アドレス フロントエンドにマスカレードされた送信フローの数を報告します。 送信元ネットワーク アドレス変換 (SNAT) ポートは、有限のリソースです。 このメトリックはアプリケーションが送信フローで SNAT にどれくらい依存しているかを示すことができます。 成功した送信 SNAT フローと失敗した送信 SNAT フローのカウンターがレポートされるので、送信フローの正常性について、トラブルシューティングしたり、理解したりするのに役立てることができます。 | SUM |
| 割り当てられる SNAT ポート | パブリック ロード バランサー | Standard Load Balancer では、バックエンド インスタンスごとに割り当てられる SNAT ポートの数が報告されます。 | Average |
| 使用された SNAT ポート | パブリック ロード バランサー | Standard Load Balancer からは、バックエンド インスタンスごとに活用されている SNAT ポートの数が報告されます | Average |
| バイト数 | パブリックおよび内部ロード バランサー | Standard Load Balancer は、フロントエンドごとに処理されたデータ量を報告します。 バックエンド インスタンス間でバイト数が均等に配布されていないことがわかります。 Azure の Load Balancer アルゴリズムはフローに基づいているため、これは予期されていることです。 | SUM |
| パケット数 | パブリックおよび内部ロード バランサー | Standard Load Balancer は、フロントエンドごとに処理されたパケット数を報告します。 | SUM |

詳細については、[Azure Monitor でサポートされている Load Balancer 用のすべてのプラットフォーム メトリック](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkloadbalancers)の一覧をご覧ください。

## <a name="metric-dimensions"></a>メトリック ディメンション

メトリック ディメンションの詳細については、「[多次元メトリック](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)」を参照してください。

Load Balancer では、次のディメンションがメトリックに関連付けられています。

| ディメンション名 | 説明 |
| -------------- | ----------- |
| フロントエンド IP | 関連する負荷分散規則のフロントエンド IP アドレス |
| フロントエンド ポート | 関連する負荷分散規則のフロントエンド ポート | 
| バックエンド IP | 関連する負荷分散規則のバックエンド IP アドレス |
| バックエンド ポート | 関連する負荷分散規則のバックエンド ポート |
| プロトコル タイプ | 関連する負荷分散規則のプロトコル。TCP または UDP を指定できます |
| Direction | トラフィックが流れる方向。 受信または送信を指定できます。 | 
| 接続状態 | SNAT 接続の状態。保留中、成功、または失敗を指定できます | 

## <a name="resource-logs"></a>リソース ログ

Azure Load Balancer では現在、リソース ログはサポートされていません

## <a name="azure-monitor-logs-tables"></a>Azure Monitor ログ テーブル
### <a name="diagnostics-tables"></a>診断テーブル

Azure Load Balancer では現在、診断テーブルはサポートされていません。
## <a name="activity-log"></a>アクティビティ ログ

次の表は、アクティビティ ログで作成される可能性がある Load Balancer に関連する操作を示しています。

| 操作 | 説明 |
| --- | --- |
| Microsoft.Network/loadBalancers/read | ロード バランサー定義を取得します。 |
| Microsoft.Network/loadBalancers/write | ロード バランサーを作成するか、既存のロード バランサーを更新します。 |
| Microsoft.Network/loadBalancers/delete | ロード バランサーを削除します。 |
| Microsoft.Network/loadBalancers/backendAddressPools/queryInboundNatRulePortMapping/action | インバウンド NAT 規則のポート マッピングをクエリします。 |
| Microsoft.Network/loadBalancers/backendAddressPools/read | ロード バランサーのバックエンド アドレス プールの定義を取得します。 |
| Microsoft.Network/loadBalancers/backendAddressPools/write | ロード バランサーのバックエンド アドレス プールを作成するか、既存のロード バランサーのバックエンド アドレス プールを更新します |
| Microsoft.Network/loadBalancers/backendAddressPools/delete | ロード バランサーのバックエンド アドレス プールを削除します |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | ロード バランサーのバックエンド アドレス プールを接続します。 警告不可能です。 |
| Microsoft.Network/loadBalancers/backendAddressPools/backendPoolAddresses/read | ロード バランサーのバックエンド アドレス プールのバックエンド アドレスを一覧表示します |
| Microsoft.Network/loadBalancers/frontendIPConfigurations/read | ロード バランサーのフロントエンド IP 構成定義を取得します。 |
| Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | ロード バランサーのフロントエンド IP 構成を結合します。 警告不可能です。 |
| Microsoft.Network/loadBalancers/inboundNatPools/read | ロード バランサーの受信 NAT プールの定義を取得します。 |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | ロード バランサーの受信 NAT プールを接続します。 警告不可能です。 |
| Microsoft.Network/loadBalancers/inboundNatRules/read | ロード バランサーの受信 NAT 規則の定義を取得します。 |
| Microsoft.Network/loadBalancers/inboundNatRules/write | ロード バランサーの受信 NAT 規則を作成するか、既存の受信 NAT 規則を更新します。 |
| Microsoft.Network/loadBalancers/inboundNatRules/delete | ロード バランサーの受信 NAT 規則を削除します。 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | ロード バランサーのインバウンド NAT 規則を接続します。 警告不可能です。 |
| Microsoft.Network/loadBalancers/loadBalancingRules/read | ロード バランサーの負荷分散規則の定義を取得します。 |
| Microsoft.Network/loadBalancers/networkInterfaces/read | ロード バランサーの下のすべてのネットワーク インターフェイスへの参照を取得します。 |
| Microsoft.Network/loadBalancers/outboundRules/read | ロード バランサーの送信規則の定義を取得します |
| Microsoft.Network/loadBalancers/probes/read | ロード バランサー プローブを取得します。 |
| Microsoft.Network/loadBalancers/probes/join/action | ロード バランサーのプローブの使用を許可します。 たとえば、このアクセス許可では、仮想マシン スケール セットの healthProbe プロパティでプローブを参照できます。 警告不可能です。 |
| Microsoft.Network/loadBalancers/virtualMachines/read | ロード バランサーの下のすべての仮想マシンへの参照を取得します。 |

アクティビティ ログ エントリのスキーマの詳細については、[アクティビティ ログのスキーマ](../azure-monitor/essentials/activity-log-schema.md)に関する記事を参照してください。 

## <a name="see-also"></a>参照

- Azure Load Balancer の監視については、[Azure Load Balancer の監視](./monitor-load-balancer.md)に関するページをご覧ください。