---
title: "Azure Log Analytics のネットワーク パフォーマンス モニター ソリューション | Microsoft Docs"
description: "Network Performance Monitor のサービス エンドポイント マネージャー機能では、TCP ポートが開いている任意のエンドポイントへのネットワーク接続を監視することができます。"
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: ba19a4fc24668bff27c961b5b415f840d1132a34
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2018
---
# <a name="service-endpoint-monitor"></a>サービス エンドポイント モニター

[Network Performance Monitor](log-analytics-network-performance-monitor.md) のサービス エンドポイント モニター機能では、TCP ポートが開いている任意のエンドポイントへのネットワーク接続を監視することができます。 対象となるエンドポイントには、Web サイト、SaaS アプリケーション、PaaS アプリケーション、および SQL データベースが含まれます。 

**サービス エンドポイント モニター**では、次の機能を実行できます。 

- 複数のブランチ オフィス/場所から、アプリケーションおよびネットワーク サービス (Office 365、Dynamics CRM、内部の基幹業務アプリケーション、SQL データベースなど) へのネットワーク接続を監視する 
- Office 365 および Dynamics365 エンドポイントへのネットワーク接続を監視する組み込みテスト 
- エンドポイントへの接続時に発生した応答時間、ネットワーク待機時間、パケット損失を確認する 
- アプリケーション パフォーマンスの低下がネットワークに起因するものなのか、それともアプリケーション プロバイダー側の問題によるものなのかを確認する 
- トポロジ マップ上の各ホップの影響を受けている待機時間を表示することによって、アプリケーション パフォーマンスの低下の原因となっている可能性があるネットワーク上のホット スポットを特定する。 


![サービス エンドポイント モニター](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>構成 
Network Performance Monitor の構成を開くには、[Network Performance Monitor ソリューション](log-analytics-network-performance-monitor.md)を開き、**[構成]** ボタンをクリックします。

![Network Performance Monitor の構成](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-oms-agents-for-the-monitoring"></a>監視のための OMS エージェントを構成する。  
監視に使うノードで次のファイアウォール規則を有効にして、ノードからサービス エンドポイントまでのトポロジを探索できるようにします。 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action 
```

### <a name="create-service-endpoint-monitor-tests"></a>サービス エンドポイント モニター テストの作成 

サービス エンドポイントへのネットワーク接続を監視するためのテストを作成します 

1. **[サービス エンドポイント モニター]** タブをクリックします。
2. **[テストの追加]** をクリックし、テストの名前と説明を入力します。 
3. テストの種類を選択します。<br>HTTP/S 要求に応答するサービス (outlook.office365.com、bing.com など) への接続を監視する場合は、**[Web テスト]** を選択します。<br>TCP 要求に応答し、HTTP/S 要求には応答しないサービス (SQL サーバー、FTP サーバー、SSH ポートなど) への接続を監視する場合は、**[ネットワーク テスト]** を選択します。 
4. ネットワーク測定 (ネットワーク待機時間、パケット損失、トポロジ探索) を実行しない場合は、テキスト ボックスをオフにします。 この機能のメリットを最大限に得るには、オンにしておくことをお勧めします。 
5. ネットワーク接続を監視する、ターゲット URL/FQDN/IP アドレスを入力します。  
6. ターゲット サービスのポート番号を入力します。 
7. テストを実行する頻度を入力します。 
8. サービスへのネットワーク接続を監視するノードを選択します。 

    >[!NOTE]
    > Windows サーバー ベースのノードの場合、この機能では、TCP ベースの要求を使用してネットワーク測定が実行されます。 Windows クライアント ベースのノードの場合、この機能では、ICMP ベースの要求を使用してネットワーク測定が実行されます。 場合によっては、ターゲット アプリケーションによって、受信された ICMP ベース要求がブロックされることがあります。これは、ノードが Windows クライアント ベースである場合、ネットワーク測定を実行できないためです。 そのため、このような場合には Windows サーバー ベースのノードを使用することをお勧めします。 

9. 選択した項目の正常性イベントを作成したくない場合は、**[Enable health monitoring on the targets covered by this test]\(このテストの対象になるターゲットの稼働状況の監視を有効にする\)** をオフにします。 
10. 監視条件を選択します。 しきい値を入力して、正常性イベントの生成に関するカスタムしきい値を設定できます。 選択したネットワーク ペア/サブネットワーク ペアに対して選択したしきい値を条件の値が上回ると、正常性イベントが生成されます。 
11. **[保存]** をクリックして構成を保存します。 

 ![サービス エンドポイント モニターの構成](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>チュートリアル 

ネットワーク パフォーマンス監視ダッシュボード ビューに移動し、**[サービス エンドポイント モニター]** ページを見て、作成した各テストの正常性の概要を確認します。  

![[サービス エンドポイント モニター] ページ](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

タイルをクリックしてドリル ダウンし、**[テスト]** ページでテストの詳細を確認します。 LHS テーブルでは、すべてのテストについて、特定の時点での正常性と、サービス応答時間、ネットワーク待機時間、およびパケット損失の値を確認できます。 [ネットワーク状態の記録機能] コントロールを使用すると、過去の別の時点のスナップショットを表示できます。 調査するテーブル内のテストをクリックすると、 [RHS] ウィンドウのグラフから、損失、待機時間、および応答時間の値に関する過去の傾向を確認できます。 [テストの詳細] リンクをクリックすると、各ノードからのパフォーマンスを確認できます。 

![サービス エンドポイント モニターのテスト](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

**[テスト ノード]** ビューでは、各ノードからのネットワーク接続を確認できます。 パフォーマンスが低下しているノードをクリックします。  これは、アプリケーションの実行速度が低下しているノードです。 

アプリケーションの応答時間とネットワーク待機時間の相関関係を観察して、アプリケーション パフォーマンスの低下がネットワークに起因するものなのか、それともアプリケーション プロバイダー側の問題によるものなのかを確認します。 

**アプリケーションの問題:** 応答時間の急増が見られる一方、ネットワーク待機時間は一貫しているという場合、ネットワークは正常に動作しており、問題の原因はアプリケーション側にあると考えられます。  

![サービス エンドポイント モニターのアプリケーションの問題](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

**ネットワークの問題:** 応答時間の急増に伴ってネットワーク待機時間も急増している場合、応答時間の増加は、ネットワーク待機時間の増加に起因していると考えられます。  

![サービス エンドポイント モニターのネットワークの問題](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

問題の原因がネットワークであると判断した場合は、**[トポロジ]** ビュー リンクをクリックすると、トポロジ マップ上で問題のホップを確認できます。 たとえば次の図では、ノードとアプリケーション エンドポイント間の合計待機時間である 105 ミリ秒のうち、96 ミリ秒は赤で囲まれたホップに起因していることがわかります。 問題のホップを特定したら、是正措置を実行できます。  

![サービス エンドポイント モニターのテスト](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>診断 

異常が確認された場合は、次の手順に従います。

サービス応答時間、ネットワーク損失、および待機時間が NA と表示される場合は、次の 1 つ以上が原因として考えられます。
- アプリケーションがダウンしている。
- サービスへのネットワーク接続のチェックに使用されているノードがダウンしている。
- テスト構成で入力したターゲットが正しくない。
- ノードにネットワーク接続がない。

有効なサービス応答時間が表示されているものの、ネットワーク損失と待機時間が NA と表示される場合は、次の 1 つ以上が原因として考えられます。
- サービスへのネットワーク接続のチェックに使用されたノードが Windows クライアント コンピューターである場合は、ターゲット サービスが ICMP 要求をブロックしているか、またはネットワーク ファイアウォールがノードからの ICMP 要求をブロックしている。
- テスト構成で **[ネットワークの測定を実行します]** チェック ボックスをオフにした。 

サービス応答時間が NA で、ネットワーク損失と待機時間が有効な場合は、ターゲット サービスが Web アプリケーションではないことが考えられます。 テスト構成を編集し、テストの種類として、Web テストではなくネットワーク テストを選択してください。 

アプリケーションの実行速度が遅い場合は、アプリケーション パフォーマンスの低下がネットワークに起因するものなのか、それともアプリケーション プロバイダー側の問題によるものなのかを確認してください。


## <a name="next-steps"></a>次の手順
* 詳細なネットワーク パフォーマンスのデータ レコードを表示するために、[ログを検索](log-analytics-log-searches.md)します。
