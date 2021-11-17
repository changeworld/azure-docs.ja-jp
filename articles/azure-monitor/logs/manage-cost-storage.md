---
title: Azure Monitor ログの使用量とコストを管理する
description: Azure Monitor で Log Analytics ワークスペースの料金プランを変更し、データ ボリュームとアイテム保持ポリシーを管理する方法について説明します。
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/17/2021
ms.author: bwren
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 37b6411dbddd92f9a0ba8ba8908b1f848d761683
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319702"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Azure Monitor ログで使用量とコストを管理する

> [!NOTE]
> この記事では、Azure Monitor ログにかかるコストを理解し、それを制御する方法について説明します。 関連記事の「[使用量と推定コストの監視](../usage-estimated-costs.md)」では、さまざまな価格モデルに対する複数の Azure 監視機能全体の使用量と推定コストを表示する方法について説明します。 この記事のすべての価格とコストは、単に例示を目的としています。 

Azure Monitor ログは、企業内のソースまたは Azure に展開されたソースから毎日大量のデータを収集し、インデックスを付けて、保存する処理をスケーリングおよびサポートするように設計されています。  これは組織の主要な原動力になる場合がありますが、最終的に基になる原動力はコスト効率です。 そのためには、Log Analytics ワークスペースのコストは、収集されるデータのボリュームだけでなく、選択されているプラン、および接続されたソースから生成されたデータの保持期間にも依存することを、理解しておくことが重要です。  

この記事では、取り込まれたデータ ボリュームとストレージの増加を事前に監視する方法について確認します。 また、関連するコストを制御する制限を定義する方法についても説明します。 

## <a name="pricing-model"></a>価格モデル

Log Analytics の既定の料金は、取り込まれたデータの量に基づく **従量課金制** であり、必要に応じてデータの保持期間を長くすることができます。 データ量は、GB (10^9 バイト) 単位で格納されるデータのサイズとして測定されます。 各 Log Analytics ワークスペースは個々のサービスとして課金され、Azure サブスクリプションの課金内容に加えられます。 データ インジェストの量は、次の要因に大きく依存する可能性があります。 

  - 有効にされている管理ソリューションのセットとその構成
  - 監視対象のリソースの数と種類
  - 各監視対象リソースから収集されたデータの種類
  
従量課金制モデルに加えて、Log Analytics には **コミットメント レベル** があります。これにより、従量課金制の料金と比較して 30% も節約できます。 コミットメント レベルの価格を使用すると、従量課金制の価格より低い価格でデータ インジェストを 100 GB/日から購入することを確約できます。 コミットメント レベルを超える使用量 (超過分) については、現在のコミットメント レベルで提供されている同じ GB あたりの価格で請求されます。 コミットメント レベルには、31 日間のコミットメント期間があります。 コミットメント期間中、より高いコミットメント レベルに変更できます (これにより 31 日間のコミットメント期間が再スタートされます) が、コミットメント期間が終了するまでは、従量課金制または低いコミットメント レベルに戻ることはできません。 コミットメント レベルの課金は 1 日単位で行われます。 Log Analytics の従量課金制とコミットメント レベルの価格については、[こちら](https://azure.microsoft.com/pricing/details/monitor/)をご覧ください。 

> [!NOTE]
> 2021 年 6 月 2 日以降、**容量予約** は **コミットメント レベル** に呼称が変更されました。 コミットメント レベルを超えて収集されたデータ (超過分) は、現在のコミットメント レベルと同じ GB あたりの料金で課金されるようになり、以前の従量課金制料金での課金方法と比較してコストが下がりました。また、大きなデータ ボリュームを持つユーザーがコミットメント レベルを微調整する必要性も低下しました。 1000、2000、および 5000 GB/日という 3 つの新しいコミットメント レベルも追加されました。 

すべての価格レベルにおいて、イベントのデータ サイズは、データがエージェントから送信されるか、インジェスト プロセス中に追加されるかに関係なく、Log Analytics に格納されているこのイベントのプロパティの文字列表記から計算されます。 これには、データが収集されて Log Analytics に格納されるときに追加されるすべての[カスタム フィールド](custom-fields.md)が含まれます。 一部の [Log Analytics 標準プロパティ](./log-standard-columns.md)を含む、すべてのデータ型に共通するいくつかのプロパティは、イベント サイズの計算から除外されます。 これには、`_ResourceId`、`_SubscriptionId`、`_ItemId`、`_IsBillable`、`_BilledSize`、および `Type` が含まれます。 Log Analytics に格納されている他のすべてのプロパティは、イベント サイズの計算に含まれます。 [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity)、[Heartbeat](/azure/azure-monitor/reference/tables/heartbeat)、[Usage](/azure/azure-monitor/reference/tables/usage)、[Operation](/azure/azure-monitor/reference/tables/operation) 型などの一部のデータ型に対しては、データ インジェスト料金は一切かかりません。 一部のソリューションでは、無料のデータ インジェストに関するソリューション固有のポリシーが他のソリューションより多くなっています。たとえば、[Azure Migrate](https://azure.microsoft.com/pricing/details/azure-migrate/) の場合、サーバー評価の最初の 180 日間、依存関係視覚化データが無料になります。 イベントがデータ インジェストの課金から除外されたかどうかを確認するには、[以下](#data-volume-for-specific-events)に示すように [_IsBillable](log-standard-columns.md#_isbillable) プロパティを使用できます。 使用量は GB (10^9 バイト) 単位で報告されます。 

また、[Microsoft Defender for Cloud](https://azure.microsoft.com/pricing/details/azure-defender/)、[Microsoft Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/)、[構成管理](https://azure.microsoft.com/pricing/details/automation/)などの一部のソリューションには、独自の価格モデルがあります。 

### <a name="log-analytics-dedicated-clusters"></a>Log Analytics 専用クラスター

[Log Analytics 専用クラスター](logs-dedicated-clusters.md)では、1 つのマネージド Azure Data Explorer クラスターにワークスペースのコレクションが集約され、[カスタマー マネージド キー](customer-managed-keys.md)などの高度なシナリオがサポートされます。 Log Analytics 専用クラスターでは、ワークスペースと同じコミットメントレベルの価格モデルが使用されます。ただし、クラスターには 1 日に少なくとも 500 GB のコミットメント レベルが必要です。 クラスターの従量課金制オプションはありません。 このクラスターのコミットメント レベルには、コミットメント レベルが上がった後に 31 日間のコミットメント期間があります。 コミットメント期間中は、コミットメント レベルを下げることはできませんが、上げることはいつでも可能です。 ワークスペースがクラスターに関連付けられている場合、それらのワークスペースのデータ インジェストの課金は、構成されているコミットメント レベルを使用してクラスター レベルで行われます。 詳細については、[Log Analytics クラスターの作成](customer-managed-keys.md#create-cluster)と、[それへのワークスペースの関連付け](customer-managed-keys.md#link-workspace-to-cluster)に関するページを参照してください。 コミットメント レベルの価格については、[Azure Monitor の価格ページ]( https://azure.microsoft.com/pricing/details/monitor/)をご覧ください。

クラスターのコミットメント レベルは、`Sku` の下にある `Capacity` パラメーターを使用して、Azure Resource Manager でプログラムによって構成されます。 `Capacity` は GB 単位で指定され、1 日あたり 500 GB、1,000 GB、2,000 GB、5,000 GB の値を設定できます。 コミットメント レベルを超える使用量 (超過分) については、現在のコミットメント レベルで提供されている同じ GB あたりの価格で請求されます。  詳細については、「[Azure Monitor のカスタマー マネージド キー](customer-managed-keys.md)」を参照してください。

クラスターでの使用については、2 つの課金モードがあります。 これらは、[クラスターの作成](logs-dedicated-clusters.md#create-a-dedicated-cluster)時に `billingType` パラメーターによって指定することも、作成後に設定することもできます。 次の 2 つのモードがあります。 

- **クラスター**: この場合 (既定)、取り込まれたデータに対してクラスター レベルで課金されます。 クラスターに関連付けられている各ワークスペースが取り込んだデータ量が集計され、クラスターの日次請求が計算されます。 [Microsoft Defender for Cloud](../../security-center/index.yml) からのノードごとの割り当ては、クラスター内のすべてのワークスペースのデータの集計に対するこの集計の前に、ワークスペース レベルで適用されます。 

- **ワークスペース**: クラスターのコミットメント レベル コストは、(各ワークスペースに対する [Microsoft Defender for Cloud](../../security-center/index.yml) からのノードごとの割り当てを考慮した後に) 各ワークスペースのデータ インジェスト ボリュームによって、クラスター内のワークスペースに比例して割り当てられます。1 日の間にクラスターに取り込まれたデータ ボリュームの合計がコミットメント レベルよりも少ない場合は、ワークスペースごとにコミットメント レベルとして取り込まれたデータに対して有効な 1 GB あたりのコミットメント レベル料金で課金され、コミットメント レベルの未使用分についてはクラスター リソースに課金されます。 1 日の間にクラスターに取り込まれたデータ ボリュームの合計がコミットメント レベルよりも多い場合は、ワークスペースごとに、その日に取り込まれたデータの割合に基づいてコミットメント レベルの割合で課金され、コミットメント レベルを超えて取り込まれたデータの割合についてワークスペースごとに課金されます。 1 日の間にワークスペースに取り込まれたデータ ボリュームの合計がコミットメント レベルを超えている場合、クラスター リソースには何も課金されません。

クラスター課金オプションでは、データ保有はワークスペースごとに課金されます。 クラスターが作成されると、ワークスペースがそのクラスターに関連付けられるかどうかに関係なく、クラスターの課金が開始されます。 クラスターに関連付けられたワークスペースには、独自の価格レベルはなくなります。

## <a name="estimating-the-costs-to-manage-your-environment"></a>ご利用の環境を管理するためのコストの見積もり 

Azure Monitor ログをまだ使用していない場合は、[Azure Monitor 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=monitor)を使用して、Log Analytics を使用する場合のコストを見積もることができます。 **[検索]** ボックスに「Azure Monitor」と入力し、表示された結果から Azure Monitor タイルを選択します。 ページを下にスクロールして **Azure Monitor** に移動し、 **[Log Analytics]** セクションを展開します。 ここで収集するデータを GB 単位で入力できます。 Azure Monitor ログを評価している場合は、独自の環境からのデータ統計を使用することができます。 [監視対象の VM の数](#understanding-nodes-sending-data)と[ご利用のワークスペースによって取り込まれているデータ量](#understanding-ingested-data-volume)を特定する方法については、以下を参照してください。 Log Analytics をまだ実行していない場合は、データ ボリュームを推定するためのガイダンスを次に示します。

1. **VM の監視:** 一般的な監視を有効にすると、監視対象の VM ごとに 1 GB から 3 GB のデータが毎月取り込まれます。 
2. **Azure Kubernetes Service (AKS) クラスターの監視:** 一般的な AKS クラスターを監視する必要があるデータ ボリュームの詳細については、[こちら](../containers/container-insights-cost.md#estimating-costs-to-monitor-your-aks-cluster)を参照してください。 次の[ベスト プラクティス](../containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost)に従って、AKS クラスターの監視コストを制御します。 
3. **アプリケーションの監視**: Azure Monitor 料金計算ツールには、アプリケーションの使用状況と Application インサイト データ ボリュームの統計分析に基づいて を使用するデータ ボリューム推定機能が含まれています。 料金計算ツールの [Application Insights] セクションで、[アプリケーション アクティビティに基づいてデータ量を見積もる] の横にあるスイッチを切り替えて、これを使用します。 

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>ご自分の Azure 請求書での Log Analytics の使用状況の表示 

特定の Log Analytics ワークスペースの課金対象の使用量を表示する最も簡単な方法は、ワークスペースの **概要** ページに移動し、ページ上部にある基本セクションの右上にある **[View Cost]\(価格の表示\)** をクリックすることです。 これにより、このワークスペースに対して既にスコープが設定されている Azure Cost Management + Billing のコスト分析が起動します。  

または、[Azure Cost Management + Billing](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=%2fazure%2fbilling%2fTOC.json) ハブから起動することもできます。 ここでは、"コスト分析" 機能を使用して、Azure リソースの経費を表示できます。 まず、Log Analytics の支出を追跡するために、"リソースの種類" によるフィルターを追加します (Log Analytics の場合は microsoft.operationalinsights/workspace、Log Analytics クラスターの場合は microsoft.operationalinsights/cluster)。 **[グループ化]** で **[測定カテゴリ]** または **[測定]** を選択します。 Microsoft Defender for Cloud、Microsoft Sentinel などのその他のサービスも、Log Analytics ワークスペース リソースに対して使用量の請求を行います。 サービス名へのマッピングを表示するには、グラフの代わりにテーブル ビューを選択できます。 

使用状況をさらに詳しく理解するために、[Azure portal から使用状況をダウンロード](../../cost-management-billing/understand/download-azure-daily-usage.md)することもできます。 ダウンロードしたスプレッドシートで、Azure リソースごとに (たとえば、Log Analytics ワークスペース)、1 日あたりの使用量を確認できます。 この Excel スプレッドシートでは、Log Analytics ワークスペースからの使用状況を検索できます。それには、まず、"測定カテゴリ" 列でフィルター処理を行って "Log Analytics"、"洞察と分析" (一部のレガシ価格レベルで使用)、"Azure Monitor" (コミットメント レベルの価格レベルで使用) を表示し、次に "インスタンス ID" 列に対するフィルター "contains workspace" または "contains cluster" (後者の場合は、Log Analytics クラスターの使用量を含める) を追加します。 使用量が [消費量] 列に表示され、各エントリの単位が [測定単位] 列に表示されます。 詳細については、「[個々の Azure サブスクリプションの課金書を確認する](../../cost-management-billing/understand/review-individual-bill.md)」を参照してください。 

## <a name="understand-your-usage-and-optimizing-your-pricing-tier"></a>使用量の把握と価格レベルの最適化
<a name="understand-your-usage-and-estimate-costs"></a>

使用量の傾向を知り、最も費用対効果の高いログ分析の価格レベルを選択するには、**ログ分析の使用量と推定コスト** を使用します。 これにより、各ソリューションで収集されたデータ量、保持されているデータ量、および最近のデータ インジェスト パターンに基づく各価格レベルのコストの見積もりを示します。 

:::image type="content" source="media/manage-cost-storage/usage-estimated-cost-dashboard-01.png" alt-text="使用量と推定コスト":::

データの詳細を確認するには、 **[使用量と推定コスト]** ページで、いずれかのグラフの右上隅にあるアイコンを選択します。 これで、このクエリを操作して使用状況の詳細を調査できます。  

:::image type="content" source="media/manage-cost-storage/logs.png" alt-text="ログ ビュー":::

**[使用量と推定コスト]** ページでは、該当の月のデータ ボリュームを確認できます。 これには、受信して Log Analytics ワークスペースに格納されたすべての課金対象データが含まれます。  
 
Log Analytics の課金は Azure の課金内容に加えられます。 Azure Portal の **[課金]** または [Azure Billing Portal](https://account.windowsazure.com/Subscriptions) で、Azure の課金内容の詳細を確認できます。  

## <a name="changing-pricing-tier"></a>価格レベルの変更

ワークスペースの Log Analytics 価格レベルを変更するには、

1. Azure portal で、ワークスペースから **[使用量と推定コスト]** を開きます。このワークスペースでは、使用可能な各価格レベルの一覧が表示されます。

2. それぞれの価格レベルについての推定コストを確認します。 この見積もりは、過去 31 日間の使用量に基づいているため、このコストの見積もりは、通常の使用状況を表す直近の 31 日間に依存します。 下の例では、過去 31 日間のデータ パターンに基づいて、このワークスペースのコストが 100 GB/日のコミットメント レベル (#2) と比較して従量課金制レベル (#1) ではどれほど低くなるかを確認することができます。  

:::image type="content" source="media/manage-cost-storage/pricing-tier-estimated-costs.png" alt-text="価格レベル":::
    
3. 過去 31 日間の使用量に基づいて推定コストを確認した後、価格レベルを変更する場合は、 **[選択 ]** を選択します。  

### <a name="changing-pricing-tier-via-arm"></a>ARM を使用した価格レベルの変更

また、価格レベルを設定する `sku` オブジェクトや、価格レベルが `capacityresrvation` の場合は `capacityReservationLevel` パラメーターを使用して、[Azure Resource Manager から価格レベルを設定](./resource-manager-workspace.md)することもできます。 (詳細については、[ARM を使用したワークスペース プロパティの設定](/azure/templates/microsoft.operationalinsights/2020-08-01/workspaces?tabs=json#workspacesku-object)に関する記事を参照してください)。次に示すのは、ワークスペースを 300 GB/日のコミットメント レベルに設定するサンプル Azure Resource Manager テンプレートです (Resource Manager では `capacityreservation` と呼ばれます)。 

```
{
  "$schema": https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#,
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "YourWorkspaceName",
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "location": "yourWorkspaceRegion",
      "properties": {
                    "sku": {
                      "name": "capacityreservation",
                      "capacityReservationLevel": 300
                    }
      }
    }
  ]
}
```

PowerShell でこのテンプレートを使用するには、[Azure Az PowerShell モジュールをインストール](/powershell/azure/install-az-ps)した後、`Connect-AzAccount` を使用して Azure にサインインし、`Select-AzSubscription -SubscriptionId YourSubscriptionId` を使用してワークスペースが含まれているサブスクリプションを選択し、(template.json という名前のファイルに保存されている) テンプレートを適用します。

```
New-AzResourceGroupDeployment -ResourceGroupName "YourResourceGroupName" -TemplateFile "template.json"
```

価格レベルを従量課金制などの他の値 (SKU では `pergb2018` と呼ばれます) に設定するには、`capacityReservationLevel` プロパティを省略します。 詳細については、[ARM テンプレートの作成](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)、[テンプレートへのリソースの追加](../../azure-resource-manager/templates/template-tutorial-add-resource.md)、[テンプレートの適用](../resource-manager-samples.md)に関する記事を参照してください。 

### <a name="tracking-pricing-tier-changes"></a>価格レベルの変更の追跡

ワークスペースの価格レベルの変更は、"ワークスペースの作成" という名前の操作を含むイベントと共に[アクティビティ ログ](../essentials/activity-log.md)に記録されます。 イベントの **[変更履歴]** タブの `properties.sku.name` 行には、新旧の価格レベルが表示されます。  ワークスペースの [アクティビティ ログ] オプションをクリックすると、特定のワークスペースを対象としたイベントが表示されます。 価格レベルの変更を監視するには、"ワークスペースの作成" 操作に対するアラートを作成します。 

## <a name="legacy-pricing-tiers"></a>レガシ価格レベル

2018 年 4 月 2 日に Log Analytics ワークスペースまたは Application Insights リソースが含まれていたか、2019 年 2 月 1 日より前に開始され、まだアクティブな Enterprise Agreement にリンクされているサブスクリプションは、レガシ価格レベル (**無料試用版**、**スタンドアロン (GB 単位)** 、および **ノードごと (OMS)** ) を引き続き利用できます。 無料試用版の価格レベルのワークスペースでは、毎日のデータ インジェストの上限が 500 MB になります ([Microsoft Defender for Cloud](../../security-center/index.yml) によって収集されるセキュリティ データの種類を除く)。また、データ保有は 7 日に制限されます。 無料試用版価格レベルは、評価目的専用です。 Free レベルに対して、SLA は提供されません。  スタンドアロンまたはノードごとの価格レベルのワークスペースには、30 日から 730 日までのユーザーが構成可能な保持が含まれています。

スタンドアロン価格レベルでの使用量は、取り込まれたデータ ボリューム単位で請求されます。 これは **Log Analytics** サービス上で報告され、メーターには "分析対象データ" という名前が付けられています。 

ノードごとの価格レベルは、1 時間単位の細分性で監視対象の VM (ノード) ごとに課金されます。 監視対象のノードごとに、ワークスペースには 1 日あたり 500 MB の課金されないデータが割り当てられます。 この割り当ては、1 時間ごとの細分性で計算され、毎日ワークスペース レベルで集計されます。 集計される 1 日のデータ割り当てを超えて取り込まれたデータは、データ超過分として GB 単位で課金されます。 請求書では、ワークスペースがノードごとの価格レベルの場合、サービスはログ分析の利用状況の **洞察と分析** になります。 使用量は、次の 3 つのメーターで報告されます。

- **ノード**: これは、ノード月の単位での監視対象ノード (VM) 数に対応した使用量です。
- **ノードごとの超過データ**: これは、集計されたデータの割り当てを超える取り込まれたデータの GB 数です。
- **ノードごとに含まれるデータ**: これは、集計されたデータの割り当ての範囲に含まれる取り込まれたデータ量です。 このメーターは、すべての価格レベルにワークスペースが存在する場合に、Microsoft Defender for Cloud によって提供される範囲のデータ量を示すためにも使用されます。

> [!TIP]
> ワークスペースで **ノードごとの** 価格レベルにアクセスできるが、従量課金制レベルよりもコストが低いかどうか知りたい場合は、[次のクエリを使用](#evaluating-the-legacy-per-node-pricing-tier)して、推奨事項を簡単に取得できます。 

2016 年 4 月より前に作成されたワークスペースの場合、**Standard** と **Premium** の価格レベルを引き続き利用できます。これらのデータ保有は、それぞれ 30 日と 365 日に固定されています。 **Standard** または **Premium** の価格レベルに新しいワークスペースを作成することはできません。ワークスペースをこれらのレベルから移動した場合、元に戻すことはできません。 これらの従来のレベルでの Azure 請求書のデータ インジェストのメーターは、"分析対象データ" と呼ばれています。

### <a name="legacy-pricing-tiers-and-microsoft-defender-for-cloud"></a>レガシ価格レベルと Microsoft Defender for Cloud

また、従来の Log Analytics レベルの使用と [Microsoft Defender for Cloud](../../security-center/index.yml) に対する使用量の請求方法の間にもいくつかの動作があります。 

- ワークスペースが従来の Standard レベルまたは Premium レベルの場合、Microsoft Defender for Cloud はノードごとではなく Log Analytics データ インジェストに対してのみ課金されます。
- ワークスペースが従来の Per Node レベルの場合、Microsoft Defender for Cloud は、現在の [Microsoft Defender for Cloud のノードベースの価格モデル](https://azure.microsoft.com/pricing/details/security-center/)を使用して課金されます。 
- 他の価格レベル (コミットメント レベルを含む) では、2017 年 6 月 19 日より前に Microsoft Defender for Cloud が有効になっている場合、Microsoft Defender for Cloud は Log Analytics データ インジェストに対してのみ課金されます。 それ以外の場合、Microsoft Defender for Cloud は、現在の Microsoft Defender for Cloud のノードベースの価格モデルを使用して課金されます。

価格レベルの制限に関する詳細は、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)」にあります。

レガシ価格レベルには、リージョンベースの価格はありません。  

> [!NOTE]
> OMS E1 Suite、OMS E2 Suite、または OMS Add-On for System Center のいずれかを購入することによって得られる資格を使用するには、OMS Log Analytics の *ノード単位* の価格レベルを選択します。

## <a name="log-analytics-and-microsoft-defender-for-cloud"></a>Log Analytics と Microsoft Defender for Cloud

[Microsoft Defender for Servers (Defender for Cloud)](../../security-center/index.yml) の課金は、Log Analytics の課金と密接に結び付けられています。 Microsoft Defender for Cloud では、[監視対象サービス数に応じて課金](https://azure.microsoft.com/pricing/details/azure-defender/)されます。また、Update Management ソリューションがワークスペースで実行されていないか、ソリューションのターゲット設定が有効にされている場合、[セキュリティ データの種類](/azure/azure-monitor/reference/tables/tables-category#security)のサブセット (WindowsEvent、SecurityAlert、SecurityBaseline、SecurityBaselineSummary、SecurityDetection、SecurityEvent、WindowsFirewall、MaliciousIPCommunication、LinuxAuditLog、SysmonEvent、ProtectionStatus)、および Update と UpdateSummary のデータの種類に対して適用される、毎日サーバーあたり 500 MB のデータ割り当てが提供されます ([詳細情報](../../security-center/security-center-pricing.md#what-data-types-are-included-in-the-500-mb-data-daily-allowance))。 監視対象サーバーの数は、1 時間単位の細分性で計算されます。 各監視対象サーバーからの毎日のデータ割り当ては、ワークスペース レベルで集計されます。 ワークスペースが従来の Per Node 価格レベルである場合、Microsoft Defender for Cloud と Log Analytics の割り当てが結合されて、取り込まれたすべての課金対象データにまとめて適用されます。  

## <a name="change-the-data-retention-period"></a>データ保持期間の変更

次の手順では、ワークスペースにログ データを保持する期間を構成する方法について説明します。 ワークスペース レベルでのデータ保持期間は、従来の無料試用版の価格レベルを使用している場合を除き、すべてのワークスペースで 30 ～ 730 日 (2 年) に構成できます。 個々のデータ型の保有期間は、最短 4 日に設定できます。 データ保持期間の延長の価格の[詳細](https://azure.microsoft.com/pricing/details/monitor/)を参照してください。 730 日を超えてデータを保持するには、[Log Analytics ワークスペースのデータのエクスポート](logs-data-export.md)を使用することを検討してください。

### <a name="workspace-level-default-retention"></a>ワークスペース レベルの既定の保有期間

ご利用のワークスペースに既定のリテンション期間を設定するには
 
1. Azure portal で、ご利用のワークスペースの左ウィンドウで **[使用量と推定コスト]** を選択します。
2. **[使用とコストの見積もり]** ページの上部にある **[データ保有期間]** を選択します。
3. ペインで、スライダーを動かして日数を増減してから、 **[OK]** を選択します。  *無料* プランをご利用の場合は、データ リテンション期間を変更できません。この設定を制御するには、有料プランにアップグレードする必要があります。

:::image type="content" source="media/manage-cost-storage/manage-cost-change-retention-01.png" alt-text="ワークスペースのデータ保持設定の変更":::

保持期間が短縮された場合、新しい保持設定よりも古いデータが削除されるまでには、数日の猶予期間があります。 

**[データ保有期間]** ページでは、30、31、60、90、120、180、270、365、550、および 730 日の保有期間設定を使用できます。 別の設定が必要な場合は、[Azure Resource Manager](./resource-manager-workspace.md) で `retentionInDays` パラメーターを使用して構成できます。 データ保持期間を 30 日に設定すると、`immediatePurgeDataOn30Days` パラメーターを使用して、(猶予期間を除去し) 古いデータの即時消去をトリガーできます。 これは、即時のデータ削除が不可欠なコンプライアンス関連のシナリオに役立ちます。 この即時消去機能は、Azure Resource Manager 経由でのみ公開されています。 

30 日の保持期間があるワークスペースでは、実際には 31 日間分のデータを保持することができます。 データを 30 日間しか保持しないことが不可欠な場合は、Azure Resource Manager を使用して保持期間を 30 日に設定したうえで、`immediatePurgeDataOn30Days` パラメーターを指定します。  

既定では、`Usage` と `AzureActivity` の 2 つのデータ型は、少なくとも 90 日間無料で保持されます。 ワークスペースの保持期間が 90 日を超えた場合、これらのデータの種類の保持期間も長くなります。 これらのデータの種類は、データ インジェスト料金の対象にもなりません。 

ワークスペースベースの Application Insights のリソース (`AppAvailabilityResults`、`AppBrowserTimings`、`AppDependencies`、`AppExceptions`、`AppEvents`、`AppMetrics`、`AppPageViews`、`AppPerformanceCounters`、`AppRequests`、`AppSystemEvents`、`AppTraces`) のデータの種類も既定では無料で 90 日間保持されます。 保持期間は、データの種類別のリテンション期間によって調整できます。 

Log Analytics [消去 API](/rest/api/loganalytics/workspacepurge/purge) はデータ保有の課金に影響せず、非常に限られたケースでの使用が意図されています。 データ保有の課金を減らすには、ワークスペースか特定のデータ型に対してリテンション期間を短縮する必要があります。 Log Analytics と Application インサイト に格納されている個人データの管理[の詳細を確認してください](./personal-data-mgmt.md)。

### <a name="retention-by-data-type"></a>データの種類別のリテンション期間

また、個々のデータの種類に対して、4 から 730 日でそれぞれ異なるリテンション期間を指定することもできます (従来の無料試用版の価格レベルのワークスペースを除きます)。これは、ワークスペース レベルの既定の保有期間をオーバーライドします。 それぞれのデータの種類は、ワークスペースのサブリソースです。 たとえば、SecurityEvent テーブルは [Azure Resource Manager](../../azure-resource-manager/management/overview.md) で次のようにアドレス指定できます。

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

データの種類 (テーブル) では大文字と小文字が区別されることに注意してください。 特定のデータ型 (この例では SecurityEvent) について、データ型別のリテンション期間の現在の設定を取得するには、次を使用します。

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

> [!NOTE]
> データ型のリテンション期間は、そのデータ型に対して明示的に設定されている場合にのみ返されます。 リテンション期間が明示的に設定されていない (そのため、ワークスペースのリテンション期間を継承する) データ型は、この呼び出しからは何も返しません。 

ご使用のワークスペース内でデータ型別のリテンション期間が設定されているすべてのデータ型について、データ型別のリテンション期間の現在の設定を取得するには、次のように特定のデータ型を単に省略します。

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

特定のデータの種類 (この例では SecurityEvent) のリテンション期間を 730 日に設定するには、次を使用します。

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

`retentionInDays` の有効な値は、4 から 730 です。

データの種類 `Usage` および `AzureActivity` は、カスタム リテンション期間を使用して設定することはできません。 これらは既定のワークスペース リテンション期間の最大値 (90 日) を取ります。 

Azure Resource Manager に直接接続してデータの種類別にリテンション期間を設定する優れたツールとして、OSS ツール [ARMclient](https://github.com/projectkudu/ARMClient) があります。  (ARMClient の詳細については、[David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) および Daniel Bowbyes による記事を参照してください。)  ARMClient を使用して、SecurityEvent データを 730 日のリテンション期間に設定する例を次に示します。

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> 各データの種類別にリテンション期間を設定すると、データ保有のコストを減らすことができます。 2019 年 10 月以降 (この機能がリリースされたタイミング) に収集されたデータで、リテンション期間を減らしたデータの種類では、時間が経つにつれデータ保有コストを減らすことができます。 それ以前に収集されたデータについては、種類ごとにリテンション期間の設定を減らしても、保有コストには影響しません。  

## <a name="manage-your-maximum-daily-data-volume"></a>ご自分のデータの 1 日の最大ボリュームを管理する

日次上限を構成して、ワークスペースの毎日のインジェストを制限できますが、1 日の上限に達することが目標ではないので注意する必要があります。 そうしないと、その日の残りの時間についてデータが失われ、ワークスペースで最新のデータが利用できることに依存する機能を持つ他の Azure サービスやソリューションに影響を与える可能性があります。 その結果、IT サービスをサポートするリソースの正常性状態を監視してアラートを受け取る機能が影響を受けます。 日次上限は、マネージド リソースからのデータ ボリュームの **予期しない増加** を管理して制限内に留めるため、またはワークスペースの計画外の料金を制限する手段として使うためにあります。 ワークスペース上での 1 日あたりに見合うように日次上限を設定するのは、適切ではありません。

各ワークスペースには、1 日の異なる時間に適用される 1 日の上限があります。 リセット時間は、 **[日次上限]** ページに表示されます (下記参照)。 このリセット時間は構成できません。 

日次上限に達するとすぐに、課金対象のデータの種類の収集は、その日はそれ以上行われません。 (日次上限の適用には待ち時間が内在するため、指定された日次上限のレベルは正確には適用されません)。 選択した Log Analytics ワークスペースのページの上部に警告バナーが表示され、**LogManagement** カテゴリの *Operation* テーブルに操作イベントが送信されます。 [*1 日の上限を次に設定する*] で定義されているリセット時刻が過ぎると、データ収集が再開されます。 この操作イベントに基づいてアラート ルールを定義し、データの日次制限に達したら通知するよう構成することをお勧めします。 詳細については、「[日次上限に達したときにアラートを表示する](#alert-when-daily-cap-is-reached)」セクションを参照してください。 

> [!NOTE]
> 特にワークスペース上で大量のデータを受信している場合には、日次上限によって、指定された上限レベルで厳密にデータ収集を停止することはできず、一部のデータが超過することが予想されます。 データが上限を超えて収集された場合でも、請求は発生します。 日次上限の動作の確認に役立つクエリについては、この記事の「[日次上限の効果を表示する](#view-the-effect-of-the-daily-cap)」セクションを参照してください。 

> [!WARNING]
> 日次上限によって、データの種類 (**WindowsEvent**、**SecurityAlert**、**SecurityBaseline**、**SecurityBaselineSummary**、**SecurityDetection**、**SecurityEvent**、**WindowsFirewall**、**MaliciousIPCommunication**、**LinuxAuditLog**、**SysmonEvent**、**ProtectionStatus**、**Update** および **UpdateSummary**) の収集が停止することはありません。ただし、2017 年 6 月 19 日より前に Microsoft Defender for Cloud がインストールされたワークスペースは除きます。 

### <a name="identify-what-daily-data-limit-to-define"></a>定義する日次データ制限を明らかにする

データ インジェストの傾向および定義する日次データ ボリューム上限について理解するには、[Log Analytics の使用量と推定コスト](../usage-estimated-costs.md)に関する記事を参照してください。 制限に達した後にリソースを監視することはできないため、慎重に検討してください。 

### <a name="set-the-daily-cap"></a>1 日の上限を設定する

次の手順では、Log Analytics ワークスペースが 1 日に取り込むデータのボリュームを管理する制限を構成する方法について説明します。  

1. ワークスペースの左ウィンドウで **[使用量と推定コスト]** を選択します。
2. 選択したワークスペースの **[使用量と推定コスト]** ページの上部にある **[データ上限]** を選択します。 
3. 既定では、 **[日次上限]** は **[OFF]** に設定されています。 有効にするには、 **[ON]** を選択し、GB/日単位でデータ ボリュームの制限を設定します。

:::image type="content" source="media/manage-cost-storage/set-daily-volume-cap-01.png" alt-text="Log Analytics のデータ制限の構成":::

Azure Resource Manager を使用して、日次上限を構成することができます。 これを構成するには、「[ワークスペース-作成または更新](/rest/api/loganalytics/workspaces/createorupdate#workspacecapping)」で説明されているように、`WorkspaceCapping` で `dailyQuotaGb` パラメーターを設定します。 

このクエリを使用して、1 日あたりの上限に加えられた変更を追跡できます。

```kusto
_LogOperation | where Operation == "Workspace Configuration" | where Detail contains "Daily quota"
```

_LogOperation 関数の詳細については、[こちら](./monitor-workspace.md)を参照してください。 

### <a name="view-the-effect-of-the-daily-cap"></a>日次上限の効果を表示する

日次上限の効果を表示する際に重要な点は、日次上限に含まれていないセキュリティ データの種類と、ワークスペースのリセット時間を考慮することです。 日次上限のリセット時間は、 **[日次上限]** ページに表示されます。 次のクエリを使用すると、日次上限のリセット間で日次上限の対象となるデータ ボリュームを追跡できます。 この例では、ワークスペースのリセット時間は 14:00 です。 この時間は、ワークスペース用に更新する必要があります。

```kusto
let DailyCapResetHour=14;
Usage
| where DataType !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend TimeGenerated=datetime_add("hour",-1*DailyCapResetHour,TimeGenerated)
| where TimeGenerated > startofday(ago(31d))
| where IsBillable
| summarize IngestedGbBetweenDailyCapResets=sum(Quantity)/1000. by day=bin(TimeGenerated, 1d) // Quantity in units of MB
| render areachart  
```
ワークスペースで Update Management ソリューションが実行されていないか、ソリューションのターゲット設定が有効になっている場合は、`Update` および `UpdateSummary` データ型を `where Datatype` 行に追加します ([詳細はこちら](../../security-center/security-center-pricing.md#what-data-types-are-included-in-the-500-mb-data-daily-allowance)。)


### <a name="alert-when-daily-cap-is-reached"></a>1 日の上限に達したら警告する

データ制限のしきい値に達すると Azure Portal に視覚的な合図が表示されますが、この動作は、早急な措置を必要とする運用上の問題を管理する方法と、必ずしも一致していない場合があります。 アラート通知を受け取るには、Azure Monitor で新しいアラート ルールを作成します。 詳細については、[アラートを作成、表示、管理する方法](../alerts/alerts-metric.md)に関するページをご覧ください。

はじめに、`_LogOperation` 関数 ([詳細情報](./monitor-workspace.md)) を使用して、`Operation` テーブルのクエリを実行するアラートの推奨される設定を次に示します。 

- ターゲット:お客様の Log Analytics リソースを選択します
- 条件: 
   - シグナル名: カスタム ログ検索
   - 検索クエリ: `_LogOperation | where Operation =~ "Data collection stopped" | where Detail contains "OverQuota"`
   - ベース: 結果の数
   - 条件: より大きい
   - しきい値: 0
   - 期間: 5 (分)
   - 頻度: 5 (分)
- アラート ルール名: 1 日のデータ制限に達しました
- 重大度: 警告 (重大度 1)

アラートを定義した後で制限に達すると、アラートがトリガーされ、アクション グループで定義されている応答が実行されます。 次の方法でチームに通知できます。

- 電子メールとテキスト メッセージ
- Webhook を使用した自動アクション
- Azure Automation Runbook
- [外部の ITSM ソリューションと統合](../alerts/itsmc-definition.md#create-itsm-work-items-from-azure-alerts)されます。 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>使用量が予想よりも多い理由のトラブルシューティング

使用量が多くなる原因は、次のいずれかまたは両方です。
- 予想よりも多くのノードが Log Analytics ワークスペースにデータを送信している 詳細については、この記事のセクション「[データを送信するノードについて理解する](#understanding-nodes-sending-data)」を参照してください。
- 予想よりも多くのデータが Log Analytics ワークスペースに送信されている (新しいソリューションの使用を開始したか、既存のソリューションの構成を変更したことが原因である可能性があります)。 詳細については、この記事のセクション「[取り込まれたデータ ボリュームについて理解する](#understanding-ingested-data-volume)」を参照してください。

`Usage` レコードを使用して多数のデータ インジェストが報告されている (「[ソリューション別のデータ ボリューム](#data-volume-by-solution)」を参照) が、[データ型](#data-volume-for-specific-events)で直接 `_BilledSize` を合計したときに同じ結果が確認できない場合は、大幅に到着が遅延しているデータがある可能性があります。 これを診断する方法の詳細については、この記事の「[到着遅延データ](#late-arriving-data)」セクションを参照してください。 

### <a name="log-analytics-workspace-insights"></a>Log Analytics Workspace Insights

[Log Analytics Workspace Insights ブック](log-analytics-workspace-insights-overview.md)の **[使用量]** タブで、データ ボリュームの理解を開始します。 **[使用量]** ダッシュボードでは、次のことを簡単に確認できます。
- メイン テーブルの最も多くのデータ ボリュームを取り込んでいるデータ テーブルはどれか  
- データに貢献している最上位のリソースはどれか 
- データ インジェストの傾向は何か

**[追加のクエリ]** に切り替えると、データ パターンを理解するのに役立つクエリをもっと多く簡単に実行できます。 

[[使用量]](log-analytics-workspace-insights-overview.md#usage-tab) タブの機能の詳細を参照してください。 

このブックでは、クエリを実行したり、さらに具体的な質問に回答したり、より詳細な分析を行ったりすることなく、多くの質問に答えることができますが、次の 2 つのセクションのクエリは、作業を開始するのに役立ちます。 

## <a name="understanding-nodes-sending-data"></a>データを送信するノードについて理解する

過去 1 か月間の日単位のエージェントからのハートビートを報告するノード数を把握するには、次のクエリを使用します。

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
過去 24 時間でデータを送信したノードの数を取得するには、次のクエリを使用します。 

```kusto
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

データを送信するノードの一覧 (およびそれぞれによって送信されたデータの量) を取得するには、次のクエリを使用できます。

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

### <a name="nodes-billed-by-the-legacy-per-node-pricing-tier"></a>レガシのノードごとの価格レベルによって課金されるノード

[レガシのノードごとの価格レベル](#legacy-pricing-tiers)では、1 時間ごとの細分性でノードに課金され、一連のセキュリティ データの種類のみを送信するノードはカウントされません。 その毎日のノードのカウントは、次のクエリに近くなります。

```kusto
find where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now()) project Computer, _IsBillable, Type, TimeGenerated
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| where _IsBillable == true
| summarize billableNodesPerHour=dcount(computerName) by bin(TimeGenerated, 1h)
| summarize billableNodesPerDay = sum(billableNodesPerHour)/24., billableNodeMonthsPerDay = sum(billableNodesPerHour)/24./31.  by day=bin(TimeGenerated, 1d)
| sort by day asc
```

請求書の単位数は、クエリで `billableNodeMonthsPerDay` によって表されるノード月の単位になります。 ワークスペースに Update Management ソリューションがインストールされている場合は、上記のクエリの where 句の一覧に **Update** および **UpdateSummary** データの種類を追加します。 最後に、上記のクエリで表されていないソリューションのターゲット設定を使用した場合に、実際の課金アルゴリズムにいくらかの複雑さが加わります。 


> [!TIP]
> 複数の種類のデータにわたるスキャンは、実行に[多量のリソースを使うため](./query-optimization.md#query-performance-pane)、これらの `find` クエリは多用しないようにします。 **コンピューターごと** の結果が不要な場合は、**Usage** データ型に関するクエリを実行します (下記参照)。

## <a name="understanding-ingested-data-volume"></a>取り込まれたデータ ボリュームについて理解する

**[使用量と推定コスト]** ページの *[ソリューションごとのデータの取り込み]* グラフに、送信されたデータの総量と各ソリューションによって送信されている量が表示されます。 全体的なデータ使用量 (または、特定のソリューションによる使用量) が、増加しているか、一定しているか、減少しているかといった傾向を確認できます。 

### <a name="data-volume-for-specific-events"></a>特定のイベントのデータ量

特定のイベント セット用に取り込まれたデータのサイズを確認するには、特定のテーブル (この例では `Event`) に対してクエリを実行してから、目的のイベントにクエリを制限します (この例では、イベント ID 5145 または 5156)。

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
```

句 `where _IsBillable = true` は、取り込み料金がかからない特定のソリューションからのデータの種類を除外することに注意してください。 `_IsBillable` に関する[詳細情報](./log-standard-columns.md#_isbillable)を参照してください。

### <a name="data-volume-by-solution"></a>ソリューション別のデータ ボリューム

過去 1 か月間 (まだ終わっていない最終日を除く) の課金対象データ ボリュームをソリューション別に表示するために使用するクエリは、[Usage](/azure/azure-monitor/reference/tables/usage) データ型を使用して作成できます。

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution 
| render columnchart
```

`TimeGenerated` を含む句は、Azure portal のクエリ機能が既定の 24 時間を超えてさかのぼって参照するようにするためだけのものです。 **Usage** データ型を使用する場合、`StartTime` と `EndTime` は、結果が表示される時間バケットを表します。 

### <a name="data-volume-by-type"></a>種類別のデータ ボリューム

さらにドリルダウンして、データの種類別にデータの傾向を確認できます。

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType 
| render columnchart
```

または、過去 1 か月について、ソリューション別および種類別にテーブルを表示するには、次のようにします

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000 by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>コンピューターごとのデータ ボリューム

**Usage** データ型には、コンピューター レベルの情報は含まれていません。 コンピューターごとに、取り込まれた課金対象データの **サイズ** を表示するには **_BilledSize** [プロパティ](./log-standard-columns.md#_billedsize)を使用します。サイズはバイト単位で示されます。

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer, Type
| where _IsBillable == true and Type != "Usage"
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes desc nulls last
```

**_IsBillable** [プロパティ](./log-standard-columns.md#_isbillable)では、取り込まれたデータで課金が発生するかどうかを指定します。 **Usage** 型は、データの傾向を分析するためにのみ使用されるため、省略されています。 

コンピューターごとに取り込まれた請求対象のイベントの **数** を表示するには、次を使用します 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true and Type != "Usage"
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount desc nulls last
```

> [!TIP]
> 複数の種類のデータにわたるスキャンは、実行に[多量のリソースを使うため](./query-optimization.md#query-performance-pane)、これらの `find` クエリは多用しないようにします。 **コンピューターごと** の結果が不要な場合は、**Usage** データ型に関するクエリを実行します。

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Azure リソース、リソース グループ、またはサブスクリプションごとのデータ ボリューム

__コンピューターごと__ に取り込まれたデータの **サイズ** を取得できる Azure でホストされているノードからのデータについては、次のようにリソースへの完全パスを提供する [_ResourceId プロパティ](./log-standard-columns.md#_resourceid)を使用します。

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

Azure でホストされているノードからのデータについては、次のように **_SubscriptionId** プロパティを使用して __Azure サブスクリプションごと__ に取り込まれたデータの **サイズ** を取得できます。

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, _SubscriptionId
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _SubscriptionId | sort by BillableDataBytes nulls last
```

リソース グループ別にデータ量を取得するには、 **_ResourceId** を解析できます。

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

必要に応じて、 **_ResourceId** をより詳細に解析することもできます。

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> 複数の種類のデータにわたるスキャンは、実行に[多量のリソースを使うため](./query-optimization.md#query-performance-pane)、これらの `find` クエリは多用しないようにします。 サブスクリプション、リソース グループ、またはリソース名ごとの結果が不要な場合は、**Usage** データ型に関するクエリを実行します。

> [!WARNING]
> **Usage** データ型の一部のフィールドは、スキーマにはまだありますが非推奨とされていて、その値が設定されなくなっています。 これらは、**Computer** と、取り込みに関連するフィールド (**TotalBatches**、**BatchesWithinSla**、**BatchesOutsideSla**、**BatchesCapped**、および **AverageProcessingTimeMs**) です。

## <a name="late-arriving-data"></a>到着遅延データ

古いタイムスタンプでデータが取り込まれる状況が発生することがあります。 たとえば、通信に問題があるためにエージェントが Log Analytics と通信できない場合や、ホストの時刻や日時が正しくない場合があります。 これは、**Usage** データ型によって報告される取り込まれたデータと、イベントが生成されたタイムスタンプである **TimeGenerated** によって指定された特定の日の生データの **_BilledSize** を合計するクエリとの間の明らかな不一致によって示される可能性があります。

到着遅延データの問題を診断するには、**TimeGenerated** 列に加えて、 **_TimeReceived** 列 ([詳細情報](./log-standard-columns.md#_timereceived)) を使用します。 **_TimeReceived** は、そのレコードが Azure クラウド内の Azure Monitor インジェスト ポイントによって受信された時刻です。 たとえば、**Usage** レコードを使用しているとき、2021 年 5 月 2 日に **W3CIISLog** データの大量の取り込みデータ ボリュームを観察した場合、この取り込まれたデータのタイムスタンプを識別するクエリは次のようになります。 

```Kusto
W3CIISLog
| where TimeGenerated > datetime(1970-01-01)
| where _TimeReceived >= datetime(2021-05-02) and _TimeReceived < datetime(2021-05-03) 
| where _IsBillable == true
| summarize BillableDataMB = sum(_BilledSize)/1.E6 by bin(TimeGenerated, 1d)
| sort by TimeGenerated asc 
```

`where TimeGenerated > datetime(1970-01-01)` ステートメントは、すべてのデータを調べる手掛かりを Log Analytics ユーザー インターフェイスに提供する目的でのみ存在します。  

## <a name="querying-for-common-data-types"></a>一般的なデータ型のクエリを実行する

特定のデータ型のデータのソースについてさらに詳しく調べるための、いくつかの便利なクエリの例を次に示します。

+ **ワークスペース ベースの Application Insights** リソース
  - 詳細は「[Application Insights の使用量とコストを管理する](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources)」にあります
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

## <a name="tips-for-reducing-data-volume"></a>データ量の削減のためのヒント

収集されるログの量を削減するためのいくつかの提案は、次の表のとおりです。

| データ量の多いソース | データ量を削減する方法 |
| -------------------------- | ------------------------- |
| データ収集ルール      | [Azure Monitor エージェント](../agents/azure-monitor-agent-overview.md)は、データ収集ルールを使用してデータの収集を管理します。 カスタム XPath クエリを使用して、[データの収集を制限](../agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries)することができます。 | 
| Container Insights         | 必要なデータのみを収集するように [Container Insights を構成](../containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost)します。 |
| Microsoft Sentinel | 追加のデータ ボリュームのソースとして最近有効にした [Sentinel データ ソース](../../sentinel/connect-data-sources.md)を確認します。 Sentinel のコストと課金の詳細については、[こちらをご覧](../../sentinel/azure-sentinel-billing.md)ください。 |
| セキュリティ イベント            | [一般的または最小限のセキュリティ イベント](../../security-center/security-center-enable-data-collection.md#data-collection-tier)を選択します。 <br> 必要なイベントのみを収集するようにセキュリティ監査ポリシーを変更します。 特に、次のイベントを収集する必要性を検討します。 <br> - [フィルタリング プラットフォームの監査](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772749(v=ws.10))。 <br> - [レジストリの監査](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))。 <br> - [ファイル システムの監査](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))。 <br> - [カーネル オブジェクトの監査](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))。 <br> - [ハンドル操作の監査](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))。 <br> - リムーバブル記憶域の監査。 |
| パフォーマンス カウンター       | [パフォーマンス カウンターの構成](../agents/data-sources-performance-counters.md)を次のように変更します。 <br> - 収集の頻度を減らす。 <br> - パフォーマンス カウンターの数を減らす。 |
| イベント ログ                 | [イベント ログの構成](../agents/data-sources-windows-events.md)を次のように変更します。 <br> - 収集対象のイベント ログの数を減らす。 <br> - 必要なイベント レベルのみを収集する たとえば、"*情報*" レベルのイベントを収集しないようにします。 |
| syslog                     | [syslog の構成](../agents/data-sources-syslog.md)を次のように変更します。 <br> - 収集対象の施設の数を減らす。 <br> - 必要なイベント レベルのみを収集する たとえば、"*情報*" と "*デバッグ*" レベルのイベントを収集しないようにします。 |
| AzureDiagnostics           | [リソース ログの収集](../essentials/diagnostic-settings.md#create-in-azure-portal)を次のように変更します。 <br> - Log Analytics へのリソース送信ログの数を減らす。 <br> - 必要なログのみを収集する。 |
| ソリューションを必要としないコンピューターからのソリューション データ | [ソリューションのターゲット設定](../insights/solution-targeting.md)を使用して、必要なコンピューター グループからのみデータを収集するようにします。 |
| Application Insights | [Application Insights のデータ ボリュームを管理する](../app/pricing.md#managing-your-data-volume)ためのオプションを確認します。 |
| [SQL Analytics](../insights/azure-sql.md) | [AzSqlServerAudit](/powershell/module/az.sql/set-azsqlserveraudit) を使用して、監査設定を調整します。 |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>ノードごとの価格レベルでの課金対象のノードの取得

ワークスペースが "ノードごと" のレガシ価格レベルである場合に、ノードとして課金対象になるコンピューターの一覧を取得するには、**課金されるデータの種類** を送信しているノードを探します (一部のデータの種類は無料です)。 これを行うには、[_IsBillable プロパティ](./log-standard-columns.md#_isbillable)を使用し、完全修飾ドメイン名の左端のフィールドを使用します。 これにより、1 時間あたりの課金対象のデータがあるコンピューターの数が返されます (ノードがカウントされて課金される粒度)。

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>セキュリティ ノード数と Automation ノード数を取得する

個別のセキュリティ ノードの数を表示するには、次のクエリを使用できます。

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

個別の Automation ノードの数を表示するには、次のクエリを使用します。

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>従来のノードごとの価格レベルの評価

従来の **ノードごとの** 価格レベルにアクセスできるワークスペースで、そのレベルのほうが適しているのか、または現在の **従量課金制** または **コミットメント レベル** のほうが適しているのかの判断は、多くの場合、お客様が評価するのは容易ではありません。  これには、ノードごとの価格レベルでの監視対象ノードあたりの固定費とそれに含まれる 500 MB/ノード/日のデータ割り当てと、従量課金制 (GB 単位) レベルでの取り込まれたデータのみに対して支払うコストとの間のトレードオフを理解することが伴います。 

この評価を容易にするために、次のクエリを使用して、ワークスペースの使用パターンに基づいて最適な価格レベルの推奨設定を作成できます。 このクエリでは、監視対象ノードとワークスペースに取り込まれた過去 7 日間のデータに注目し、各日でどの価格レベルが最適であるかが評価されます。 クエリを使用するには、以下を指定する必要があります。

- ワークスペースが Microsoft Defender for Cloud を使用するかどうかを、**workspaceHasSecurityCenter** を **true** または **false** に設定して指定する。 
- 特定の割引がある場合は価格を更新する。
- **daysToEvaluate** を設定し、さかのぼって分析する日数を指定する。 これは、クエリで 7 日間のデータの検索に時間がかかりすぎている場合に便利です。

価格レベルの推奨クエリは次のとおりです。

```kusto
// Set these parameters before running query
// Pricing details available at https://azure.microsoft.com/pricing/details/monitor/
let daysToEvaluate = 7; // Enter number of previous days to analyze (reduce if the query is taking too long)
let workspaceHasSecurityCenter = false;  // Specify if the workspace has Defender for Cloud (formerly known as Azure Security Center)
let PerNodePrice = 15.; // Enter your monthly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let CommitmentTier100Price = 196.; // Enter your price for the 100 GB/day commitment tier
let CommitmentTier200Price = 368.; // Enter your price for the 200 GB/day commitment tier
let CommitmentTier300Price = 540.; // Enter your price for the 300 GB/day commitment tier
let CommitmentTier400Price = 704.; // Enter your price for the 400 GB/day commitment tier
let CommitmentTier500Price = 865.; // Enter your price for the 500 GB/day commitment tier
let CommitmentTier1000Price = 1700.; // Enter your price for the 1000 GB/day commitment tier
let CommitmentTier2000Price = 3320.; // Enter your price for the 2000 GB/day commitment tier
let CommitmentTier5000Price = 8050.; // Enter your price for the 5000 GB/day commitment tier
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend billableGB = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)), DataGB )
| extend PerGBDailyCost = billableGB * PerGBPrice
| extend CommitmentTier100DailyCost = CommitmentTier100Price + max_of(billableGB - 100, 0.)* CommitmentTier100Price/100.
| extend CommitmentTier200DailyCost = CommitmentTier200Price + max_of(billableGB - 200, 0.)* CommitmentTier200Price/200.
| extend CommitmentTier300DailyCost = CommitmentTier300Price + max_of(billableGB - 300, 0.)* CommitmentTier300Price/300.
| extend CommitmentTier400DailyCost = CommitmentTier400Price + max_of(billableGB - 400, 0.)* CommitmentTier400Price/400.
| extend CommitmentTier500DailyCost = CommitmentTier500Price + max_of(billableGB - 500, 0.)* CommitmentTier500Price/500.
| extend CommitmentTier1000DailyCost = CommitmentTier1000Price + max_of(billableGB - 1000, 0.)* CommitmentTier1000Price/1000.
| extend CommitmentTier2000DailyCost = CommitmentTier2000Price + max_of(billableGB - 2000, 0.)* CommitmentTier2000Price/2000.
| extend CommitmentTier5000DailyCost = CommitmentTier5000Price + max_of(billableGB - 5000, 0.)* CommitmentTier5000Price/5000.
| extend MinCost = min_of(
    PerNodeDailyCost,PerGBDailyCost,CommitmentTier100DailyCost,CommitmentTier200DailyCost,
    CommitmentTier300DailyCost, CommitmentTier400DailyCost, CommitmentTier500DailyCost, CommitmentTier1000DailyCost, CommitmentTier2000DailyCost, CommitmentTier5000DailyCost)
| extend Recommendation = case(
    MinCost == PerNodeDailyCost, "Per node tier",
    MinCost == PerGBDailyCost, "Pay-as-you-go tier",
    MinCost == CommitmentTier100DailyCost, "Commitment tier (100 GB/day)",
    MinCost == CommitmentTier200DailyCost, "Commitment tier (200 GB/day)",
    MinCost == CommitmentTier300DailyCost, "Commitment tier (300 GB/day)",
    MinCost == CommitmentTier400DailyCost, "Commitment tier (400 GB/day)",
    MinCost == CommitmentTier500DailyCost, "Commitment tier (500 GB/day)",
    MinCost == CommitmentTier1000DailyCost, "Commitment tier (1000 GB/day)",
    MinCost == CommitmentTier2000DailyCost, "Commitment tier (2000 GB/day)",
    MinCost == CommitmentTier5000DailyCost, "Commitment tier (5000 GB/day)",
    "Error"
)
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, 
    CommitmentTier100DailyCost, CommitmentTier200DailyCost, CommitmentTier300DailyCost, CommitmentTier400DailyCost, CommitmentTier500DailyCost, CommitmentTier1000DailyCost, CommitmentTier2000DailyCost, CommitmentTier5000DailyCost, Recommendation 
| sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

このクエリは、使用量の計算方法を正確に複製するものではなく、ほとんどの場合、価格レベルの推奨を提供するために使用されます。  

> [!NOTE]
> OMS E1 Suite、OMS E2 Suite、または OMS Add-On for System Center のいずれかを購入することによって得られる資格を使用するには、OMS Log Analytics の *ノード単位* の価格レベルを選択します。

## <a name="create-an-alert-when-data-collection-is-high"></a>収集したデータの量が多い場合のアラートを作成する

このセクションでは、Azure Monitor の[ログ アラート](../alerts/alerts-unified-log.md)を使用して、過去 24 時間のデータ量が指定された量を超えたことを示すアラートを作成する方法について説明します。 

過去 24 時間に取り込まれた課金対象データの量が 50 GB を超えた場合に警告するには、次の手順に従います。 

- **[アラートの条件を定義します]** では、リソース ターゲットとして Log Analytics ワークスペースを指定します。
- **[アラートの条件]** では、以下を指定します。
   - **[シグナル名]** では、 **[カスタム ログ検索]** を選択します。
   - **[検索クエリ]** : `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50`。  
   - **[アラート ロジック]** は "*結果の数*" **に基づき、** **[条件]** は **しきい値** の *0* "*より大きい*" です
   - 1 日に 1 回実行するため、 **[期間]** を *1,440* 分に設定し、 **[アラートの頻度]** を *1,440* 分間隔に設定します。
- **[アラートの詳細を定義します]** では、以下を指定します。
   - **[名前]** : "*24 時間でデータ量が 50 GB を超える課金対象データ*"
   - **[重大度]** : *[警告]*

ログ アラートが条件に一致するときに通知させるには、既存の[アクション グループ](../alerts/action-groups.md)を指定するか、アクション グループを新たに作成します。

アラートを受け取ったら、上記の、使用量が予想よりも多い理由についてのトラブルシューティングを行う方法に関するセクションの手順を使用します。

## <a name="data-transfer-charges-using-log-analytics"></a>Log Analytics を使用したデータ転送の料金

Log Analytics にデータを送信すると、データ帯域幅の料金が発生する可能性があります。 ただし、Log Analytics エージェントがインストールされている仮想マシンに限定され、診断設定を使用している場合や、Microsoft Sentinel に組み込まれている他のコネクタでは適用されません。 [Azure 帯域幅の価格ページ](https://azure.microsoft.com/pricing/details/bandwidth/)で説明されているように、2 つのリージョンに存在する Azure サービス間のデータ転送は、通常の料金で送信データ転送として課金されます。 受信データ転送は無料です。 ただし、この料金は、Log Analytics データ インジェストのコストと比べると非常に小さいものです。 そのため、Log Analytics のコスト管理では、ご自分で[取り込まれたデータ ボリューム](#understanding-ingested-data-volume)に注目する必要があります。 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Log Analytics がデータを収集しなくなった場合のトラブルシューティング

レガシの無料の価格レベルを使用しており、1 日に 500 MB を超えるデータを送信した場合、その日の残りはデータ収集が停止します。 1 日の制限に達したことが、Log Analytics がデータの収集を停止したり、データがないように見えたりする一般的な原因です。 データ収集が開始および停止すると、Log Analytics は **Operation** 型のイベントを作成します。 1 日の制限に達し、データがなくなっているかどうかを確認するには、検索で次のクエリを実行します。 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

データ収集が停止するとき、**OperationStatus** は **[警告]** です。 データ収集が開始するとき、**OperationStatus** は **[成功]** です。 次の表では、データ収集が停止する原因と、データ収集を再開するための推奨されるアクションを説明しています。

|収集が停止する原因| 解決策| 
|-----------------------|---------|
|ワークスペースの 1 日あたりの上限に達した|収集が自動的に再開されるまで待つか、「最大日次データ ボリュームを管理する」で説明されているようにして 1 日のデータ ボリューム制限を増やします。 日次上限のリセット時間が **[日次上限]** ページに表示されます。 |
| お使いのワークスペースが[データ インジェストのボリューム レート](../service-limits.md#log-analytics-workspaces)に達しました | 診断設定を利用して Azure リソースから送信されるデータの既定のインジェスト ボリューム レート上限はワークスペースあたり約 6 GB/分です。 これは概算値であり、実際のサイズはログの長さとその圧縮率に左右され、データの種類によって異なることがあります。 この上限は、エージェントまたはデータ コレクター API から送信されるデータには適用されません。 1 つのワークスペースに高い比率でデータを送信すると、一部のデータが削除され、しきい値を超え続けている間、6 時間ごとにワークスペースの操作テーブルにイベントが送信されます。 インジェスト ボリュームがインジェスト率の制限を超えている場合、または間もなくそれに達すると予測される場合は、LAIngestionRate@microsoft.com にメールを送信するかサポート リクエストを開いて、ワークスペースの増加を要求できます。 データ インジェスト レート制限を示すイベントは、クエリ `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"` によって見つけることができます。 |
|レガシの無料価格レベルの 1 日の制限に到達している |収集が自動的に再開される次の日まで待つか、または有料の価格レベルに変更します。|
|次のために、Azure サブスクリプションが中断された状態にある<br> 無料試用版が終了した<br> Azure パスの期限が切れた<br> 1 月の使用制限に達した (たとえば、MSDN または Visual Studio サブスクリプションで)|有料のサブスクリプションに変換する<br> 制限を削除するか、または制限がリセットされるまで待つ|

データ収集が停止したときに通知を受け取るには、「[1 日の上限に達したら警告する](#alert-when-daily-cap-is-reached)」セクションで説明されている手順を使用します。 アラート ルールに電子メール、webhook、または Runbook アクションを構成するには、[アクション グループの追加](../alerts/action-groups.md)に関する記事に記載されている手順を使用します。 

## <a name="limits-summary"></a>制限の概要

Log Analytics の追加の制限があり、その一部は Log Analytics 価格レベルに依存します。 詳細は「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)」にあります。


## <a name="next-steps"></a>次のステップ

- 検索言語の使用方法については、[Azure Monitor ログでのログ検索](../logs/log-query-overview.md)に関する記事を参照してください。 検索クエリを使用して、使用量データをさらに分析できます。
- [新しいログ アラートの作成](../alerts/alerts-metric.md)に関するページで説明されている手順を使用して、検索条件が満たされた場合に通知されるようにします。
- [ソリューションのターゲット設定](../insights/solution-targeting.md)を使用して、必要なコンピューター グループからのみデータを収集するようにします。
- 効果的なイベント収集ポリシーを構成するには、[Microsoft Defender for Cloud のフィルタリング ポリシー](../../security-center/security-center-enable-data-collection.md)に関するページを参照してください。
- [パフォーマンス カウンターの構成](../agents/data-sources-performance-counters.md)を変更します。
- イベントの収集設定を変更する方法については、[イベント ログの構成](../agents/data-sources-windows-events.md)に関するページを参照してください。
- Syslog の収集設定を変更する方法については、[Syslog の構成](../agents/data-sources-syslog.md)に関するページを参照してください。
- Syslog の収集設定を変更する方法については、[Syslog の構成](../agents/data-sources-syslog.md)に関するページを参照してください。
