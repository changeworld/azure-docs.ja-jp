---
title: Azure portal を使用して Log Analytics ワークスペースを別の Azure リージョンに移動する
description: Azure portal を使用して Log Analytics ワークスペースを Azure リージョン間で移動するには、Azure Resource Manager テンプレートを使用します。
author: yossiy
ms.topic: how-to
ms.date: 08/17/2021
ms.author: yossiy
ms.openlocfilehash: 01d7852e514931a1546a9ab66c3b582bea115f2b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319626"
---
# <a name="move-a-log-analytics-workspace-to-another-region-by-using-the-azure-portal"></a>Azure portal を使用して Log Analytics ワークスペースを別のリージョンに移動する

既存の Log Analytics ワークスペースを、あるリージョンから別のリージョンに移動させるシナリオには、さまざまなものがあります。 たとえば、お客様のリソースのほとんどをホストしているリージョンで Log Analytics が最近利用できるようになったため、ワークスペースをより近くに移動して、エグレス料金を節約したい場合などです。 また、データ主権の要件により、新たに追加されたリージョンにワークスペースを移動したい場合もあります。

Log Analytics ワークスペースをリージョン間で移動することはできません。 ただし、Azure Resource Manager テンプレートを使用して、ワークスペース リソースと関連リソースをエクスポートすることはできます。 そこで、これらのリソースを別のリージョンにステージするには、ワークスペースをテンプレートにエクスポートし、移動先リージョンに合わせてパラメーターを変更してから、そのテンプレートを新しいリージョンにデプロイします。 Resource Manager とテンプレートの詳細については、「[クイック スタート: Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)」を参照してください。 

ワークスペース環境は、接続されたソース、マネージド ソリューション、リンク サービス、アラート、クエリ パックなどを含む複雑なものになることもあります。 すべてのリソースを Resource Manager テンプレートにエクスポートできるわけではないため、ワークスペースを移動する際には個別の構成が必要なものもあります。

## <a name="prerequisites"></a>前提条件

- ワークスペース構成を、別のリージョンにデプロイ可能なテンプレートにエクスポートするには、[Log Analytics 共同作成者](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)または[監視共同作成者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)ロール以上が必要です。

- 次のような、お使いのワークスペースに現在関連付けられているすべてのリソースを特定します。
  - *接続されたエージェント*: ワークスペースに「**ログ**」と入力し、[ハートビート](../insights/solution-agenthealth.md#azure-monitor-log-records) テーブルのクエリを実行して、接続されているエージェントを一覧表示します。
    ```kusto
    Heartbeat
    | summarize by Computer, Category, OSType, _ResourceId
    ```
  - *診断設定*: リソースでは、ワークスペース内の Azure Diagnostics または専用テーブルにログを送信できます。 ワークスペースに「**ログ**」と入力し、`AzureDiagnostics` テーブルにデータを送信するリソースに対して次のクエリを実行します。

    ```kusto
    AzureDiagnostics
    | where TimeGenerated > ago(12h)
    | summarize by  ResourceProvider , ResourceType, Resource
    | sort by ResourceProvider, ResourceType
    ```

    専用テーブルにデータを送信するリソースに対して、次のクエリを実行します。

    ```kusto
    search *
    | where TimeGenerated > ago(12h)
    | where isnotnull(_ResourceId)
    | extend ResourceProvider = split(_ResourceId, '/')[6]
    | where ResourceProvider !in ('microsoft.compute', 'microsoft.security')
    | extend ResourceType = split(_ResourceId, '/')[7]
    | extend Resource = split(_ResourceId, '/')[8]
    | summarize by tostring(ResourceProvider) , tostring(ResourceType), tostring(Resource)
    | sort by ResourceProvider, ResourceType
    ```

  - *インストール済みのソリューション*: インストール済みソリューションの一覧を表示するには、ワークスペース ナビゲーション ウィンドウの **[ソリューション]** を選択します。
  - *データ コレクター API*: [データ コレクター API](../logs/data-collector-api.md) 経由で着信したデータは、カスタム ログ テーブルに保存されます。 カスタム ログ テーブルの一覧を表示するには、ワークスペース ナビゲーション ウィンドウの **[ログ]** を選択してから、スキーマ ウィンドウの **[カスタム ログ]** を選択します。
  - *リンク サービス*: ワークスペースには、Azure Automation アカウント、ストレージ アカウント、専用クラスターなどの依存リソースへのリンク サービスが含まれている場合があります。 ワークスペースからリンク サービスを削除します。 ターゲット ワークスペース内にそれらを手動で再構成します。
  - *アラート*: アラートの一覧を表示するには、ワークスペース ナビゲーション ウィンドウの **[アラート]** を選択してから、ツール バーの **[アラート ルールの管理]** を選択します。 2019 年 6 月 1 日より後に作成されたワークスペース内、または[Log Analytics のアラート API から scheduledQueryRules API にアップグレードされた](../alerts/alerts-log-api-switch.md)ワークスペース内のアラートをテンプレートに含めることができます。 
  
     [scheduledQueryRules API がお使いのワークスペース内のアラートに使用されているかどうかを確認](../alerts/alerts-log-api-switch.md#check-switching-status-of-workspace)できます。 または、ターゲット ワークスペース内にアラートを手動で構成することもできます。
  - *クエリ パック*: ワークスペースは、複数のクエリ パックに関連付けることができます。 お使いのワークスペース内のクエリ パックを特定するには、ワークスペース ナビゲーション ウィンドウの **[ログ]** 、左側のウィンドウの **[クエリ]** 、検索ボックスの右側にある省略記号の順に選択します。 選択したクエリ パックを含むダイアログが右側に開きます。 移動するワークスペースが含まれているリソース グループにクエリ パックも含まれている場合は、この移動に含めることができます。
- お使いの Azure サブスクリプションで、ターゲット リージョンに Log Analytics ワークスペースを作成できることを確認します。

## <a name="prepare-and-move"></a>準備と移動
次の手順では、Resource Manager テンプレートを使用してワークスペースとリソースの移動に向けた準備を行い、その後でポータルを使用してそれらをターゲット リージョンに移動する方法を示します。 これらの手順は順番に実行してください。

> [!NOTE]
> 一部のリソースは、テンプレートを使用してエクスポートできません。 これらは、ターゲット リージョンにワークスペースを作成した後で個別に構成する必要があります。

### <a name="select-resource-groups-and-edit-parameters"></a>リソースグループを選択してパラメーターを編集する

1. [Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。
1. お使いのワークスペースが含まれているリソース グループを探し、それを選択します。
1. アラート リソースを表示するには、 **[非表示の型の表示]** を選択します。
1. **[種類]** フィルターを選択します。 **Log Analytics ワークスペース**、**ソリューション**、**SavedSearches**、**microsoft.insights/scheduledqueryrules**、**defaultQueryPack**、およびその他の含まれているワークスペース関連リソース (Automation など) を選択します。 次に、**[適用]** を選択します。
1. ワークスペース、ソリューション、保存された検索条件、アラート、クエリ パック、およびその他の含まれているワークスペース関連リソース (Automation など) を選択します。 次に、ツール バーの **[テンプレートのエクスポート]** を選択します。
    
    > [!NOTE]
    > Microsoft Sentinel は、テンプレートを使用してエクスポートできません。 ターゲット ワークスペースに [Sentinel をオンボードする](../../sentinel/quickstart-onboard.md)必要があります。
   
1. ツール バーの **[デプロイ]** を選択して、デプロイ用にテンプレートを編集して準備します。
1. ツール バーの **[パラメーターの編集]** を選択して、オンライン エディターで *parameters.json* ファイルを開きます。
1. パラメーターを編集するには、`parameters` の下の `value` プロパティを変更します。 次に例を示します。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaces_name": {
          "value": "my-workspace-name"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/resource-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/workspaces/workspace-name"
        },
        "alertName": {
          "value": "my-alert-name"
        },
        "querypacks_name": {
          "value": "my-default-query-pack-name"
        }
      }
    }
    ```

1. エディターで **[保存]** を選択します。

### <a name="edit-the-template"></a>テンプレートの編集

1. ツール バーの **[テンプレートの編集]** を選択して、オンライン エディターで *template.json* ファイルを開きます。
1. Log Analytics ワークスペースをデプロイするターゲット リージョンを編集するには、オンライン エディターで `resources` の下の `location` プロパティを変更します。 

   リージョンの場所コードを取得する場合は、「[Azure でのデータ所在地](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。 リージョンのコードは、スペースを含まないリージョン名です。 たとえば、**Central US** は `centralus` とする必要があります。
1. テンプレート内にリンク サービス リソース (`microsoft.operationalinsights/workspaces/linkedservices`) がある場合は削除します。 これらのリソースは、ターゲット ワークスペースに手動で再構成する必要があります。

   次のテンプレートの例には、ワークスペース、保存された検索、ソリューション、アラート、クエリ パックが含まれています。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaces_name": {
          "type": "String"
        },
        "workspaceResourceId": {
          "type": "String"
        },
        "alertName": {
          "type": "String"
        },
        "querypacks_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "type": "microsoft.operationalinsights/workspaces",
          "apiVersion": "2020-08-01",
          "name": "[parameters('workspaces_name')]",
          "location": "france central",
          "properties": {
            "sku": {
              "name": "pergb2018"
            },
            "retentionInDays": 30,
            "features": {
              "enableLogAccessUsingOnlyResourcePermissions": true
            },
            "workspaceCapping": {
              "dailyQuotaGb": -1
            },
            "publicNetworkAccessForIngestion": "Enabled",
            "publicNetworkAccessForQuery": "Enabled"
          }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
          "apiVersion": "2020-08-01",
          "name": "[concat(parameters('workspaces_name'), '/2b5112ec-5ad0-5eda-80e9-ad98b51d4aba')]",
          "dependsOn": [
            "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaces_name'))]"
          ],
          "properties": {
            "category": "VM Monitoring",
            "displayName": "List all versions of curl in use",
            "query": "VMProcess\n| where ExecutableName == \"curl\"\n| distinct ProductVersion",
            "tags": [],
            "version": 2
          }
        },
        {
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "2015-11-01-preview",
          "name": "[concat('Updates(', parameters('workspaces_name'))]",
          "location": "france central",
          "dependsOn": [
            "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]"
          ],
          "plan": {
            "name": "[concat('Updates(', parameters('workspaces_name'))]",
            "promotionCode": "",
            "product": "OMSGallery/Updates",
            "publisher": "Microsoft"
          },
          "properties": {
            "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]",
            "containedResources": [
              "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name')), '/views/Updates(', parameters('workspaces_name'), ')')]"
            ]
          }
        }
        {
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "2015-11-01-preview",
          "name": "[concat('VMInsights(', parameters('workspaces_name'))]",
          "location": "france central",
          "plan": {
            "name": "[concat('VMInsights(', parameters('workspaces_name'))]",
            "promotionCode": "",
            "product": "OMSGallery/VMInsights",
            "publisher": "Microsoft"
          },
          "properties": {
            "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]",
            "containedResources": [
              "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name')), '/views/VMInsights(', parameters('workspaces_name'), ')')]"
            ]
          }
        },
        {
          "type": "microsoft.insights/scheduledqueryrules",
          "apiVersion": "2021-08-01",
          "name": "[parameters('alertName')]",
          "location": "france central",
          "properties": {
            "displayName": "[parameters('alertName')]",
            "severity": 3,
            "enabled": true,
            "evaluationFrequency": "PT5M",
            "scopes": [
              "[parameters('workspaceResourceId')]"
            ],
            "windowSize": "PT15M",
            "criteria": {
              "allOf": [
                {
                  "query": "Heartbeat | where computer == 'my computer name'",
                  "timeAggregation": "Count",
                  "operator": "LessThan",
                  "threshold": 14,
                  "failingPeriods": {
                    "numberOfEvaluationPeriods": 1,
                    "minFailingPeriodsToAlert": 1
                  }
                }
              ]
            },
            "autoMitigate": true,
            "actions": {}
          }
        },
        {
          "type": "Microsoft.OperationalInsights/querypacks",
          "apiVersion": "2019-09-01-preview",
          "name": "[parameters('querypacks_name')]",
          "location": "francecentral",
          "properties": {}
        },
        {
          "type": "Microsoft.OperationalInsights/querypacks/queries",
          "apiVersion": "2019-09-01-preview",
          "name": "[concat(parameters('querypacks_name'), '/00000000-0000-0000-0000-000000000000')]",
          "dependsOn": [
            "[resourceId('Microsoft.OperationalInsights/querypacks', parameters('querypacks_name'))]"
          ],
          "properties": {
            "displayName": "my-query-name",
            "body": "my-query-text",
            "related": {
              "categories": [],
              "resourceTypes": [
                  "microsoft.operationalinsights/workspaces"
              ]
            },
            "tags": {
              "labels": []
            }
          }
        }
      ]
    }
    ```

1. オンライン エディターで **[保存]** を選択します。

### <a name="deploy-the-workspace"></a>ワークスペースをデプロイする

1. **[サブスクリプション]** を選択して、ターゲット ワークスペースをデプロイするサブスクリプションを選択します。
1. **[リソース グループ]** を選択して、ターゲット ワークスペースをデプロイするリソース グループを選択します。 **[新規作成]** を選択すると、ターゲット ワークスペース用の新しいリソース グループを作成できます。
1. **[リージョン]** が、ネットワーク セキュリティ グループをデプロイする移動先の場所に設定されていることを確認します。
1. **[確認および作成]** ボタンを選択してテンプレートを検証します。
1. **[作成]** を選択して、ワークスペースと選択したリソースをターゲット リージョンにデプロイします。
1. これで、選択したリソースを含むワークスペースがターゲット リージョンにデプロイされました。 元のワークスペースと同等の機能を持つように、ワークスペース内の残りの構成を完了できます。
   - *エージェントの接続*: 使用可能なオプション (データ収集ルールなど) を使用して、仮想マシンと仮想マシン スケール セット上に必要なエージェントを構成し、新しいターゲット ワークスペースを移動先として指定します。
   - *診断設定*: 特定されたリソースの診断設定を、移動先に指定されたターゲット ワークスペースで更新します。
   - *ソリューションのインストール*: [Microsoft Sentinel](../../sentinel/quickstart-onboard.md) などの一部のソリューションには特定のオンボード手順が必要なため、テンプレートには含まれていません。 これらは、新しいワークスペースに別途オンボードする必要があります。
   - *データ コレクター API の構成*: ターゲット ワークスペースにデータを送信するようにデータ コレクター API インスタンスを構成します。
   - *アラート ルールの構成*: テンプレートにアラートがエクスポートされない場合は、ターゲット ワークスペースに手動で構成する必要があります。
1. 新しいデータが元のワークスペースに取り込まれていないことを確認します。 元のワークスペースで次のクエリを実行し、移動後にインジェストが存在しないことを確認します。

    ```kusto
    search *
    | where TimeGenerated > ago(12h)
    | summarize max(TimeGenerated) by Type
    ```

データ ソースがターゲット ワークスペースに接続されると、取り込まれたデータはターゲット ワークスペースに格納されます。 古いデータは元のワークスペースに残り、アイテム保持ポリシーの対象となります。 [クロスワークスペース クエリ](./cross-workspace-query.md#performing-a-query-across-multiple-resources)を実行できます。 両方のワークスペースに同じ名前が割り当てられていた場合は、ワークスペースの参照で修飾名 (*subscriptionName/resourceGroup/componentName*) を使用できます。

同じ名前を持つ 2 つのワークスペース間のクエリの例を次に示します。

```kusto
union 
  workspace('subscription-name1/<resource-group-name1/<original-workspace-name>')Update, 
  workspace('subscription-name2/<resource-group-name2/<target-workspace-name>').Update, 
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="discard"></a>破棄

ソース ワークスペースを破棄する場合は、エクスポートしたリソース、またはこれらのリソースを含むリソース グループを削除します。

1. Azure portal でターゲット リソース グループを選択します。
1. **[概要]** ページで、次の操作を行います。
   
   - このデプロイ用に新しいリソース グループを作成した場合は、ツール バーの **[リソース グループの削除]** を選択して、そのリソース グループを削除します。
   - テンプレートが既存のリソース グループにデプロイされている場合は、テンプレートを使用してデプロイされたリソースを選択してから、ツール バーの **[削除]** を選択して、選択したリソースを削除します。

## <a name="clean-up"></a>クリーンアップ

新しいデータは新しいワークスペースに取り込まれていますが、元のワークスペース内の古いデータは残っていてクエリに使用でき、ワークスペースで定義されているアイテム保持ポリシーの対象になります。 ワークスペースを[またいでクエリを実行](./cross-workspace-query.md#performing-a-query-across-multiple-resources)するために古いデータが必要な限り、元のワークスペースを保持することをお勧めします。 

元のワークスペース内の古いデータにアクセスする必要がなくなった場合は、次の操作を行います。

1. Azure portal で元のリソース グループを選択します。 
1. 削除するリソースを選択してから、ツール バーの **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

この記事では、Log Analytics ワークスペースとその関連リソースをリージョン間で移動し、ソース リソースをクリーンアップしました。 リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。

- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM を別のリージョンに移動する](../../site-recovery/azure-to-azure-tutorial-migrate.md)
