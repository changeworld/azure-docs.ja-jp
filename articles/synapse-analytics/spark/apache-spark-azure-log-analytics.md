---
title: Azure Log Analytics を使用してメトリックとログを収集して視覚化する (プレビュー)
description: Apache Spark アプリケーションのメトリックとログを収集して Azure Log Analytics ワークスペースに送信するために、Synapse の組み込みの Azure Log Analytics コネクタを有効にする方法について説明します。
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 03/25/2021
ms.custom: references_regions
ms.openlocfilehash: 243618192593d93bba9d5229e7becfb2af62ce32
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107729"
---
# <a name="tutorial-use-azure-log-analytics-to-collect-and-visualize-metrics-and-logs-preview"></a>チュートリアル: Azure Log Analytics を使用してメトリックとログを収集して視覚化する (プレビュー)

このチュートリアルでは、Apache Spark アプリケーションのメトリックとログを収集して [Azure Log Analytics ワークスペース](/azure/azure-monitor/logs/quick-create-workspace)に送信するために、Synapse の組み込みの Azure Log Analytics コネクタを有効にする方法について説明します。 その後、Azure Monitor ブックを利用してメトリックとログを視覚化できます。

## <a name="configure-azure-log-analytics-workspace-information-in-synapse-studio"></a>Synapse Studio で Azure Log Analytics ワークスペースの情報を構成する

### <a name="step-1-create-an-azure-log-analytics-workspace"></a>手順 1: Azure Log Analytics ワークスペースを作成する

以下のドキュメントに従って、Log Analytics ワークスペースを作成できます。
- [Azure portal で Log Analytics ワークスペースを作成する](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace)
- [Azure CLI を使用して Log Analytics ワークスペースを作成する](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace-cli)
- [PowerShell を使用して Azure Monitor の Log Analytics ワークスペースを作成および構成する](https://docs.microsoft.com/azure/azure-monitor/logs/powershell-workspace-configuration)

### <a name="step-2-prepare-a-spark-configuration-file"></a>手順 2: Spark 構成ファイルを準備する

#### <a name="option-1-configure-with-azure-log-analytics-workspace-id-and-key"></a>方法 1. Azure Log Analytics ワークスペース ID およびキーを使用して構成する 

次の Spark 構成をコピーして **"spark_loganalytics_conf.txt"** として保存し、パラメーターを入力します。

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: Azure Log Analytics ワークスペース ID。
   - `<LOG_ANALYTICS_WORKSPACE_KEY>`: Azure Log Analytics キー: **Azure portal > [Azure Log Analytics ワークスペース] > [エージェント管理] > [プライマリ キー]**

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.secret <LOG_ANALYTICS_WORKSPACE_KEY>
```

#### <a name="option-2-configure-with-an-azure-key-vault"></a>方法 2. Azure キー コンテナーを使用して構成する

> [!NOTE]
>
> Spark アプリケーションを送信するユーザーにシークレットの読み取りアクセス許可を付与する必要があります。 「[Azure のロールベースのアクセス制御を使用して Key Vault のキー、証明書、シークレットへのアクセス権を付与する](https://docs.microsoft.com/azure/key-vault/general/rbac-guide)」を参照してください。

ワークスペース キーを格納するよう Azure キー コンテナーを構成するには、次の手順に従います。

1. Azure portal で、キー コンテナーを作成し、そこに移動します。
2. Key Vault の設定ページで、 **[シークレット]** を選択します。
3. **[Generate/Import]\(生成/インポート\)** をクリックします。
4. **[シークレットの作成]** 画面で、次の値を選択します。
   - **[名前]** : シークレットの名前を入力します。既定値として「`"SparkLogAnalyticsSecret"`」と入力します。
   - **[値]** : シークレットの **<LOG_ANALYTICS_WORKSPACE_KEY>** を入力します。
   - 他の値は既定値のままにしておきます。 **Create** をクリックしてください。
5. 次の Spark 構成をコピーして **"spark_loganalytics_conf.txt"** として保存し、パラメーターを入力します。

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: Azure Log Analytics ワークスペース ID。
   - `<AZURE_KEY_VAULT_NAME>`: 構成した Azure キー コンテナー名。
   - `<AZURE_KEY_VAULT_SECRET_KEY_NAME>` (省略可能): ワークスペース キーに対応する Azure キー コンテナー内のシークレット名 (既定値: "SparkLogAnalyticsSecret")。

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
```

> [!NOTE]
>
> Log Analytics ワークスペース ID を Azure キー コンテナーに格納することもできます。 上記の手順を参照し、このワークスペース ID をシークレット名 `"SparkLogAnalyticsWorkspaceId"` と共に格納してください。 または、`spark.synapse.logAnalytics.keyVault.key.workspaceId` 構成を使用して、Azure キー コンテナーにあるワークスペース ID のシークレット名を指定してください。

#### <a name="option-3-configure-with-an-azure-key-vault-linked-service"></a>方法 3. Azure Key Vault のリンク サービスを使用して構成する

> [!NOTE]
>
> Synapse ワークスペースに対してシークレットの読み取りアクセス許可を付与する必要があります。 「[Azure のロールベースのアクセス制御を使用して Key Vault のキー、証明書、シークレットへのアクセス権を付与する](https://docs.microsoft.com/azure/key-vault/general/rbac-guide)」を参照してください。

ワークスペース キーを格納するように Synapse Studio で Azure Key Vault のリンク サービスを構成するには、次の手順に従います。

1. 「`Option 2. Configure with an Azure Key Vault`」セクションのすべての手順に従います。
2. Synapse Studio で Azure Key Vault のリンク サービスを作成します。

    a. **Synapse Studio > [管理] > [リンク サービス]** に移動し、 **[新規]** ボタンをクリックします。

    b. 検索ボックスで **Azure Key Vault** を検索します。

    c. リンク サービスの名前を入力します。

    d. 自分の Azure キー コンテナーを選択します。 **[作成]** をクリックします。

3. Spark 構成に `spark.synapse.logAnalytics.keyVault.linkedServiceName` という項目を追加します。

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
spark.synapse.logAnalytics.keyVault.linkedServiceName <LINKED_SERVICE_NAME>
```

#### <a name="available-spark-configuration"></a>使用可能な Spark 構成

| 構成名                                  | 既定値                | 説明                                                                                                                                                                                                |
| --------------------------------------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| spark.synapse.logAnalytics.enabled                  | false                        | Spark アプリケーションに対して Azure Log Analytics シンクを有効にする場合は true。 それ以外の場合は false。                                                                                                                  |
| spark.synapse.logAnalytics.workspaceId              | -                            | 対象の Azure Log Analytics ワークスペース ID                                                                                                                                                          |
| spark.synapse.logAnalytics.secret                   | -                            | 対象の Azure Log Analytics ワークスペース シークレット。                                                                                                                                                      |
| spark.synapse.logAnalytics.keyVault.linkedServiceName   | -                            | Azure Log Analytics ワークスペース ID およびキー用の Azure Key Vault のリンク サービス名                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.name            | -                            | Azure Log Analytics ID およびキー用の Azure キー コンテナー名                                                                                                                                                |
| spark.synapse.logAnalytics.keyVault.key.workspaceId | SparkLogAnalyticsWorkspaceId | Azure Log Analytics ワークスペース ID 用の Azure キー コンテナー シークレット名                                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.key.secret      | SparkLogAnalyticsSecret      | Azure Log Analytics ワークスペース キー用の Azure キー コンテナー シークレット名                                                                                                                                      |
| spark.synapse.logAnalytics.keyVault.uriSuffix       | ods.opinsights.azure.com     | 対象の Azure Log Analytics ワークスペースの [URI サフィックス][uri_suffix]。 自分の Azure Log Analytics ワークスペースが Azure グローバルに存在しない場合は、それぞれのクラウドに従って URI サフィックスを更新する必要があります。 |

> [!NOTE]  
> - Azure China クラウドの場合は、"spark.synapse.logAnalytics.keyVault.uriSuffix" パラメーターを "ods.opinsights.azure.cn" にする必要があります。 
> - Azure Gov クラウドの場合は、"spark.synapse.logAnalytics.keyVault.uriSuffix" パラメーターを "ods.opinsights.azure.us" にする必要があります。 

[uri_suffix]: https://docs.microsoft.com/azure/azure-monitor/logs/data-collector-api#request-uri


### <a name="step-3-upload-your-spark-configuration-to-a-spark-pool"></a>手順 3: Spark 構成を Spark プールにアップロードする
構成ファイルは、Synapse Studio で Synapse Spark プールにアップロードできます。

   1. Azure Synapse Studio で Apache Spark プールに移動します ([管理] -> [Apache Spark プール])。
   2. Apache Spark プールの右側にある **[...]** ボタンをクリックします。
   3. Apache Spark 構成を選択します。 
   4. **[アップロード]** をクリックし、作成した **"spark_loganalytics_conf.txt"** を選択します。
   5. **[アップロード]** をクリックして **[適用]** をクリックします。

      > [!div class="mx-imgBorder"]
      > ![Spark プールの構成](./media/apache-spark-azure-log-analytics/spark-pool-configuration.png)

> [!NOTE] 
>
> 上記の Spark プールに送信されたすべての Spark アプリケーションでは、この構成設定を使用して、指定した Azure Log Analytics ワークスペースに Spark アプリケーションのメトリックとログをプッシュします。

## <a name="submit-a-spark-application-and-view-the-logs-and-metrics-in-azure-log-analytics"></a>Spark アプリケーションを送信して Azure Log Analytics でログとメトリックを表示する

 1. 次のいずれかの方法を使用すると、前の手順で構成した Spark プールに Spark アプリケーションを送信できます。
    - Synapse Studio ノートブックを実行します。 
    - Spark ジョブ定義を使用して Synapse Apache Spark バッチ ジョブを送信します。
    - Spark アクティビティを含むパイプラインを実行します。

 2. 指定した Azure Log Analytics ワークスペースに移動すると、Spark アプリケーションの実行が開始されたときにアプリケーションのメトリックとログが表示されます。

## <a name="use-the-sample-azure-log-analytics-workbook-to-visualize-the-metrics-and-logs"></a>サンプルの Azure Log Analytics ブックを使用してメトリックとログを視覚化する

1. こちらの[ブックをダウンロード](https://aka.ms/SynapseSparkLogAnalyticsWorkbook)します。
2. ブック ファイルを開き、内容を **コピー** します。
3. Azure Log Analytics ブックに移動します ([Azure portal](https://portal.azure.com/) > [Log Analytics ワークスペース] > [ブック])。
4. **"詳細エディター"** モードで **"空の"** Azure Log Analytics ブックを開きます (</> アイコンを押します)。
5. 存在する JSON の上に **貼り付け** ます。
6. **[適用]** 、 **[編集が完了しました]** の順にクリックします。

    > [!div class="mx-imgBorder"]
    > ![新しいブック](./media/apache-spark-azure-log-analytics/new-workbook.png)

    > [!div class="mx-imgBorder"]
    > ![ブックのインポート](./media/apache-spark-azure-log-analytics/import-workbook.png)

その後、構成済みの Spark プールに Apache Spark アプリケーションを送信します。 このアプリケーションが実行状態になったら、ブックのドロップダウン リストで、実行中のアプリケーションを選択します。

> [!div class="mx-imgBorder"]
> ![ブックの画像](./media/apache-spark-azure-log-analytics/workbook.png)

さらに、Kusto クエリでブックをカスタマイズしたり、アラートを構成したりできます。

> [!div class="mx-imgBorder"]
> ![kusto クエリとアラート](./media/apache-spark-azure-log-analytics/kusto-query-and-alerts.png)

## <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

1. Spark イベントに対するクエリの例。

   ```kusto
   SparkListenerEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100 
   ```

2. Spark アプリケーション ドライバーと Executor ログに対するクエリの例。

   ```kusto
   SparkLoggingEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100
   ```

3. Spark メトリックに対するクエリの例。

   ```kusto
   SparkMetrics_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | where name_s endswith "jvm.total.used"
   | summarize max(value_d) by bin(TimeGenerated, 30s), executorId_s
   | order by TimeGenerated asc
   ```

## <a name="create-and-manage-alerts-using-azure-log-analytics"></a>Azure Log Analytics を使用してアラートを作成および管理する

Azure Monitor アラートにより、ユーザーは Log Analytics クエリを使用して、設定された頻度でメトリックやログを評価し、その結果に基づいてアラートを発行することができます。

詳細については、「[Azure Monitor を使用してログ アラートを作成、表示、管理する](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log)」を参照してください。

## <a name="limitation"></a>制限事項

 - [マネージド仮想ネットワーク](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-vnet)が有効になっている Azure Synapse Analytics ワークスペースはサポートされていません。
 - 次のリージョンは、現在サポートされていません。
   - 米国東部 2
   - ノルウェー東部
   - アラブ首長国連邦北部

## <a name="next-steps"></a>次のステップ

 - [Synapse Studio でサーバーレス Apache Spark プールを使用する](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio)方法を学習します。
 - [ノートブックで Spark アプリケーションを実行する](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-development-using-notebooks)方法を学習します。
 - [Synapse Studio で Apache Spark ジョブ定義を作成する](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-job-definitions)方法を学習します。