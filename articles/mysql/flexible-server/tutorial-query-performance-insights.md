---
title: 'チュートリアル: Azure Database for MySQL – フレキシブル サーバーでのクエリ パフォーマンスの分析情報'
description: 'チュートリアル: Azure Database for MySQL – フレキシブル サーバーでのクエリ パフォーマンスの分析情報'
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: ed78f493021c94c8beeecb8d5470d9a846b19d8d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621500"
---
# <a name="tutorial-query-performance-insights-for-azure-database-for-mysql--flexible-server"></a>チュートリアル: Azure Database for MySQL – フレキシブル サーバーでのクエリ パフォーマンスの分析情報
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Query Performance Insight は、データベースに対してインテリジェントなクエリ分析を提供することを提案します。 最も優先度の高い分析情報は、ワークロードのパターンと実行時間の長いクエリです。 これにより、パフォーマンス全体を向上させ、利用可能なリソースを効率的に使用するために、最適化するクエリを見つけることができます。 Query Performance Insight を使用すると、以下のような詳細が提供され、データベースのパフォーマンスのトラブルシューティングに費やす時間を短縮できます。
* 実行時間の長いクエリの上位 N 個とその傾向。
* クエリの詳細にドリルダウンして、クエリ テキストと、最小、最大、平均、および標準偏差のクエリ時間を含む実行履歴を表示する機能
* リソース使用率 (CPU、メモリ、およびストレージ)
 
このチュートリアルでは、MySQL の低速クエリ ログ、Log Analytics ツールまたは Workbooks テンプレートを使用して、Azure Database for MySQL フレキシブル サーバーのクエリ パフォーマンスの分析情報を視覚化する方法について説明します。 

## <a name="prerequisites"></a>前提条件
- Azure Database for MySQL – フレキシブル サーバーのインスタンスを作成する必要があります。 詳細な手順については、[Azure Database for MySQL - フレキシブル サーバーのインスタンスの作成](./quickstart-create-server-portal.md)に関するページを参照してください。
- Log Analytics ワークスペースを作成する必要があります。 詳細な手順については、[Log Analytics ワークスペースの作成](../../azure-monitor/logs/quick-create-workspace.md)に関するページを参照してください。

このチュートリアルで学習する内容は次のとおりです。
>[!div class="checklist"]
> * 低速クエリ ログをポータルから、または Azure CLI を使用して構成する
> * 診断を設定する
> * Log Analytics を使用して低速クエリを表示する 
> * Workbooks を使用して低速クエリを表示する 

## <a name="configure-slow-query-logs-from-portal"></a>ポータルから低速クエリ ログを構成する 


1. [Azure portal](https://portal.azure.com/) にサインインします。

1. フレキシブル サーバーを選択します。

1. サイドバーの **[設定]** セクションで、 **[サーバー パラメーター]** を選択します。
   :::image type="content" source="./media//tutorial-query-performance-insights/server-parameters.png" alt-text="[サーバー パラメーター] ページ。":::

1. **slow_query_log** パラメーターを **[ON]** に更新します。
   :::image type="content" source="./media/tutorial-query-performance-insights/slow-query-log-enable.png" alt-text="低速クエリ ログを有効にします。":::

1. 必要なその他のすべてのパラメーターを変更します (例: `long_query_time`, `log_slow_admin_statements`). その他のパラメーターについては、[低速クエリ ログ](./concepts-slow-query-logs.md#configure-slow-query-logging)のドキュメントを参照してください。  
   :::image type="content" source="./media/tutorial-query-performance-insights/long-query-time.png" alt-text="低速クエリ ログに関連するパラメーターを更新します。":::

1. **[保存]** を選択します。 
   :::image type="content" source="./media/tutorial-query-performance-insights/save-parameters.png" alt-text="低速クエリ ログ パラメーターを保存します。":::

**[サーバー パラメーター]** ページを閉じると、ログの一覧に戻ることができます。



## <a name="configure-slow-query-logs-from-azure-cli"></a>Azure CLI から低速クエリ ログを構成する
 
Azure CLI を使用して上記の手順を実行する場合は、CLI を使用してサーバーの低速クエリ ログを有効にし、構成することができます。 

> [!IMPORTANT]
> サーバーのパフォーマンスに大きな影響を与えないように、監査のために必要なイベントの種類とユーザーのみをログに記録することをお勧めします。

サーバーの低速クエリ ログを有効にして構成します。

```azurecli
# Turn on statement level log

az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 sec

# This setting will log all queries executing for more than 10 sec. Please adjust this threshold based on your definition for slow queries

az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable Slow query logs



az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="set-up-diagnostics"></a>診断を設定する

低速クエリ ログは Azure Monitor 診断設定と統合されており、ログを Azure Monitor ログ、Event Hubs、または Azure Storage にパイプ処理することができます。

1. サイドバーの **[監視]** セクションの下で、 **[診断設定]**  >  **[診断設定を追加する]** を選択します。

   :::image type="content" source="./media/tutorial-query-performance-insights/add-diagnostic-setting.png" alt-text="[診断設定] のオプションのスクリーンショット":::

1. 診断設定の名前を指定します。

1. どの送信先 (Log Analytics ワークスペース、ストレージ アカウントまたはイベント ハブ) に低速クエリ ログを送信するかを指定します。

    >[!Note]
    > このチュートリアルの範囲では、低速クエリ ログを Log Analytics ワークスペースに送信する必要があります。

1. ログの種類として **[MySqlSlowLogs]** を選択します。
    :::image type="content" source="./media/tutorial-query-performance-insights/configure-diagnostic-setting.png" alt-text="診断設定構成オプションのスクリーンショット":::

1. 低速クエリ ログをパイプするようにデータ シンクを設定した後、 **[保存]** を選択します。
    :::image type="content" source="./media/tutorial-query-performance-insights/save-diagnostic-setting.png" alt-text="[保存] が強調して示されている診断設定構成オプションのスクリーンショット":::

    >[!Note]
    >診断設定を構成する前に、データ シンク (Log Analytics ワークスペース、ストレージ アカウント、またはイベント ハブ) を作成する必要があります。 構成したデータ シンク (Log Analytics ワークスペース、ストレージ アカウント、またはイベント ハブ) で低速クエリ ログにアクセスできます。ログが表示されるまでに最大 10 分かかることがあります。
 
## <a name="view-query-insights-using-log-analytics"></a>Log Analytics を使用してクエリ分析情報を表示する 

**[監視]** セクションで、 **[ログ]** に移動します。 **[クエリ]** ウィンドウを閉じます。

:::image type="content" source="./media/tutorial-query-performance-insights/log-query.png" alt-text="Log Analytics のスクリーンショット":::

クエリ ウィンドウでは、実行するクエリを記述できます。  ここでは、クエリを使用して、特定のサーバーで 10 秒を超えるクエリを検索しました。

```kusto
 AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
```
    
**[時間の範囲]** を選択し、クエリを **実行** します。 下にクエリの結果が表示されます。  

:::image type="content" source="./media/tutorial-query-performance-insights/slow-query.png" alt-text="低速クエリ ログのスクリーンショット":::

## <a name="view-query-insights-using-workbooks"></a>Workbooks を使用してクエリ分析情報を表示する 

1.  Azure portal で、Azure Database for MySQL - フレキシブル サーバーの **[監視]** ブレードに移動し、 **[Workbooks]** を選択します。
2.  テンプレートを表示できるようになります。 **Query Performance Insight** を選択します。 

    :::image type="content" source="./media/tutorial-query-performance-insights/monitor-workbooks.png" alt-text="ブック テンプレートのスクリーンショット":::

以下の視覚化を表示できます。 
>[!div class="checklist"]
> * クエリの読み込み
> * アクティブな接続の合計
> * 低速クエリの傾向 (クエリ時間が 10 秒を超えるもの)
> * 低速クエリの詳細
> * 長さが上位 5 件のクエリを一覧表示する
> * 低速クエリの最小、最大、平均、および標準偏差のクエリ時間を集計する

    
:::image type="content" source="./media/tutorial-query-performance-insights/long-query.png" alt-text="長いクエリのスクリーンショット":::

>[!Note]
> * リソース使用率については、概要テンプレートを使用できます。
> * これらのテンプレートを編集し、要件に従ってカスタマイズすることもできます。詳細については、[Azure Monitor Workbooks の概要 - Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode) に関する記事を参照してください
> * 素早く表示するために、ダッシュボードにブックまたは Log Analytics クエリをピン留めすることもできます。 詳細については、「[Azure portal でダッシュボードを作成する - Azure portal](../../azure-portal/azure-portal-dashboards.md)」を参照してください。 

Query Performance Insight の 2 つのメトリック (実行時間と実行回数) は、潜在的なボトルネックの特定に役立つ場合があります。 実行時間の長いクエリは、長期にわたるリソースのロック、他のユーザーのブロック、スケーラビリティの制限を引き起こす最大の原因と考えられます。 場合によっては、実行回数が多いと、ネットワークのラウンド トリップが増えることがあります。 ラウンド トリップはパフォーマンスに影響します。 これは、ネットワーク待ち時間やダウンストリーム サーバーの待ち時間の影響を受けます。 実行回数は、頻繁に実行される ("おしゃべりな") クエリを見つけるのに役立ちます。 こうしたクエリは、最適化に最も適した要素です。 

## <a name="next-steps"></a>次の手順
- [Azure Monitor Workbooks の使用を開始](../../azure-monitor/visualize/workbooks-overview.md#visualizations)し、ブックの豊富な視覚化オプションを確認します。
- [低速クエリ ログ](concepts-slow-query-logs.md)の詳細情報


