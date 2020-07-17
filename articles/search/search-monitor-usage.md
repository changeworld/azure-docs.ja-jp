---
title: 操作とアクティビティを監視する
titleSuffix: Azure Cognitive Search
description: ログ記録を有効にし、クエリ アクティビティのメトリック、リソースの使用状況、およびその他のシステム データを Azure Cognitive Search サービスから取得します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462328"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Azure Cognitive Search の操作とアクティビティを監視する

この記事では、サービス (リソース) レベルとワークロード レベル (クエリやインデックス作成) での監視について説明し、ユーザー アクセスを監視するためのフレームワークを提案します。

統計情報、カウント、状態を返すサービス API に加え、組み込みのインフラストラクチャと基本的なサービス (Azure Monitor など) を広く組み合わせて使用します。 一連の機能を把握しておくと、表面化した問題を解決できるようにフィードバック ループを構築する際に役立ちます。

## <a name="use-azure-monitor"></a>Azure Monitor の使用

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) は、Azure Cognitive Search などの多くのサービスで、アラート、メトリック、診断データのログに活用されています。 Azure Cognitive Search では、組み込みの監視インフラストラクチャが主にリソースレベルの監視 (サービス正常性) と[クエリの監視](search-monitor-queries.md)に使用されます。

次のスクリーンショットは、ポータルで Azure Monitor 機能を見つける際に役立ちます。

+ **[監査]** タブ: メインの概要ページにあり、主要なメトリックを一目で確認できます。
+ **[アクティビティ ログ]** : [概要] のすぐ下にあります。リソースレベルのアクション (サービス正常性や API キー要求通知など) がレポートされます。
+ **[監視]** : リストの下の方にあり、構成可能なアラート、メトリック、診断ログが表示されます。 これらは必要に応じて作成します。 データが収集されて保存されたら、その情報を照会したり視覚化したりすることで分析情報を得ることができます。

![Search Service への Azure Monitor の統合](./media/search-monitor-usage/azure-monitor-search.png
 "Search Service への Azure Monitor の統合")

### <a name="precision-of-reported-numbers"></a>レポートされる数値の精度

ポータルのページは、数分ごとに更新されます。 したがって、このポータルでレポートされる数値は概数であり、システムによる要求の処理状況の目安を示すことが意図されています。 1 秒あたりのクエリ数 (QPS: Queries Per Second) など実際のメトリックは、ページに表示される値を上回る場合もあれば下回る場合もあります。

## <a name="activity-logs-and-service-health"></a>アクティビティ ログとサービス正常性

[**アクティビティ ログ**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)は、Azure Resource Manager から情報を収集し、サービス正常性の変化をレポートします。 アクティビティ ログを監視して、サービス正常性に関連したクリティカル、エラー、警告の各状態を把握することができます。

クエリ、インデックス作成、オブジェクトの作成など、進行中のタスクの場合は、各要求の "*管理者キーの取得*" や "*クエリ キーの取得*" など、情報提供を目的とした一般的な通知は表示されますが、具体的なアクション自体は表示されません。 この粒度の情報については、診断ログを構成する必要があります。

**アクティビティ ログ**には、左側のナビゲーション ウィンドウ、上部ウィンドウのコマンド バーの通知、または **[問題の診断と解決]** ページからアクセスすることができます。

## <a name="monitor-storage"></a>ストレージの監視

概要ページに組み込まれたタブ ページには、リソース使用状況がレポートされます。 この情報は、サービスを使い始めるとすぐに表示され、構成は必要ありません。また、ページは数分おきに更新されます。 

[運用環境のワークロードに使用するレベル](search-sku-tier.md)について、または[アクティブなレプリカとパーティションの数を調整する](search-capacity-planning.md)かどうかについて最終的に判断する場合、これらのメトリックを見ると、リソースが消費される速さや、現在の構成で既存の負荷がどの程度うまく処理されているかがわかるので、これらの決定を下すのに役立ちます。

ストレージに関連したアラートは現在利用できません。ストレージ消費量は集計されず、Azure Monitor の **AzureMetrics** テーブルにも記録されません。 リソースに関連した通知を生成する[カスタム ソリューションを作成](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating)する必要があります。そこでは、独自のコードでストレージ サイズを調べて応答を処理します。 ストレージのメトリックの詳細については、[サービス統計情報の取得](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response)に関するページを参照してください。

ポータルで視覚的に監視できるように、 **[Usage]\(使用状況\)** タブには、リソースの空き状況が、サービス レベルによる現在の[制限](search-limits-quotas-capacity.md)と比較して表示されます。 

次の図は、オブジェクトが種類ごとに 3 つ、ストレージが 50 MB に制限されている Free サービスを示したものです。 Basic または Standard サービスでは上限がこれより高く、パーティションの数を増やすと最大ストレージがそれに比例して増加します。

![レベルの制限を基準とした使用状況](./media/search-monitor-usage/usage-tab.png
 "レベルの制限を基準とした使用状況")

## <a name="monitor-workloads"></a>ワークロードの監視

ログに記録されるイベントには、インデックス作成やクエリに関連したイベントがあります。 クエリやインデックス作成に関連したオペレーショナル データは、Log Analytics の **AzureDiagnostics** テーブルに収集されます。

ログに記録されるデータの大半は、読み取りのみの操作に関するものです。 ログにキャプチャされないその他の操作 (作成、更新、削除) については、Search Service でシステム情報を照会してください。

| OperationName | 説明 |
|---------------|-------------|
| ServiceStats | この操作は、[サービス統計情報の取得](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics)に対するルーチン呼び出しです。直接呼び出されるか、またはポータル概要ページの内容を設定するために、その読み込み時または更新時に暗黙的に呼び出されます。 |
| Query.Search |  インデックスに対するクエリ要求。ログに記録されるクエリについては、[クエリの監視](search-monitor-queries.md)に関するページを参照してください。|
| Indexing.Index  | この操作は、[ドキュメントの追加、更新、削除](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)を行うための呼び出しです。 |
| indexes.Prototype | これは、データ インポート ウィザードによって作成されるインデックスです。 |
| Indexers.Create | データ インポート ウィザードを通じて暗黙的にまたは明示的にインデクサーを作成します。 |
| Indexers.Get | インデクサーが実行されるたびにそのインデクサーの名前を返します。 |
| Indexers.Status | インデクサーが実行されるたびにそのインデクサーの状態を返します。 |
| DataSources.Get | インデクサーが実行されるたびにデータ ソースの名前を返します。|
| Indexes.Get | インデクサーが実行されるたびにインデックスの名前を返します。 |

### <a name="kusto-queries-about-workloads"></a>ワークロードに関する Kusto クエリ

ログを有効にした場合は、**AzureDiagnostics** にクエリを実行して、特定のサービスでいつどのような操作が実行されたかを照会できます。 また、アクティビティを関連付けて、パフォーマンスの変化を調べることもできます。

#### <a name="example-list-operations"></a>例:操作の一覧表示 

操作と各操作の回数のリストを取得します。

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>例:操作の関連付け

クエリ要求をインデックス作成操作に関連付け、時間グラフにデータ ポイントをレンダリングして同時に起こった操作を表示します。

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Search API の使用

Azure Cognitive Search REST API および .NET SDK の両方を使用することにより、プログラムからサービス メトリック、インデックスとインデクサーの情報、ドキュメント数にアクセスできます。

+ [サービス統計情報の取得](/rest/api/searchservice/get-service-statistics)
+ [インデックス統計の取得](/rest/api/searchservice/get-index-statistics)
+ [ドキュメント数の取得](/rest/api/searchservice/count-documents)
+ [インデクサーの状態の取得](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>ユーザー アクセスの監視

検索インデックスは、より大きなクライアント アプリケーションのコンポーネントであるため、インデックスへのアクセスをユーザーごとに制御または監視するために組み込まれた手法はありません。 要求は、管理またはクエリ要求を目的として、クライアント アプリケーションから送信されたものと見なされます。 管理目的の読み取り/書き込み操作には、サービス全体を対象としたオブジェクトの作成、更新、削除が含まれます。 読み取り専用の操作としては、単一のインデックスに範囲が限定されたドキュメント コレクションに対するクエリがあります。 

その意味で、アクティビティ ログに表示されるのは、管理者キーまたはクエリ キーを使用した呼び出しの参照といえます。 クライアント コードから送信される要求には、適切なキーが含まれています。 このサービスには、ID トークンや偽装を処理するしくみが備わっていません。

ビジネス要件がユーザーごとの承認のために存在する場合は、Azure Active Directory との統合をお勧めします。 $filter とユーザー ID を使用すると、ユーザーに表示すべきでない、ドキュメントの[検索結果をトリミングする](search-security-trimming-for-azure-search-with-aad.md)ことができます。 

$filter パラメーターを含んだクエリ文字列とは別に、この情報をログに記録することはできません。 クエリ文字列のレポートの詳細については、[クエリの監視](search-monitor-queries.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Azure Cognitive Search などのリソースを含む Azure サービスを監視するうえで、Azure Monitor を使いこなすことが欠かせません。 Azure Monitor を十分に理解していない場合は、時間を取ってリソースに関連した記事を確認してください。 チュートリアルのほか、次の記事からお読みいただくことをお勧めします。

> [!div class="nextstepaction"]
> [Azure Monitor を使用した Azure リソースの監視](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
