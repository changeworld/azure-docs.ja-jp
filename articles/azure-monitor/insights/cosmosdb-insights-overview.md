---
title: Azure Monitor for Cosmos DB (プレビュー) で Azure Cosmos DB を監視する | Microsoft Docs
description: この記事では、Cosmos DB の所有者が CosmosDB アカウントのパフォーマンスと使用状況の問題をすばやく把握できる Azure Monitor for Cosmos DB の機能について説明します。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: dece5b0bb0508e2d83ee184e71ef0b4364d25ac8
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622971"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>Azure Monitor for Azure Cosmos DB (プレビュー) の詳細

Azure Monitor for Azure Cosmos DB (プレビュー) では、全 Azure Cosmos DB リソースの全体的なパフォーマンス、エラー、容量、操作上の正常性を、一元的な対話型エクスペリエンスで把握できます。 この記事では、この新しい監視エクスペリエンスの利点のほか、組織固有のニーズを満たすようそのエクスペリエンスに変更を加え、調整する方法をわかりやすく説明しています。   

## <a name="introduction"></a>はじめに

エクスペリエンスについて詳しく調べる前に、情報が提供および視覚化される方法を理解する必要があります。 

次のことが実現されます。

* **大規模な分析観点**: すべてのサブスクリプションの Azure Cosmos DB リソースを 1 か所で分析すると共に、関心のあるサブスクリプションとリソースに限定して評価することができます。

* **ドリルダウン分析**: 特定の Azure CosmosDB リソースについて、カテゴリ (使用率、エラー、容量、操作) ごとに問題を診断したり詳細な分析を実行したりするのに役立ちます。 これらのオプションのいずれかを選択すると、該当する Azure Cosmos DB のメトリックの詳細が表示されます。  

* **カスタマイズ可能**: このエクスペリエンスは、Azure Monitor ブック テンプレートをベースに作成されています。表示されるメトリックを変更したり、実際の制限に合わせてしきい値を変更または設定したりしてから、カスタム ブックに保存することができます。 ブック内のグラフは、Azure ダッシュボードにピン留めできます。  

この機能を使用するために、何かを有効にしたり構成したりする必要はありません。これらの Azure Cosmos DB メトリックは既定で収集されます。

>[!NOTE]
>この機能へのアクセスに対して料金はかからず、構成または有効にした Azure Monitor の基本機能に対してのみ課金されます。[Azure Monitor の価格の詳細](https://azure.microsoft.com/pricing/details/monitor/)に関するページをご覧ください。

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Azure Cosmos DB の操作レベルのメトリックを表示する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 左側のナビゲーション バーから **[監視]** を選択し、 **[メトリック]** を選択します。

   ![Azure Monitor のメトリック ウィンドウ](./media/cosmosdb-insights-overview/monitor-metrics-blade.png)

1. **[メトリック]** ウィンドウから、 **[リソースの選択]** を選択し、必要な**サブスクリプション**と**リソース グループ**を選択します。 **[リソースの種類]** で、 **[Azure Cosmos DB accounts]\(Azure Cosmos DB アカウント\)** を選択し、既存の Azure Cosmos アカウントの一つを選択し、 **[適用]** を選択します。

   ![メトリックを表示する Cosmos DB アカウントの選択](./media/cosmosdb-insights-overview/select-cosmosdb-account.png)

1. 次に、使用可能なメトリックの一覧からメトリックを選択できます。 要求ユニット、ストレージ、待機時間、可用性、Cassandra などに固有のメトリックを選択できます。 この一覧で使用可能なすべてのメトリックの詳細については、「[カテゴリ別のメトリック](../../cosmos-db/monitor-cosmos-db-reference.md)」の記事を参照してください。 この例では、 **[要求ユニット]** および集計値として **[Avg]** を選択します。

   これらの詳細に加えて、メトリックの **[時間の範囲]** と **[時間の粒度]** を選択することもできます。 最大で、過去 30 日間のメトリックを表示できます。  フィルターを適用すると、そのフィルターに基づいてグラフが表示されます。 選択した期間に消費された要求ユニットの 1 分あたりの平均数を確認できます。  

   ![Azure portal からのメトリックの選択](./media/cosmosdb-insights-overview/metric-types.png)

### <a name="add-filters-to-metrics"></a>メトリックにフィルターを追加する

メトリックと、特定の **CollectionName**、**DatabaseName**、**OperationType**、**Region**、および **StatusCode** によって表示されるグラフをフィルターすることもできます。 メトリックにフィルターを適用するには、 **[フィルターの追加]** を選択し、**OperationType** などの必要なプロパティを選択し、**Query** などの値を選択します。 その後グラフには、選択した期間のクエリ操作で消費された要求ユニットが表示されます。 ストアド プロシージャを介して実行された操作は、ログに記録されないため、OperationType メトリックでは使用できません。

![メトリック細分性を選択するためのフィルターの追加](./media/cosmosdb-insights-overview/add-metrics-filter.png)

**[Apply splitting]\(分割の適用\)** オプションを使用すると、メトリックをグループ化できます。 たとえば、次の図に示すように、要求ユニットを操作の種類ごとにグループ化し、すべての操作のグラフを一度に表示できます。

![分割の適用フィルターの追加](./media/cosmosdb-insights-overview/apply-metrics-splitting.png)

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Azure Cosmos DB の使用率とパフォーマンスのメトリックを表示する

所有するすべてのサブスクリプションのストレージ アカウントの使用状況とパフォーマンスを確認するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 「**監視**」を検索し、 **[監視]** を選択します。

    ![[サービス] の [監視] ドロップダウンとスピードメーターのスタイル画像が表示される検索ボックスに「監視」と入力したところ](./media/cosmosdb-insights-overview/search-monitor.png)

3. **[Cosmos DB (プレビュー)]** を選択します。

    ![Cosmos DB の概要ブックのスクリーンショット](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>概要

**[概要]** のテーブルには、インタラクティブな Azure Cosmos DB のメトリックが表示されます。 次のドロップダウン リストから選択するオプションに基づいて、結果をフィルター処理できます。

* **[サブスクリプション]** - Azure Cosmos DB リソースを含むサブスクリプションのみが一覧表示されます。  

* **[Cosmos DB]** - すべての Azure Cosmos DB リソースか、そのサブセット、またはいずれか 1 つを選択できます。

* **[時間の範囲]** - 既定では、選択結果に応じて過去 4 時間の情報が表示されます。

ドロップダウン リストの下に表示されるカウンター タイルには、選択したサブスクリプションに含まれる Azure Cosmos DB リソースの総数が集計されます。 ブックの列には、条件に基づく色分け (ヒートマップ) が示され、トランザクションのメトリックがレポートされます。 最も濃い色は最大値で、薄い色は最小値に基づいています。 

いずれかの Azure Cosmos DB リソースの横にあるドロップダウン矢印を選択すると、個々のデータベース コンテナー レベルでパフォーマンス メトリックの内訳が表示されます。

![ドロップダウンを展開すると、個々のデータベース コンテナーおよび関連するパフォーマンスの内訳が表示されます。](./media/cosmosdb-insights-overview/container-view.png)

青色で強調表示されている Azure Cosmos DB リソース名を選択すると、関連付けられている Azure Cosmos DB アカウントの既定の **[概要]** に移動します。 

### <a name="failures"></a>エラー

ページの上部にある **[エラー]** を選択すると、ブック テンプレートの**エラー**部分が表示されます。 要求数の合計が、それらの要求を構成する応答の配分と共に表示されます。

![HTTP 要求タイプごとの内訳を含むエラーのスクリーンショット](./media/cosmosdb-insights-overview/failures.png)

| コード      |  説明       | 
|-----------|:--------------------|
| `200 OK`  | 次のいずれかの REST 操作が成功しました。 </br>- リソースに対する GET。 </br> - リソースに対する PUT。 </br> - リソースに対する POST。 </br> - ストアド プロシージャ リソースに対する POST でのストアド プロシージャの実行。|
| `201 Created` | リソースを作成するための POST 操作が成功しました。 |
| `404 Not Found` | もう存在していないリソースを操作しようとしています。 たとえば、リソースは既に削除されている可能性があります。 |

詳細な状態コード一覧については、[Azure Cosmos DB HTTP 状態コードに関する記事](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)を参照してください。

### <a name="capacity"></a>容量

ページの上部にある **[容量]** を選択すると、ブック テンプレートの**容量**部分が表示されます。 保有するドキュメント数、時間の経過に伴うドキュメントの増加、データ使用状況、利用可能な残りストレージ容量の合計が表示されます。  これは、ストレージとデータ使用率の潜在的な問題を特定する目的で役立てることができます。

![[容量] ブック](./media/cosmosdb-insights-overview/capacity.png) 

概要ブックと同様、 **[サブスクリプション]** 列で Azure Cosmos DB リソースの横にあるドロップダウンを選択すると、データベースを構成する個々のコンテナーごとの内訳が表示されます。

### <a name="operations"></a>操作 

ページの上部にある **[操作]** を選択すると、ブック テンプレートの**操作**部分が表示されます。 送信された要求の種類ごとに要求の内訳を確認できます。 

以下の例を見ると、`eastus-billingint` は、読み取り要求の大部分を受け取っていますが、upsert 要求と作成要求はごくわずかであることがわかります。 `westeurope-billingint` は、要求の観点から言えば読み取りのみですが、現在このブックがパラメーターでスコープ設定されている時間範囲は、少なくとも過去 4 時間となります。

![操作ブック](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>ピン留め、エクスポート、展開

セクションの右上にある画びょうアイコンを選択すると、メトリックのどのセクションでも [Azure ダッシュボード](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)にピン留めできます。

![メトリック セクションのダッシュボードへのピン留めの例](./media/cosmosdb-insights-overview/pin.png)

データを Excel 形式にエクスポートするには、押しピン アイコンの左側にある下矢印アイコンを選択します。

![ブックのエクスポート アイコン](./media/cosmosdb-insights-overview/export.png)

ブックのすべてのドロップダウン ビューを展開したり折りたたんだりするには、エクスポート アイコンの左側の展開アイコンを選択します。

![ブックの展開アイコン](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>Azure Monitor for Azure Cosmos DB (プレビュー) のカスタマイズ

このエクスペリエンスは、Azure Monitor ブック テンプレートをベースに作成されているため、 **[カスタマイズ]**  >  **[編集]** して、編集したバージョンのコピーをカスタム ブックに **[保存]** することができます。 

![カスタマイズ バー](./media/cosmosdb-insights-overview/customize.png)

ブックは、リソース グループ内の、ユーザー個人の **[個人用レポート]** セクション、またはリソース グループにアクセスできるユーザーであればだれでもアクセスできる **[共有レポート]** セクションのいずれかに保存されます。 カスタム ブックを保存した後は、ブック ギャラリーに移動して起動する必要があります。

![コマンド バーからブック ギャラリーを起動する](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>次のステップ

* [メトリック アラート](../platform/alerts-metric.md)と[サービス正常性通知](../../service-health/alerts-activity-log-service-notifications.md)を構成して、問題の検出に役立つ自動アラートを設定します。

* ブックがサポートするように設計されているシナリオ、新規レポートの作成方法と既存レポートのカスタマイズ方法などについては、「[Azure Monitor ブックを使用した対話型レポートの作成](../app/usage-workbooks.md)」で学習してください。
