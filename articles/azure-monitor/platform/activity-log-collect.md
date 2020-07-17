---
title: Azure Monitor での Azure アクティビティ ログの収集と分析
description: Azure Monitor のログに Azure アクティビティ ログを収集し、監視ソリューションを使用して、すべての Azure サブスクリプションにわたって Azure アクティビティ ログの分析や検索ができます。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382872"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Azure Monitor での Azure アクティビティ ログの収集と分析
[Azure アクティビティ ログ](platform-logs-overview.md)は、Azure で発生したサブスクリプションレベルのイベントの分析情報を提供する[プラットフォーム ログ](platform-logs-overview.md)です。 Azure portal でアクティビティ ログを表示できますが、Log Analytics ワークスペースに送信して Azure Monitor の追加機能を有効にするように構成する必要があります。 この記事では、この構成を実行する方法と、アクティビティ ログを Azure Storage およびイベント ハブに送信する方法について説明します。

Log Analytics ワークスペースでアクティビティ ログを接続すると、次のような利点があります。

- Log Analytics ワークスペースに格納されているアクティビティ ログ データのデータ インジェストまたはデータ保持の料金は発生しません。
- アクティビティ ログ データを、Azure Monitor によって収集されたその他の監視データと関連付けます。
- ログ クエリを使用して複雑な分析を実行し、アクティビティ ログのエントリから詳細な分析情報を得ます。
- アクティビティ エントリでログ アラートを使用すると、より複雑なアラート ロジックを使用できます。
- アクティビティ ログのエントリを 90 日を超えて保存します。
- 複数の Azure サブスクリプションおよびテナントのログ エントリを 1 つの場所に統合して、まとめて分析できるようにします。

> [!IMPORTANT]
> テナント間でログを収集するには [Azure Lighthouse](/azure/lighthouse) が必要です。

## <a name="collecting-activity-log"></a>アクティビティ ログの収集
アクティビティログは、[Azure portal で表示する](activity-log-view.md)ために自動的に収集されます。 これを Log Analytics ワークスペースで収集したり、Azure Storage またはイベント ハブに送信したりするには、[診断設定](diagnostic-settings.md)を作成します。 これは、リソース ログによって使用される同じ方法で、すべての[プラットフォーム ログ](platform-logs-overview.md)で一貫させます。  

アクティビティ ログの診断設定を作成するには、Azure Monitor の **[アクティビティ ログ]** メニューから **[診断設定]** を選択します。 設定の作成の詳細については、「[Azure でログとメトリックを収集するための診断設定を作成する](diagnostic-settings.md)」を参照してください。 フィルター処理できるカテゴリの説明については、「[アクティビティログのカテゴリ](activity-log-view.md#categories-in-the-activity-log)」を参照してください。 何らかのレガシ設定がある場合は、診断設定を作成する前にそれらを無効にしてください。 両方を有効にすると、重複するデータが生成される可能性があります。

![診断設定](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> 現時点では、Azure portal と Resource Manager テンプレートを使用して、サブスクリプション レベルの診断設定のみを作成できます。 


## <a name="legacy-settings"></a>レガシ設定 
診断設定はさまざまな宛先にアクティビティ ログを送信するために推奨される方法ですが、診断設定で置き換えるように選択しないと、レガシの方法が引き続き機能します。 診断設定には、レガシの方法よりも次のような利点があるため、構成を更新することをお勧めします。

- すべてのプラットフォーム ログを収集するための一貫した方法。
- 複数のサブスクリプションとテナントにわたってアクティビティ ログを収集する。
- コレクションをフィルター処理して特定のカテゴリのログのみを収集する。
- すべてのアクティビティ ログ カテゴリを収集する。 一部のカテゴリは、従来の方法を使用して収集されません。
- ログ インジェストの待機時間が短縮される。 前の方法では約 15 分の待機時間がありましたが、診断設定では約 1 分しか追加されません。



### <a name="log-profiles"></a>ログ プロファイル
ログ プロファイルは、Azure Storage またはイベント ハブにアクティビティ ログを送信するためのレガシの方法です。 ログ プロファイルを引き続き使用する場合、または診断設定への移行の準備としてそれを無効にする場合は、次の手順に従います。

1. Azure portal の **[Azure Monitor]** メニューで、 **[アクティビティ ログ]** を選択します。
3. **[診断設定]** をクリックします。

   ![診断設定](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 従来のエクスペリエンスについては、紫色のバナーをクリックします。

    ![従来のエクスペリエンス](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Log Analytics ワークスペース
アクティビティ ログを Log Analytics ワークスペースに収集するレガシの方法は、ワークスペース構成でログを接続することです。 

1. Azure portal の **[Log Analytics ワークスペース]** メニューから、アクティビティ ログを収集するためのワークスペースを選択します。
1. ワークスペースのメニューの **[ワークスペースのデータ ソース]** セクションで、 **[Azure アクティビティ ログ]** を選択します。
1. 接続するサブスクリプションをクリックします。

    ![Workspaces](media/activity-log-collect/workspaces.png)

1. **[接続]** をクリックして、選択したワークスペースにサブスクリプションのアクティビティ ログを接続します。 サブスクリプションが既に別のワークスペースに接続されている場合、最初に **[切断]** をクリックして切断します。

    ![ワークスペースを接続する](media/activity-log-collect/connect-workspace.png)


この設定を無効にするには、同じ手順を実行し、 **[切断]** をクリックして、ワークスペースからサブスクリプションを削除します。


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Log Analytics ワークスペースでのアクティビティ ログの分析
アクティビティ ログを Log Analytics ワークスペースに接続すると、ワークスペースにあり、[ログ クエリ](../log-query/log-query-overview.md)で取得できる *AzureActivity* という名前のテーブルにエントリが書き込まれるようになります。 このテーブルの構造は[ログ エントリのカテゴリ](activity-log-view.md#categories-in-the-activity-log)によって異なります。 各カテゴリの説明については、「[Azure アクティビティ ログのイベント スキーマ](activity-log-schema.md)」を参照してください。


### <a name="data-structure-changes"></a>データ構造の変更
診断設定では、アクティビティ ログを収集するために使われていたレガシの方法と同じデータを収集しますが、*AzureActivity* テーブルの構造にいくらかの変更が加えられています。

次の表の列は、更新されたスキーマで非推奨とされています。 これらは、*AzureActivity* にまだ存在しますが、データを持ちません。 これらの列の代わりとなるものは新しいものではありませんが、非推奨の列と同じデータが格納されています。 それらは形式が異なるため、それらを使用するログ クエリの変更が必要になる場合があります。 

| 非推奨の列 | 置換列 |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> 場合によっては、これらの列の値がすべて大文字になることがあります。 これらの列を含むクエリがある場合は、[=~ 演算子](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators)を使用して、大文字と小文字を区別しない比較を実行する必要があります。

更新されたスキーマの *AzureActivity* には、次の列が追加されています。

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Activity Logs Analytics 監視ソリューション
Azure Log Analytics 監視ソリューションは、間もなく非推奨となり、Log Analytics ワークスペースで更新されたスキーマを使用するブックに置き換えられます。 このソリューションは、既に有効にしている場合に引き続き使用できますが、レガシの設定を使用してアクティビティ ログを収集している場合にのみ使用できます。 



### <a name="use-the-solution"></a>ソリューションの使用
監視ソリューションには Azure portal の **[Monitor]\(監視\)** メニューからアクセスします。 **[Insights]\(インサイト\)** セクションで **[More]\(詳細\)** を選択して、ソリューション タイルのある **[概要]** ページを開きます。 **[Azure アクティビティ ログ]** タイルには、ワークスペース内の **AzureActivity** レコードの数が表示されます。

![Azure アクティビティ ログのタイル](media/collect-activity-logs/azure-activity-logs-tile.png)


**[Azure アクティビティ ログ]** タイルをクリックして、 **[Azure アクティビティ ログ]** ビューを開きます。 ビューには、次の表の視覚化パーツが含まれています。 それぞれのパーツには、指定された時間範囲について、そのパーツの基準に該当する項目が 10 個まで表示されます。 パーツの下部にある **[すべて表示]** をクリックすると、すべての一致するレコードを返すログ クエリを実行できます。

![Azure のアクティビティ ログのダッシュボード](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>新しいサブスクリプションでソリューションを有効にする
間もなく、Azure portal を使用して、アクティビティ ログ分析ソリューションをサブスクリプションに追加できなくなります。 それは Resource Manager テンプレートで、次の手順に従って追加できます。 

1. 次の json を *ActivityLogTemplate*.json というファイルにコピーします。

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. 次の PowerShell コマンドを使用して、テンプレートをデプロイします。

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>次のステップ

- [アクティビティ ログ](platform-logs-overview.md)の詳細を確認します。
- [Azure Monitor データ プラットフォーム](data-platform.md)の詳細を確認します。
- [ログ クエリ](../log-query/log-query-overview.md)を使用して、アクティビティ ログの詳細情報を表示します。
