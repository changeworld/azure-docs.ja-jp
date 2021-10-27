---
title: 'チュートリアル: Azure Database for MySQL フレキシブル サーバー向けの Query Performance Insight'
description: この記事では、Azure Database for MySQL フレキシブル サーバーの Query Performance Insight を視覚化するのに役立つツールについて説明します。
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: 63cec8fa00af3e4711c4c6383c68cc09d32ce3d3
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065713"
---
# <a name="tutorial-query-performance-insight-for-azure-database-for-mysql-flexible-server"></a>チュートリアル: Azure Database for MySQL フレキシブル サーバー向けの Query Performance Insight
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Query Performance Insight は、データベースに対してインテリジェントなクエリ分析を提供することを提案します。 最も優先度の高い分析情報は、ワークロードのパターンと実行時間の長いクエリです。 これらの分析情報を理解すると、どのクエリを最適化すれば、全体的なパフォーマンスを向上させ、使用可能なリソースを効率的に使用できるかを見つけるのに役立ちます。 

Query Performance Insight は、次のような情報を提供することで、データベースのパフォーマンスのトラブルシューティングに費やす時間を短縮できるように設計されています。
* 実行時間の長いクエリの上位 *N* 個とその傾向。
* クエリの詳細: クエリ テキストと、クエリ時間の最小、最大、平均、および標準偏差を含む実行履歴の表示。
* リソース使用率 (CPU、メモリ、ストレージ)。
 
この記事では、MySQL の低速クエリ ログ、Log Analytics ツール、ブック テンプレートを使用して、Azure Database for MySQL フレキシブル サーバーの Query Performance Insight を視覚化する方法について説明します。

このチュートリアルで学習する内容は次のとおりです。
>[!div class="checklist"]
> * Azure portal または Azure CLI を使用して低速クエリ ログを構成する
> * 診断を設定する
> * Log Analytics を使用して低速クエリ ログを表示する 
> * ブックを使用して低速クエリ ログを表示する 

## <a name="prerequisites"></a>前提条件

- [Azure Database for MySQL フレキシブル サーバー インスタンスを作成します](./quickstart-create-server-portal.md)。
- [Log Analytics ワークスペースを作成します](../../azure-monitor/logs/quick-create-workspace.md)。


## <a name="configure-slow-query-logs-by-using-the-azure-portal"></a>Azure portal を使用して低速クエリ ログを構成する 

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. フレキシブル サーバー インスタンスを選択します。

1. 左側のウィンドウの **[設定]** の下で **[サーバー パラメーター]** を選択します。

   :::image type="content" source="./media//tutorial-query-performance-insights/server-parameters.png" alt-text="[サーバーパラメーター] の一覧を示すスクリーンショット。":::

1. **slow_query_log** パラメーターで **[ON]\(オン\)** を選択します。

   :::image type="content" source="./media/tutorial-query-performance-insights/slow-query-log-enable.png" alt-text="[ON]\(オン\) に切り替えた 'slow_query_log' パラメーターを示すスクリーンショット。":::

1. **Long_query_time** や **log_slow_admin_statements** などの他のパラメーターについては、[低速クエリ ログ](./concepts-slow-query-logs.md#configure-slow-query-logging)のドキュメントを参照してください。  

   :::image type="content" source="./media/tutorial-query-performance-insights/long-query-time.png" alt-text="残りの低速クエリ ログ関連パラメーターの更新された値を示すスクリーンショット。":::

1. **[保存]** を選択します。 

   :::image type="content" source="./media/tutorial-query-performance-insights/save-parameters.png" alt-text="パラメーター値の変更を保存するための [保存] ボタンのスクリーンショット。":::

**[サーバー パラメーター]** ページを閉じると、ログの一覧に戻ることができます。

## <a name="configure-slow-query-logs-by-using-the-azure-cli"></a>Azure CLI を使用して低速クエリ ログを構成する
 
別の方法として、Azure CLI から次のコマンドを実行して、フレキシブル サーバーの低速クエリ ログを有効にして構成することもできます。 

> [!IMPORTANT]
> フレキシブル サーバーのパフォーマンスに大きな影響を与えないように、監査のために必要なイベントの種類とユーザーのみをログに記録することをお勧めします。

```azurecli
# Turn on statement level log.

az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 seconds. This setting will log all queries that execute for more than 10 seconds. Adjust this threshold based on your definition for slow queries.

az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable slow query logs.

az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="set-up-diagnostics"></a>診断を設定する

低速クエリ ログは Azure Monitor 診断設定と統合されているため、次の 3 つのデータ シンクにログをパイプすることができます。
* Log Analytics ワークスペース
* イベント ハブ
* ストレージ アカウント

>[!Note]
>診断設定を構成する前に、データ シンクを作成する必要があります。 構成したデータ シンクで低速クエリ ログにアクセスできます。 ログが表示されるまでに最大で 10 分かかる可能性があります。

1. 左側のウィンドウの **[モニター]** の下で、 **[診断設定]** を選択します。

1. **[診断設定]** ウィンドウで、 **[診断設定を追加する]** を選択します。

   :::image type="content" source="./media/tutorial-query-performance-insights/add-diagnostic-setting.png" alt-text="[診断設定] ウィンドウの [診断設定を追加する] リンクのスクリーンショット。":::

1. **[名前]** ボックスに、診断設定の名前を入力します。

1. 低速クエリ ログの送信先 (Log Analytics ワークスペース、イベント ハブ、またはストレージ アカウント) を指定するために、対応するチェックボックスをオンにします。

    >[!Note]
    > このチュートリアルでは、低速クエリ ログを Log Analytics ワークスペースに送信します。

1. **[ログ]** の [ログの種類] で、 **[MySqlSlowLogs]** のチェックボックスをオンにします。

    :::image type="content" source="./media/tutorial-query-performance-insights/configure-diagnostic-setting.png" alt-text="構成オプションを選択するための [診断設定] ウィンドウのスクリーンショット。":::

1. 低速クエリ ログをパイプするようにデータ シンクを設定した後、 **[保存]** を選択します。

    :::image type="content" source="./media/tutorial-query-performance-insights/save-diagnostic-setting.png" alt-text="[保存] が強調して示されている診断設定構成オプションのスクリーンショット":::

## <a name="view-query-insights-by-using-log-analytics"></a>Log Analytics を使用してクエリ分析情報を表示する 

1. Log Analytics の左側のペインの **[監視]** から **[ログ]** を選択します。

1. 開いている **[クエリ]** ウィンドウを閉じます。

   :::image type="content" source="./media/tutorial-query-performance-insights/log-query.png" alt-text="Log Analytics の [クエリ] ウィンドウのスクリーンショット。":::

1. クエリ ウィンドウでは、実行するクエリを記述できます。 特定のサーバーで 10 秒を超えるクエリを検索するために、次のコードを使用しました。

   ```kusto
   AzureDiagnostics
      | where Category == 'MySqlSlowLogs'
      | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
      | where query_time_d > 10
   ```
    
1. **[時間の範囲]** を選択し、クエリを実行します。 結果は次の画像に示すとおりです。  

   :::image type="content" source="./media/tutorial-query-performance-insights/slow-query.png" alt-text="低速クエリ ログのスクリーンショット。":::

## <a name="view-query-insights-by-using-workbooks"></a>ブックを使用してクエリ分析情報を表示する 

1. Azure portal の左側のペインで、Azure Database for MySQL フレキシブル サーバー インスタンスの **[監視]** の下にある **[ブック]** を選択します。

1.  **[Query Performance Insight]** テンプレートを選択します。 

    :::image type="content" source="./media/tutorial-query-performance-insights/monitor-workbooks.png" alt-text="ブック ギャラリー内のすべてのブックを示すスクリーンショット。":::

ブックでは、次の視覚化を表示できます。 
>[!div class="checklist"]
> * クエリの読み込み
> * アクティブな接続の合計
> * 低速クエリの傾向 (クエリ時間が 10 秒を超えるもの)
> * 低速クエリの詳細
> * 長さが上位 5 件のクエリを一覧表示する
> * 低速クエリの最小、最大、平均、および標準偏差のクエリ時間を集計する

    
:::image type="content" source="./media/tutorial-query-performance-insights/long-query.png" alt-text="2 つの長いクエリを示すスクリーンショット。":::

>[!Note]
> * リソース使用率を表示するには、概要テンプレートを使用できます。
> * これらのテンプレートを編集し、要件に従ってカスタマイズすることもできます。 詳細については、[Azure Monitor ブックの概要](../../azure-monitor/visualize/workbooks-overview.md#editing-mode)に関する記事を参照してください。
> * 素早く表示するために、ブックまたは Log Analytics クエリをダッシュボードにピン留めすることもできます。 詳細については、「[Azure portal でダッシュボードを作成する](../../azure-portal/azure-portal-dashboards.md)」を参照してください。 

Query Performance Insight で、潜在的なボトルネックの特定に役立てることが可能な 2 つのメトリックは、"*実行時間*" と "*実行回数*" です。 実行時間の長いクエリは、長期にわたるリソースのロック、他のユーザーのブロック、スケーラビリティの制限を引き起こす最大の原因と考えられます。 

場合によっては、実行回数が多いと、ネットワークのラウンド トリップが増えることがあります。 ラウンド トリップはパフォーマンスに影響します。 これは、ネットワーク待ち時間やダウンストリーム サーバーの待ち時間の影響を受けます。 実行回数は、頻繁に実行される ("おしゃべりな") クエリを見つけるのに役立ちます。 こうしたクエリは、最適化に最も適した要素です。 

## <a name="next-steps"></a>次の手順
- [Azure Monitor ブック](../../azure-monitor/visualize/workbooks-overview.md#visualizations)とその豊富な視覚化オプションについて学習します。
- [低速クエリ ログについて学習します](concepts-slow-query-logs.md)。


