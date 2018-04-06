---
title: Azure Standard Load Balancer - 診断 | Microsoft Docs
description: Azure Standard Load Balancer の診断に使用可能なメトリックと正常性の情報の使用について説明します
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: Kumud
ms.openlocfilehash: 7d60925381abe617f6e2fac51176b8e30517c3ba
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2018
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Standard Load Balancer のメトリックと正常性の診断

Azure Standard Load Balancer は、リソースに関する次の診断機能を提供します。
* 多次元メトリック: Standard Load Balancer には、パブリックと内部両方のロード バランサー構成用に新しい多次元診断機能が用意されています。 これにより、ロード バランサー リソースの監視、管理、トラブルシューティングを行うことができます。

* リソース正常性: Azure Portal の [ロード バランサー] ページおよび ([Monitor]\(監視\) の) [リソース正常性] ページには、Standard Load Balancer のパブリック ロード バランサー構成のリソース正常性が表示されます。

この記事では、これらの機能の概要、および Standard Load Balancer でこれらの機能を使う方法について説明します。

## <a name = "MultiDimensionalMetrics"></a>多次元メトリック

Azure Load Balancer ではポータルの新しい Azure メトリック (プレビュー) により新しい多次元メトリックが提供されており、Load Balancer リソースのリアルタイム診断分析情報を得るのに役立ちます。 

現在は、さまざまな Standard Load Balancer (LB) 構成について以下のメトリックを利用できます。

| メトリック | リソースの種類 | [説明] | 推奨される集計 |
| --- | --- | --- | --- |
| VIP 可用性 (データ パスの可用性) | パブリック LB | Standard Load Balancer は、リージョン内から Load Balancer フロントエンドを経て、VM をサポートする SDN スタックに至るまでのデータ パスを継続的に学習します。 正常なインスタンスが保持されていれば、測定ではアプリケーションの負荷分散されたトラフィックと同じパスに従います。 顧客が使用しているデータ パスも検証されます。 測定はアプリケーションには見えないので、他の操作と干渉することはありません。| 平均 |
| DIP 可用性 (正常性プローブの状態) |  パブリック LB、内部 LB | Standard Load Balancer では、構成設定に従ってアプリケーション エンドポイントの正常性を監視する、分散型の正常性プローブ サービスが使われます。 このメトリックは、Load Balancer プールの個々のインスタンス エンドポイントの集計ビューまたはエンドポイントごとのフィルター ビューを提供します。  正常性プローブ構成で示されているアプリケーションの正常性を、Load Balancer がどのように表示するのかを確認できます。 |  平均 |
| SYN パケット |  パブリック LB | Standard Load Balancer は、TCP 接続を終了したり、TCP または UDP のパケット フローと対話したりすることはありません。 フローとハンドシェイクは、常にソースと VM インスタンスの間で発生します。 TCP プロトコルのシナリオのトラブルシューティングを適切に行うために、SYN パケット カウンターを使用して TCP 接続試行の数を把握できます。 このメトリックは、受信済みの TCP SYN パケットの数を報告します。| 平均 |
| SNAT 接続 |  パブリック LB |Standard Load Balancer は、パブリック IP アドレス フロントエンドにマスカレードされた送信フローの数を報告します。 SNAT ポートは有限のリソースです。 このメトリックはアプリケーションが送信フローで SNAT にどれくらい依存しているかを示すことができます。  成功した送信 SNAT フローと失敗した送信 SNAT フローのカウンターがレポートされるので、送信フローの正常性について、トラブルシューティングしたり、理解したりするのに役立てることができます。| 平均 |
| バイト カウンター |  パブリック LB、内部 LB | Standard Load Balancer は、フロントエンドごとに処理されたデータ量を報告します。| 平均 |
| パケット カウンター |  パブリック LB、内部 LB | Standard Load Balancer は、フロントエンドごとに処理されたパケット数を報告します。| 平均 |

### <a name="view-load-balancer-metrics-in-the-portal"></a>ポータルで Load Balancer のメトリックを表示する

Azure Portal の [メトリック (プレビュー)] ページでは、Load Balancer のメトリックが公開されます。このページは、特定の Load Balancer リソースの Load Balancer リソース ページと、Azure Monitor のページから表示できます。 

Standard Load Balancer リソースのメトリックを見るには、どちらかの場所で [メトリック (プレビュー)] を表示し、ドロップダウンからメトリックの種類 ([Monitor]\(監視\) ページの場合はロード バランサー リソース) を選び、適切な集約型を設定し、必要に応じて必要なフィルターとグループを構成すると、指定した条件でメトリックの履歴ビューが表示されます。

![Standard Load Balancer のメトリックのプレビュー](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*図 - Load Balancer の DIP 可用性/正常性プローブのメトリック*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>API を使用してプログラムで多次元メトリックを取得する

多次元メトリックの定義と値を取得するための API のガイダンスについては、「Azure 監視 REST API のチュートリアル」の「[メトリック定義の取得 (多次元 API)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api)」をご覧ください。

### <a name = "DiagnosticScenarios"></a>一般的な診断シナリオと推奨されるビュー

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>データ パスが稼働していて Load Balancer VIP に使用可能か?

VIP 可用性メトリックでは、VM が存在するコンピューティング ホストまでの、リージョン内のデータ パスの正常性が示されています。 Azure インフラストラクチャを反映する正常性です。 このメトリックは次の目的に使うことができます。
- サービスの外部可用性を監視します
- サービスが展開されているプラットフォームが正常かどうか、またはゲスト OS やアプリケーション インスタンスが正常かどうかを、詳しく調べて把握します
- イベントがサービスと基盤のデータ プレーンのどちらに関係するかを切り分けます。 これを正常性プローブの状態 ("DIP 可用性") と間違えないで。

Standard Load Balancer リソースの VIP 可用性を取得するには:
- 正しい Load Balancer リソースが選ばれていることを確認します。 
- **[メトリック]** ドロップダウンから **[VIP Availability]\(VIP 可用性\)** を選びます。 
- **[集計]** で **[平均]** を選びます。 
- さらに、必要なフロントエンド IP アドレスまたはフロントエンド ポートのディメンションとして VIP アドレスまたは VIP ポートに対するフィルターを追加し、選んだディメンションでグループ化します。

![VIP のプローブ](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*図 - Load Balancer VIP プローブの詳細*

アクティブな帯域内測定によってメトリックが生成されます。 リージョン内のプローブ サービスは、この測定に対するトラフィックを開始し、ユーザーがパブリック フロントエンドで展開を作成するとすぐにアクティブ化されて、フロントエンドが削除されるまで続行します。 

>[!NOTE]
>現時点では、内部フロントエンドはサポートされていません。 

展開のフロントエンドとルールに一致するパケットが、定期的に生成されます。 パケットは、ソースから、バックエンド プール内の VM が配置されているホストまで、リージョンを移動します。 Load Balancer ンフラストラクチャは、他のすべてのトラフィックの場合と同じ負荷分散操作と変換操作を実行します。 このプローブは負荷分散されるエンドポイントの帯域内です。 バックエンド プール内の正常な VM が存在するコンピューティング ホストにプローブが到達すると、コンピューティング ホストはプローブ サービスへの応答を生成します。 VM はこのトラフィックを認識しません。
VIP 可用性は次の理由で失敗します。
- 展開のバックエンド プールに正常な VM が残っていない。 
- VIP 可用性が失われる原因となるインフラストラクチャ障害が発生した。

[診断目的のために VIP 可用性メトリックと共に正常性プローブの状態](#vipavailabilityandhealthprobes)を使うことができます。

ほとんどのシナリオでは集計として**平均**を使います。

#### <a name="are-the-backend-instances-for-my-vip-responding-to-probes"></a>VIP のバックエンド インスタンスはプローブに応答しているか?

正常性プローブ状態メトリックでは、ユーザーが Load Balancer の正常性プローブを構成するときにユーザーによって構成されたアプリケーションのデプロイの正常性が示されます。 Load Balancer は、正常性プローブの状態を使って、新しいフローの送信先を決めます。 正常性プローブは、Azure インフラストラクチャのアドレスから送信され、VM のゲスト OS 内で認識されます。

Standard Load Balancer リソースの DIP 可用性を取得するには:
- **[DIP Availability]\(DIP 可用性\)** メトリックと **[平均]** 集約型を選びます。 
- 必要な VIP の IP アドレスまたはポート (またはその両方) にフィルターを適用します。

![DIP 可用性](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*図 - Load Balancer の VIP 可用性*

正常性プローブは次の理由で失敗します。
- リッスンしていないポート、応答していないポート、または正しくないプロトコルで応答するポートに対して、正常性プローブを構成した場合。 サービスが DSR (フローティング IP) ルールを使っている場合は、サービスが、フロントエンド IP アドレスで構成されたループバックだけでなく、NIC の IP 構成の IP アドレスでリッスンしていることを確認する必要があります。
- プローブが、ネットワーク セキュリティ グループ、VM のゲスト OS のファイアウォール、またはアプリケーション レイヤーのフィルターによって許可されていない場合。

ほとんどのシナリオでは集計として**平均**を使います。

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>送信接続の統計情報を確認する方法 

SNAT 接続メトリックは、([送信フロー](https://aka.ms/lboutbound)の) 成功と失敗の量を示します。

失敗した接続の量が 0 より大きい場合は、SNAT ポートが不足していることを示します。 さらに詳しく調査し、このようなエラーの原因を特定する必要があります。 SNAT ポートの不足は、[送信フロー](https://aka.ms/lboutbound)確立の失敗として示されます。 シナリオ、動作メカニズム、および SNAT ポートの不足を軽減する方法または回避する設計の方法を理解するには、送信接続に関する記事をご覧ください。 

SNAT 接続の統計情報を取得するには:
- **[SNAT Connections]\(SNAT 接続\)** メトリックの種類と **[合計]** 集約型を選びます。 
- SNAT 接続の成功と失敗のカウントを異なる行に表示するには、**[接続の状態]** でグループ化します。 

![SNAT 接続](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*図 - Load Balancer の SNAT 接続の数*


#### <a name="how-do-i-check-inbound--outbound-connection-attempts-for-my-service"></a>サービスに対する受信/送信接続の試行を確認する方法

SYN パケット メトリックは、特定のフロントエンドに関連して到着した、または送信された ([送信フロー](https://aka.ms/lboutbound)の場合) TCP SYN パケットの量を示します。 このメトリックを使うと、サービスへの TCP 接続の試行を把握できます。
ほとんどのシナリオでは、集計として合計を使います。

![SYN 接続](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*図 - Load Balancer の SYN の数*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>ネットワーク帯域幅の消費量を確認する方法 

バイト/パケット カウンター メトリックは、フロントエンドごとにサービスによって送信または受信されたバイトおよびパケットの量を示します。
ほとんどのシナリオでは、集計として**合計**を使います。

バイト数またはパケット数の統計を取得するには
- **[Bytes Count]\(バイト数\)** および **[Packet Count]\(パケット数\)** メトリックの種類と、**[平均]** 集約型を選びます。 
- 問題になっている特定のフロントエンド IP、フロントエンド ポート、バックエンド IP、またはバックエンド ポートでフィルターを適用します。 
- または、フィルターを適用しないで Load Balancer リソース全体の統計情報を取得します。


異なる構成のメトリックに対するビューの例 -

![バイト数](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*図 - Load Balancer のバイト数*

#### <a name = "vipavailabilityandhealthprobes"></a>Load Balancer の展開を診断する方法

VIP 可用性メトリックと正常性プローブ メトリックを 1 つのグラフに組み合わせると、問題を探して解決する場所を特定することができます。 Azure が正常に動作している保証が得られ、それにより、構成またはアプリケーションが根本原因であることを確定できます。

正常性プローブ メトリックを使うと、ユーザーが指定した構成に従って、Azure が展開の正常性を表示する方法を理解できます。 正常性プローブを確認することは、常に、原因を監視または特定するのに適した第一歩です。

その後さらに、VIP 可用性メトリックを使って、特定の展開に関与する基盤データ プレーンの正常性を Azure が表示する方法についての分析情報を得ることができます。 次の例に示すように、両方のメトリックを組み合わせると、障害の可能性がある場所を分離できます。

![VIP 診断](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*図 - DIP 可用性メトリックと VIP 可用性メトリックの組み合わせ*

このグラフには次の情報が示されています。
- インフラストラクチャ自体は正常であり、VM をホストしているインフラストラクチャには到達可能で、複数の VM がバックエンドに配置されていました。 このことは、VIP 可用性の青いトレース (100% と表示) によって示されています。 
- 一方、正常性プローブの状態 (DIP 可用性) は、オレンジ色のトレースで示されているように、グラフの先頭では 0% です。 赤い円で囲んだ領域は正常性プローブ (DIP 可用性) が正常になった場所を示し、この時点で、ユーザーの展開は新しいフローを受け付けることができるようになりました。

このグラフを見ることで、ユーザーは、他の問題が発生していたかどうかを推測したりサポートに問い合わせたりすることなく、独力で展開のトラブルシューティングを行うことができます。 ユーザーのサービスは、不正な構成またはアプリケーションの障害によって正常性プローブが失敗したために利用できませんでした。

### <a name = "Limitations"></a>制限事項

- 現在、VIP 可用性はパブリック フロントエンドに対してのみ使用できます。

## <a name = "ResourceHealth"></a>リソースの正常性状態

Standard Load Balancer リソースの正常性状態は、**[Monitor]\(監視\) > [サービスの正常性]** の既存の **[リソース正常性]** によって表示されます。

>[!NOTE]
>Resource Health のリソース正常性は、現在、Standard Load Balancer のパブリック構成に対してのみ使用できます。 内部 Load Balancer リソースまたは Load Balancer リソースの Basic SKU については、リソース正常性は表示されません。

パブリック Standard Load Balancer リソースの正常性を表示するには:
 - **[Monitor]\(監視\) > [サービスの正常性]** を参照します。

  ![[Monitor]\(監視\) ページ](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *図 - Azure Monitor のサービス正常性*

 - **[リソース正常性]** を選び、正しい **[サブスクリプション ID]** が選ばれ、**[リソースの種類] = [ロード バランサー]** に設定されていることを確認します。

  ![リソースの正常性状態](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *図 - 正常性を表示するリソースの選択*

 - 一覧から [ロード バランサー] リソースをクリックして、正常性状態の履歴を表示します。

    ![Load Balancer の正常性状態](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *図 - Load Balancer リソースの正常性の表示*
 
次の表では、さまざまなリソース正常性の状態とその説明を示します。 

| リソースの正常性状態 | [説明] |
| --- | --- |
| 使用可能 | パブリック Standard Load Balancer リソースは正常であり、使用可能です |
| 使用不可 | パブリック Standard Load Balancer リソースは正常ではありません。 Azure Monitor の [メトリック] で正常性を診断してください (*これは、リソースがパブリック Standard Load Balancer リソースではないことを意味している可能性もあります*)。 |
| Unknown | パブリック Standard Load Balancer のリソース正常性はまだ更新されていません (*これは、リソースがパブリック Standard Load Balancer リソースではないことを意味している可能性もあります*)。  |

## <a name="next-steps"></a>次の手順

- [Standard Load Balancer](load-balancer-standard-overview.md) の詳細を確認する
- [ロード バランサーの送信接続](https://aka.ms/lboutbound)の詳細を確認する


