---
title: Log Analytics でのデータ使用状況の分析 | Microsoft Docs
description: Log Analytics に送信されたデータの量を評価したり、予想外の増加を引き起こした原因を特定したりするには、Log Analytics の [使用量と推定コスト] ダッシュボードを使用します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: d02c3ad3e1ca2812049608cad2eacced3686dad3
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128566"
---
# <a name="analyze-data-usage-in-log-analytics"></a>Log Analytics でのデータ使用状況の分析
Log Analytics には、収集されたデータの量、データの送信元、送信されたさまざまな種類のデータに関する情報が含まれます。  データ使用状況を確認して分析するには、**[ログ分析の利用状況]** ダッシュボードを使用します。 ダッシュボードには、各ソリューションによって収集されたデータの量と、お使いのコンピューターが送信しているデータの量が表示されます。

## <a name="understand-the-usage-dashboard"></a>利用状況ダッシュボードについて
**[Log Analytics Usage (Log Analytics の使用量)]** ダッシュボードには、次の情報が表示されます。

- データ ボリューム
    - (現在の時間範囲に基づく) 経時のデータ ボリューム
    - ソリューション別のデータ ボリューム
    - コンピューターに関連付けられていないデータ
- [Computers (コンピューター)]
    - データを送信しているコンピューター
    - 過去 24 時間のデータがないコンピューター
- プラン
    - Insight と Analytics のノード
    - Automation と Control のノード
    - セキュリティ ノード  
- [パフォーマンス]
    - データの収集とインデックス作成に要した時間  
- クエリのリスト

![使用量とコストのダッシュボード](./media/log-analytics-manage-cost-storage/usage-estimated-cost-dashboard-01.png)<br>
)

### <a name="to-work-with-usage-data"></a>使用量データを扱うには
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. Azure Portal で、**[すべてのサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。<br><br> ![Azure Portal](./media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
3. Log Analytics ワークスペースの一覧からワークスペースを選択します。
4. 左側のウィンドウにある一覧から **[使用量と推定コスト]** を選択します。
5. **[使用量と推定コスト]** ダッシュボードでは、**[時間: 過去 24 時間]** を選択して時間の範囲を変更したり、期間を変更したりすることができます。<br><br> ![期間](./media/log-analytics-usage/usage-time-filter-01.png)<br><br>
6. 確認したい領域が示されている使用量カテゴリ ブレードを確認します。 ブレードを選択し、その項目をクリックして、[[ログ検索]](log-analytics-log-searches.md) に詳細を表示します。<br><br> ![データ使用量 KPI の例](media/log-analytics-usage/data-volume-kpi-01.png)<br><br>
7. [ログ検索] ダッシュボードで、検索によって返された結果を確認します。<br><br> ![使用量のログの検索の例](./media/log-analytics-usage/usage-log-search-01.png)

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>収集したデータの量が予測よりも多い場合のアラートを作成する
このセクションでは、次の場合のアラートを作成する方法について説明します。
- データ量が指定された量を超えた。
- データ量が指定された量を超えると予測された。

Azure アラートでは、検索クエリを使用する[ログ アラート](../monitoring-and-diagnostics/monitor-alerts-unified-log.md)をサポートしています。 

次のクエリでは、過去 24 時間で 100 GB を超えるデータが収集された場合に結果が返されます。

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

次のクエリでは、簡単な数式を使用して、1 日に 100 GB を超えるデータが送信される場合を予測します。 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

別のデータ量でアラートを生成するには、クエリの 100 をアラートを生成する GB 数に変更します。

[新しいログ アラートの作成](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)に関するページで説明されている手順を使用して、収集したデータの量が予測を超えた場合に通知されるようにします。

最初のクエリ (24 時間でデータが 100 GB を超えた場合) のアラートを作成するには、次のように設定します。  

- **[アラートの条件を定義します]** では、リソース ターゲットとして Log Analytics ワークスペースを指定します。
- **[アラートの条件]** では、以下を指定します。
   - **[シグナル名]** では、**[カスタム ログ検索]** を選択します。
   - **[検索クエリ]**: `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - **[アラート ロジック]** は "*結果の数*" **に基づき、****[条件]** は**しきい値**の *0* "*より大きい*" です
   - **[期間]** を *1440* 分にします。使用状況データが更新されるのは 1 時間に 1 回のみのため、**[アラートの頻度]** を *60* 分ごとにします。
- **[アラートの詳細を定義します]** では、以下を指定します。
   - **[名前]**: "*24 時間でデータ量が 100 GB を超えた場合*"
   - **[重大度]**: *[警告]*

ログ アラートが条件に一致するときに通知されるように、既存の[アクション グループ](../monitoring-and-diagnostics/monitoring-action-groups.md)を指定するか、アクション グループを新たに作成します。

2 つ目のクエリ (24 時間でデータが 100 GB を超えると予測される場合) のアラートを作成するには、次のように設定します。

- **[アラートの条件を定義します]** では、リソース ターゲットとして Log Analytics ワークスペースを指定します。
- **[アラートの条件]** では、以下を指定します。
   - **[シグナル名]** では、**[カスタム ログ検索]** を選択します。
   - **[検索クエリ]**: `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - **[アラート ロジック]** は "*結果の数*" **に基づき、****[条件]** は**しきい値**の *0* "*より大きい*" です
   - **[期間]** を *180* 分にします。使用状況データが更新されるのは 1 時間に 1 回のみのため、**[アラートの頻度]** を *60* 分ごとにします。
- **[アラートの詳細を定義します]** では、以下を指定します。
   - **[名前]**: "*24 時間でデータ量が 100 GB を超えると予想される場合*"
   - **[重大度]**: *[警告]*

ログ アラートが条件に一致するときに通知されるように、既存の[アクション グループ](../monitoring-and-diagnostics/monitoring-action-groups.md)を指定するか、アクション グループを新たに作成します。

アラートを受け取ったら、次のセクションの手順を使用して、使用量が予想よりも多い理由のトラブルシューティングを行います。

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>使用量が予想よりも多い理由のトラブルシューティング
利用状況ダッシュボードは、使用量 (そのため、コスト) が予想を超えている理由を特定するのに役立ちます。

使用量が多くなる原因は、次のいずれかまたは両方です。
- 予想よりも多くのデータが Log Analytics に送信されている
- 予想よりも多くのノードが Log Analytics にデータを送信している

### <a name="check-if-there-is-more-data-than-expected"></a>データ量が予想を超えているかどうかの確認 
利用状況ページには、大量のデータが収集されている原因を特定するのに役立つ 2 つの重要なセクションがあります。

*[データ ボリュームの経時変化]* グラフには、送信されたデータの総量と最も多くのデータを送信しているコンピューターが示されます。 上部のグラフでは、全体的なデータ使用量が増加しているか、一定しているか、減少しているかを確認できます。 コンピューターの一覧には、大量のデータを送信している上位 10 台のコンピューターが表示されます。

*[ソリューションごとのデータ ボリューム]* グラフには、各ソリューションによって送信されたデータの量と、最も多くのデータを送信しているソリューションが示されます。 上部のグラフには、各ソリューションによって送信されたデータの総量の経時変化が示されています。 この情報により、特定のソリューションが送信しているデータの量が時間の経過に伴って増加しているのか、だいたい同じか、減少しているかを特定できます。 ソリューションの一覧には、大量のデータを送信している上位 10 個のソリューションが表示されます。 

これらの 2 つのグラフには、すべてのデータが表示されます。 データには、課金対象と無料のものがあります。 課金対象のデータのみに絞り込むには、検索ページのクエリを `IsBillable=true` を含めるように変更します。  

![データ量のグラフ](./media/log-analytics-usage/log-analytics-usage-data-volume.png)

*[データ ボリュームの経時変化]* グラフを確認します。 特定のコンピューターでデータを最も多く送信しているソリューションとデータの種類を確認するには、コンピューターの名前をクリックします。 一覧内の最初のコンピューターの名前をクリックします。

次のスクリーンショットでは、*Log Management / Perf* データの種類がそのコンピューターでデータを最も多く送信しています。<br><br> ![コンピューターのデータ量](./media/log-analytics-usage/log-analytics-usage-data-volume-computer.png)<br><br>

次に、"*利用状況*" ダッシュボードに戻り、*[ソリューションごとのデータ ボリューム]* グラフを確認します。 特定のソリューションでデータを最も多く送信しているコンピューターを確認するには、一覧内のソリューションの名前をクリックします。 一覧内の最初のソリューションの名前をクリックします。 

次のスクリーンショットでは、*mycon* コンピューターが Log Management ソリューションで最も多くのデータを送信していることがわかります。<br><br> ![ソリューションのデータ量](./media/log-analytics-usage/log-analytics-usage-data-volume-solution.png)<br><br>

必要に応じて、追加の分析を実行して、ソリューションまたはデータの種類内でデータ量が多いものを特定します。 クエリの例を次に示します。

+ **Security** ソリューション
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **Log Management** ソリューション
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf** データの種類
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Event** データの種類
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog** データの種類
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics** データ型
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

収集されるログの量を削減するには、次の手順を実行します。

| データ量の多いソース | データ量を削減する方法 |
| -------------------------- | ------------------------- |
| セキュリティ イベント            | [一般的または最小限のセキュリティ イベント](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/)を選択します。 <br> 必要なイベントのみを収集するようにセキュリティ監査ポリシーを変更します。 特に、次のイベントを収集する必要性を検討します。 <br> - [フィルタリング プラットフォームの監査](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [レジストリの監査](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [ファイル システムの監査](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [カーネル オブジェクトの監査](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [ハンドル操作の監査](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - リムーバブル記憶域の監査 |
| パフォーマンス カウンター       | [パフォーマンス カウンターの構成](log-analytics-data-sources-performance-counters.md)を次のように変更します。 <br> - 収集の頻度を減らす <br> - パフォーマンス カウンターの数を減らす |
| イベント ログ                 | [イベント ログの構成](log-analytics-data-sources-windows-events.md)を次のように変更します。 <br> - 収集対象のイベント ログの数を減らす <br> - 必要なイベント レベルのみを収集する たとえば、"*情報*" レベルのイベントを収集しないようにします。 |
| syslog                     | [syslog の構成](log-analytics-data-sources-syslog.md)を次のように変更します。 <br> - 収集対象の施設の数を減らす <br> - 必要なイベント レベルのみを収集する たとえば、"*情報*" と "*デバッグ*" レベルのイベントを収集しないようにします。 |
| AzureDiagnostics           | リソース ログの収集を次のように変更します。 <br> - Log Analytics へのリソース送信ログの数を減らす <br> - 必要なログのみを収集する |
| ソリューションを必要としないコンピューターからのソリューション データ | [ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)を使用して、必要なコンピューター グループからのみデータを収集するようにします。 |

### <a name="check-if-there-are-more-nodes-than-expected"></a>ノード数が予想を超えているかどうかの確認
"*ノード単位 (OMS)*" の価格レベルを使用している場合は、使用するノードとソリューションの数に基づいて料金が発生します。 使用されている各プランのノード数は、使用量ダッシュボードの "*オファリング*" セクションで確認できます。<br><br> ![[使用量] ダッシュボード](./media/log-analytics-usage/log-analytics-usage-offerings.png)<br><br>


  **[すべて表示]** をクリックすると、選択したプランでデータを送信しているコンピューターの完全な一覧が表示されます。

[ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)を使用して、必要なコンピューター グループからのみデータを収集するようにします。

## <a name="next-steps"></a>次の手順
* 検索言語の使用方法については、[Log Analytics のログ検索](log-analytics-log-searches.md)に関する記事を参照してください。 検索クエリを使用して、使用量データをさらに分析できます。
* [新しいログ アラートの作成](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)に関するページで説明されている手順を使用して、検索条件が満たされた場合に通知されるようにします。
* [ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)を使用して、必要なコンピューター グループからのみデータを収集するようにします。
* 効果的なセキュリティ イベント収集ポリシーを構成するには、[Azure Security Center のフィルタリング ポリシー](../security-center/security-center-enable-data-collection.md)に関するページを参照してください。
* [パフォーマンス カウンターの構成](log-analytics-data-sources-performance-counters.md)を変更します。
* イベントの収集設定を変更する方法については、[イベント ログの構成](log-analytics-data-sources-windows-events.md)に関するページを参照してください。
* Syslog の収集設定を変更する方法については、[Syslog の構成](log-analytics-data-sources-syslog.md)に関するページを参照してください。
