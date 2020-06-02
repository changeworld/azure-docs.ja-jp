---
title: Azure Cosmos コンテナーまたはアカウントの正規化された RU/秒を監視する
description: Azure Cosmos DB で、操作の正規化された要求ユニットの使用状況を監視する方法について説明します。 Azure Cosmos DB アカウントの所有者は、より多くの要求ユニットを消費している操作を把握できます。
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 05/10/2020
ms.openlocfilehash: 23001bdaab0732dbeb088ebadefa90a27e622b19
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118790"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Azure Cosmos コンテナーまたはアカウントの正規化された RU/秒を監視する方法

Azure Monitor for Azure Cosmos DB では、アカウントを監視したり、ダッシュボードを作成したりするためのメトリック ビューが提供されています。 Azure Cosmos DB のメトリックは既定で収集されるので、この機能を使用するために何かを明示的に有効にしたり構成したりする必要はありません。

**[Normalized RU Consumption]\(正規化された RU 消費量\)** メトリックを使用して、パーティション キー範囲全体の要求ユニット消費量に対してレプリカの書き込みがどの程度いっぱいになっているかを確認します。 Azure Cosmos DB は、スループットをすべての物理パーティション間で均等に分散します。 このメトリックは、レプリカ セット内の最大スループット使用率の 1 秒あたりのビューを示します。 このメトリックを使用して、要求ユニットの使用率が高くなっていることを確認した場合、ワークロードのニーズに合わせてスループットを増やす必要があります。

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>正規化された RU/秒が高い場合に想定して実行する内容

正規化された RU/秒消費量が 100% に達すると、クライアントはレート制限エラーを受け取ります。 クライアントは、待機時間を考慮して再試行する必要があります。 使用率 100% に達する短時間のスパイクが発生した場合、レプリカのスループットがその最大パフォーマンス制限に達したことを意味します。 たとえば、レプリカのすべての RU/秒を消費するストアド プロシージャなどの 1 つの操作によって、正規化された RU/秒消費量の短時間のスパイクが発生します。 このような場合、要求レートが低ければ、直ちにレート制限エラーが発生することはありません。 この理由は、Azure Cosmos DB では、特定の要求に対してプロビジョニングされた RU/秒より多くを要求で請求できるようにし、その期間内は他の要求をレート制限できることです。

Azure Monitor メトリックによる、 **[合計要求数]** メトリックを使用して、状態コードごとに操作を確認できます。 後で、429 状態コードによってこれらの要求をフィルターし、**操作の種類**別に分割することができます。

レート制限されている要求を確認するには、診断ログからこの情報を取得することをお勧めします。

正規化された RU/秒消費量が 100% または100% に近いピークのまま続く場合は、スループットを増やすことをお勧めします。 負荷の大きい操作とそのピーク使用率を確認するには、Azure Monitor メトリックと Azure Monitor ログを利用します。

**[Normalized RU Consumption]\(正規化された RU 消費量\)** メトリックを使用すると、使用率がよりウォームであるパーティション キー範囲を確認することもできます。これにより、パーティション キー範囲に対するスループットの傾斜を把握できます。 後で、追跡して Azure Monitor ログの **PartitionKeyRUConsumption** ログを確認し、使用率がホットになっている論理パーティション キーに関する情報を取得できます。

## <a name="view-the-normalized-request-unit-consumption-metric"></a>正規化された要求ユニット消費量メトリックを表示する

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. 左側のナビゲーション バーから **[監視]** を選択し、 **[メトリック]** を選択します。

   ![Azure Monitor のメトリック ウィンドウ](./media/monitor-normalized-request-units/monitor-metrics-blade.png)

3. **[メトリック]** ウィンドウから、 **[リソースの選択]** を選択し、必要な**サブスクリプション**と**リソース グループ**を選択します。 **[リソースの種類]** で、 **[Azure Cosmos DB accounts]\(Azure Cosmos DB アカウント\)** を選択し、既存の Azure Cosmos アカウントの一つを選択し、 **[適用]** を選択します。

   ![メトリックを表示する Azure Cosmos アカウントを選択する](./media/monitor-normalized-request-units/select-cosmos-db-account.png)

4. 次に、使用可能なメトリックの一覧からメトリックを選択できます。 要求ユニット、ストレージ、待機時間、可用性、Cassandra などに固有のメトリックを選択できます。 この一覧で使用可能なすべてのメトリックの詳細については、「[カテゴリ別のメトリック](monitor-cosmos-db-reference.md)」の記事を参照してください。 この例では、 **[Normalized RU Consumption]\(正規化された RU 消費量\)** メトリックを選択し、集計値として **[最大]** を選択します。

   これらの詳細に加えて、メトリックの **[時間の範囲]** と **[時間の粒度]** を選択することもできます。 最大で、過去 30 日間のメトリックを表示できます。  フィルターを適用すると、そのフィルターに基づいてグラフが表示されます。

   ![Azure portal からのメトリックの選択](./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png)

### <a name="filters-for-normalized-request-unit-consumption"></a>正規化された要求ユニット消費量をフィルターする

メトリックと、特定の **CollectionName**、**DatabaseName**、**PartitionKeyRangeID**、**Region** によって表示されるグラフをフィルターすることもできます。 メトリックをフィルターするには **[フィルターの追加]** を選択し、調べたい **CollectionName** などの必要なプロパティと対応する値を選択します。 これで、グラフには、選択した期間中にコンテナーで消費された、正規化された RU 消費量のユニットが表示されます。  

**[Apply splitting]\(分割の適用\)** オプションを使用すると、メトリックをグループ化できます。  

次の図に示すように、各コンテナーの正規化された要求ユニット消費量のメトリックが表示されます。

![正規化された要求ユニット消費量メトリックにフィルターを適用する](./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png)

## <a name="next-steps"></a>次のステップ

* Azure の[診断設定](cosmosdb-monitor-resource-logs.md)を使用して Azure Cosmos DB データを監視する
* [Azure Cosmos DB コントロール プレーン操作を監査する](audit-control-plane-logs.md)