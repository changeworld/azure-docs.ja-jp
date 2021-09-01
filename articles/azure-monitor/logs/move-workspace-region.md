---
title: Azure portal を使用して Log Analytics ワークスペースを別の Azure リージョンに移動する
description: Azure portal を使用して Log Analytics ワークスペースを Azure リージョン間で移動するには、Azure Resource Manager テンプレートを使用します。
author: yossiy
ms.topic: how-to
ms.date: 08/17/2021
ms.author: yossiy
ms.openlocfilehash: 0292559afefd983127980c036f4936433831f1e5
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326418"
---
# <a name="move-log-analytics-workspace-to-another-region-using-the-azure-portal"></a>Azure portal を使用して Log Analytics ワークスペースを別のリージョンに移動する

既存の Log Analytics ワークスペースを、あるリージョンから別のリージョンに移動させるシナリオには、さまざまなものがあります。 たとえば、お客様のリソースのほとんどをホストしているリージョンで Log Analytics が最近利用できるようになったため、ワークスペースをより近くに移動して、エグレス料金を節約したい場合などです。 また、データ主権の要件により、新たに追加されたリージョンにワークスペースを移動させたい場合もあります。

Log Analytics ワークスペースをリージョン間で移動させることはできません。 ただし、Azure Resource Manager テンプレートを使用することで、ワークスペース リソースと関連リソースをエクスポートすることはできます。 その後、ワークスペースをテンプレートにエクスポートすることで別のリージョンにリソースをステージし、移動先リージョンに合わせてパラメーターを変更してから、新しいリージョンにテンプレートをデプロイできます。 Resource Manager とテンプレートの詳細については、「[クイック スタート: Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)」を参照してください。 ワークスペース環境は、接続されたソース、マネージド ソリューション、リンク サービス、アラート、クエリ パックなどを含む複雑なものになることもあります。 すべてのリソースを Resource Manager テンプレートにエクスポートできるわけではないため、ワークスペースを移動させる際には個別の構成が必要なものもあります。

## <a name="prerequisites"></a>前提条件

- ワークスペース構成を、別のリージョンにデプロイ可能なテンプレートにエクスポートするには、[Log Analytics 共同作成者](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)または[監視共同作成者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)ロール以上が必要です。

- 次を含む、ワークスペースに現在関連付けられているすべてのリソースを特定します。
  - 接続されたエージェント -- ワークスペースに "*ログ*" と入力し、[ハートビート](../insights/solution-agenthealth.md#heartbeat-records) テーブルにクエリを実行して、接続されているエージェントを一覧表示します。
    ```kusto
    Heartbeat
    | summarize by Computer, Category, OSType, _ResourceId
    ```
  - インストールされているソリューション -- インストールされているソリューションの一覧を表示するには、ワークスペースのナビゲーション ウィンドウの **[ソリューション]** をクリックします
  - Data collector API -- [Data Collector API](../logs/data-collector-api.md) を通じて到着したデータは、カスタム ログ テーブルに保存されます。 カスタム ログ テーブルの一覧を表示するには、ワークスペースのナビゲーション ウィンドウで * **[ログ]** _ をクリックしてから、[スキーマ] ウィンドウで _ *[カスタム ログ]* * をクリックします。
  - リンク サービス -- ワークスペースには、Automation アカウント、ストレージ アカウント、専用クラスターなどの依存リソースにリンクされたサービスがある場合があります。 ワークスペースからリンク サービスを削除します。 これらは、ターゲット ワークスペースで手動で再構成する必要があります
  - アラート -- ワークスペースのナビゲーション ウィンドウで **[アラート]** をクリックしてから、ツール バーの **[アラート ルールの管理]** をクリックしてアラートを一覧表示します。 テンプレートには、2019 年 6 月 1 日より後に作成されたワークスペース、または[従来の Log Analytics アラート API から scheduledQueryRules API にアップグレードされた](../alerts/alerts-log-api-switch.md)ワークスペース内のアラートを含めることができます。 [scheduledQueryRules API がワークスペース内のアラートに使用されているかどうかを確認](../alerts/alerts-log-api-switch.md#check-switching-status-of-workspace)することができます。 または、ターゲット ワークスペースでアラートを手動で構成することもできます
  - クエリ パック -- ワークスペースは、複数のクエリ パックに関連付けることができます。 ワークスペース内のクエリ パックを特定するには、ワークスペースのナビゲーション ウィンドウの **[ログ]** 、左側のペインの **[クエリ]** の順にクリックします。その後、検索ボックスの右側にある省略記号をクリックすると、選択したクエリ パックを含むダイアログが右側に開き、さらに多くの設定を行うことができます。 移動するワークスペースが含まれているリソース グループにクエリ パックも含まれている場合は、この移動に含めることができます
- お使いの Azure サブスクリプションで、ターゲット リージョンに Log Analytics ワークスペースを作成できることを確認します

## <a name="prepare-and-move"></a>準備と移動
次の手順では、Resource Manager テンプレートを使用してワークスペースとリソースの移動に向けた準備を行い、ポータルを使用してターゲット リージョンに移動する方法を示します。 すべてのリソースをテンプレートを介してエクスポートできるわけではないため、ターゲット リージョンにワークスペースを作成した後に、これらは個別に設定する必要があります。

### <a name="export-the-template-and-deploy-from-the-portal"></a>テンプレートをエクスポートし、ポータルからデプロイする

1. [Azure portal](https://portal.azure.com)、 **[リソース グループ]** の順にログインします
2. ワークスペースが含まれているリソース グループを探し、それをクリックします
3. アラート リソースを表示するには、 **[非表示の型の表示]** を選択します
4. [種類] フィルターをクリックし、**Log Analytics ワークスペース**、**ソリューション**、**SavedSearches**、**microsoft.insights/scheduledqueryrules**、および **defaultQueryPack** (該当する場合) を選択し、[適用] をクリックします
5. 移動するワークスペース、ソリューション、アラート、保存された検索とクエリ パックを選択し、ツール バーの **[テンプレートのエクスポート]** をクリックします
    
    > [!NOTE]
    > Sentinel は、テンプレートを使用してエクスポートすることはできないため、ユーザーが Sentinel をターゲット ワークスペースに[オンボードする](../../sentinel/quickstart-onboard.md)必要があります。
   
6. ツール バーの **[デプロイ]** をクリックして、デプロイ用のテンプレートを編集して準備します
7. ツール バーの **[パラメーターの編集]** をクリックして、オンライン エディターで **parameters.json** ファイルを開きます
8. パラメーターを編集するには、**parameters** の **value** プロパティを変更します

パラメーター ファイルの例:

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

9. エディターで **[保存]** をクリックします
10. ツール バーの **[テンプレートの編集]** をクリックして、オンライン エディターで **template.json** ファイルを開きます
11. Log Analytics ワークスペースをデプロイするターゲット リージョンを編集するには、オンライン エディターで **resources** の下の **location** プロパティを変更します。 リージョンの場所コードを取得するには、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。 リージョンのコードは、スペースを含まないリージョン名です (**Central US** = **centralus**)
12. テンプレートにリンクされたサービス リソース `microsoft.operationalinsights/workspaces/linkedservices` がある場合は削除します。 これらは、ターゲット ワークスペースで手動で再構成する必要があります

ワークスペース、保存された検索、ソリューション、アラート、クエリ パックなどのテンプレートの例:

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
      "apiVersion": "2021-02-01-preview",
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

13. オンライン エディターで **[保存]** をクリックします
14. **[サブスクリプション]** をクリックして、ターゲット ワークスペースをデプロイするサブスクリプションを選択します
16. **[リソース グループ]** をクリックして、ターゲット ワークスペースをデプロイするリソース グループを選択します。 **[新規作成]** をクリックして、ターゲット ワークスペース用の新しいリソース グループを作成できます
17. **[リージョン]** が NSG をデプロイする移動先の場所に設定されていることを確認します
18. **[確認および作成]** ボタンをクリックしてテンプレートを検証します
19. **[作成]** クリックして、ワークスペースと選択したリソースをターゲット リージョンにデプロイします
20. 選択したリソースを含むワークスペースがターゲット リージョンにデプロイされたので、元のワークスペースと同等の機能を持つよう、ワークスペースの残りの構成に進むことができます
    - エージェントの接続 - DCR を含む使用可能なオプションを使用して、仮想マシンと仮想マシン スケール セットで必要なエージェントを構成し、新しいターゲット ワークスペースをターゲットとして指定します
    - ソリューションのインストール -- [Azure Sentinel](../../sentinel/quickstart-onboard.md) のような一部のソリューションには特定のオンボーディング手順が必要なため、テンプレートには含まれていません。 これらは、新しいワークスペースに別途オンボーディングする必要があります
    - Data collector API -- ターゲット ワークスペースにデータを送信する Data Collector API インスタンスを構成します
    - アラート ルール -- テンプレートにアラートがエクスポートされていない場合は、ターゲット ワークスペースで手動で設定する必要があります

## <a name="discard"></a>破棄

ソース ワークスペースを破棄する場合は、エクスポートしたリソース、またはこれらを含むリソース グループを削除します。 これを行うには、Azure portal でターゲット リソース グループを選択します。このデプロイ用に新しいリソース グループを作成した場合は、[概要] ページのツール バーにある **[リソース グループの削除]** をクリックします。 テンプレートが既存のリソース グループにデプロイされている場合は、テンプレートを使用してデプロイされたリソースを選択し、ツール バーの **[削除]** をクリックします。

## <a name="clean-up"></a>クリーンアップ

検証期間中は、ターゲット ワークスペースの機能が整うまでは、ワークスペースを含む元のリソース グループを維持することをお勧めします。 元の場所にあるリソースを破棄する場合は、Azure portal で元のリソース グループを選択し、削除するリソースを選択してからツール バーの **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Log Analytics ワークスペースとその関連リソースをあるリージョンから別のリージョンに移動させ、ソース リソースをクリーンアップしました。 リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。

- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM を別のリージョンに移動する](../../site-recovery/azure-to-azure-tutorial-migrate.md)