---
title: Azure Sentinel のコストと課金 | Microsoft Docs
description: Azure Sentinel の価格モデル、Azure Sentinel のコストの見積もりと管理、コストと課金の理解について学習します。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 06/03/2021
ms.openlocfilehash: 0d9c016741fcdb36a2803eaf9705e9dc2ea1782b
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950300"
---
# <a name="azure-sentinel-costs-and-billing"></a>Azure Sentinel のコストと課金

Azure Sentinel では、広範なクエリ言語を使用して、膨大な量の運用データの分析、操作、分析情報の抽出が数秒で行われます。 Azure Sentinel の分析用のデータは、Azure Monitor Log Analytics ワークスペースに格納されます。

Log Analytics ワークスペースで有効にされていると、Azure Sentinel によって、ワークスペースに取り込まれたすべてのデータが自動的に分析され、ワークスペースに取り込まれて格納されたデータの量に対して課金されます。 この記事では、Azure Sentinel および関連する Log Analytics ワークスペースの使用状況とコストを監視、把握、節約する方法について説明します。

## <a name="azure-sentinel-pricing-model"></a>Azure Sentinel の価格モデル

Azure Sentinel には、柔軟で予測可能な価格モデルが用意されています。 詳細については、[Azure Sentinel の価格に関するページ](https://azure.microsoft.com/pricing/details/azure-sentinel/)を参照してください。 関連する Log Analytics の料金については、[Azure Monitor Log Analytics の価格](https://azure.microsoft.com/pricing/details/log-analytics/)に関する記事を参照してください。

### <a name="pay-as-you-go-and-commitment-tiers"></a>従量課金制レベルとコミットメント レベル

Azure Sentinel サービスには、従量課金制とコミットメント レベルという 2 つの支払い方法があります。

従量課金制は既定のモデルであり、、格納されている実際のデータ量に基づき、必要に応じて 90 日より長くデータを保持できます。 データ ボリュームは GB 単位 (10^9 バイト) で測定されます。

Log Analytics と Azure Sentinel にはコミットメント レベルの価格 (旧称、容量予約) も用意されています。これは、予測可能であり、従量課金制の価格と比較して最大で 65% も節約されます。 コミットメント レベルの価格では、100 GB/日からコミットメントを購入できます。 コミットメント レベルを超える使用量には、お客様が選択したコミットメント レベル レートで課金されます。 たとえば、コミットメント レベルが 100 GB/日の場合、コミットされた 100 GB/日のデータ量に加えて、そのレベルの割引料金で GB/日の追加分が請求されます。

コミットメント レベルは、増やすのはいつでも、減らすのは 31 日ごとに可能で、データ量の増減に合わせてコストを最適化できます。 Azure Sentinel の現在の価格レベルを確認するには、Azure Sentinel の左側のナビゲーションで **[設定]** を選択してから、 **[価格]** タブを選択します。現在の価格レベルには、 **[現在のレベル]** と表示されます。

コミットメント レベルの設定と変更については、「[価格レベルを設定または変更する](#set-or-change-pricing-tier)」を参照してください。

### <a name="costs-for-other-services"></a>その他のサービスのコスト

Azure Sentinel は、強化された機能を提供するため、他の多くの Azure サービスと統合されます。 これらのサービスには、Azure Logic Apps、Azure Notebooks、機械学習持ち込み (BYOML) モデルが含まれます。 これらのサービスの一部には、追加料金が発生する場合があります。 Azure Sentinel のデータ コネクタとソリューションの一部では、データ インジェストに Azure Functions が使用され、それに関連して別途コストがかかります。

これらのサービスの価格の詳細については、以下を参照してください。

- [Automation Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)
- [Notebooks の価格](https://azure.microsoft.com/pricing/details/machine-learning/)
- [BYOML の価格](https://azure.microsoft.com/pricing/details/machine-learning-studio/)
- [Azure Functions の価格](https://azure.microsoft.com/en-us/pricing/details/functions/)

使用するその他のサービスで、関連するコストが発生する可能性があります。

### <a name="data-retention-costs"></a>データ保持コスト

Log Analytics ワークスペースで Azure Sentinel を有効にすると、ワークスペースに取り込まれたすべてのデータを、最初の 90 日間は無料で保持できます。 90 日を超えて保持すると、標準の [Log Analytics 保持価格](https://azure.microsoft.com/pricing/details/monitor/)に従って課金されます。

個々のデータの種類に異なる保持設定を指定できます。 詳細については、「[データの種類別のリテンション期間](../azure-monitor/logs/manage-cost-storage.md#retention-by-data-type)」を参照してください。

### <a name="free-trial"></a>無料試用版

新規または既存の Log Analytics ワークスペースで Azure Sentinel を有効にすると、最初の 31 日間は追加コストがかかりません。 Log Analytics、Automation、BYOML に関連する料金は、無料試用中も引き続き適用されます。 最初の 31 日を超えて使用すると、[Azure Sentinel の価格](https://azure.microsoft.com/pricing/details/azure-sentinel)に従って課金されます。 

### <a name="free-data-sources"></a>無料データ ソース

次のデータ ソースは、Azure Sentinel では無料です。

- Azure アクティビティ ログ。
- Office 365 監査ログ (すべての SharePoint アクティビティ、Exchange 管理者アクティビティ、Teams を含む)。
- Azure Defender、Microsoft 365 Defender、Microsoft Defender for Office 365、Microsoft Defender for Identity、Microsoft Defender for Endpoint からのアラートなど、Microsoft Defender のアラート。
- Azure Security Center と Microsoft Cloud App Security (MCAS) のアラート。 ただし、Microsoft 365 Defender、MCAS、Azure Active Directory (Azure AD)、Azure Information Protection (AIP) の一部のデータの種類の生ログは有料です。

次の表は、Azure Sentinel で有効にできる無料のデータ ソースの一覧です。 Microsoft 365 Defender や MCAS などの一部のデータ コネクタには、無料と有料両方のデータの種類が含まれます。

| Azure Sentinel のデータ コネクタ   | データ型 | 無料または有料 |
|-------------------------------------|--------------------------------|------------------|
| **Azure Activity Logs**         | AzureActivity                  | Free             |
| **Office 365**                     | OfficeActivity (SharePoint)    | Free|
|| OfficeActivity (Exchange)|Free|
|| OfficeActivity (Teams)          | Free|
| **Azure Defender**                  | SecurityAlert (ASC)             | Free             |
| **Azure Defender for IoT**          | SecurityAlert (ASC for IoT)     | Free             |
| **Microsoft 365 Defender**          | SecurityIncident | Free|
||SecurityAlert| Free|
||DeviceEvents                    | 有料|
||DeviceFileEvents                | 有料|
||DeviceImageLoadEvents           | 有料|
||DeviceInfo                      | 有料|
||DeviceLogonEvents               | 有料|
||DeviceNetworkEvents             | 有料|
||DeviceNetworkInfo               | 有料|
||DeviceProcessEvents             | 有料|
||DeviceRegistryEvents            | 有料|
||DeviceFileCertificateInfo       | 有料|
| **Microsoft Defender for Endpoint** | SecurityAlert (MDATP)          | Free             |
| **Microsoft Defender for Identity** | SecurityAlert (AATP)           | Free             |
| **Microsoft Cloud App Security**   | SecurityAlert (MCAS)           | Free             |
||MCASShadowITReporting           | 有料|

無料と有料両方のデータの種類が含まれるデータ コネクタの場合は、有効にするデータの種類を選択できます。

![無料のセキュリティ アラートが選択され、有料の MCASShadowITReporting が選択されていない、MCAS の [データ コネクタ] ページを示すスクリーンショット。](media/billing/data-types.png)

## <a name="estimate-azure-sentinel-costs"></a>Azure Sentinel のコストを見積もる

Azure Sentinel をまだ使用していない場合は、[Azure Sentinel 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=azure-sentinel)を使用して、Azure Sentinel を使用した場合の予想コストを見積もることができます。 検索ボックスに「*Azure Sentinel*」と入力し、結果の Azure Sentinel タイルを選択します。 料金計算ツールを使用すると、予想されるデータ インジェストと保持期間に基づいて、可能性のあるコストを見積もることができます。

たとえば、Azure Sentinel に取り込むことが予想される 1 日当たりの GB 数と、ワークスペースのリージョンを入力できます。 計算ツールにより、これらのコンポーネント全体の 1 か月の総コストが提供されます。

- Log Analytics のデータ インジェスト 
- Azure Sentinel のデータ分析 
- Log Analytics のデータ保持 

## <a name="manage-azure-sentinel-costs"></a>Azure Sentinel のコストを管理する

Azure Sentinel の使用量とコストを把握して管理するには、複数の方法があります。

データ インジェストと保持を管理します。

- データ インジェストの量に基づき、[コミットメント レベルの価格を使用してコストを最適化](#set-or-change-pricing-tier)します。
- [Log Analytics のデータ量の上限を定義](#define-a-data-volume-cap-in-log-analytics)してインジェストを管理しますが、セキュリティ データは上限から除外されます。
- [専用クラスターを使用して Log Analytics のコストを最適化します](#optimize-log-analytics-costs-with-dedicated-clusters)。
- [セキュリティ以外のデータを別のワークスペースに分離します](#separate-non-security-data-in-a-different-workspace)。
- [Azure Data Explorer (ADX) を使用して、長期のデータ保持コストを削減します](#reduce-long-term-data-retention-costs-with-adx)。

データ インジェストとコストの変化を把握し、監視し、アラートを受け取ります。

- [クエリを実行して、データ インジェストを把握します](#run-queries-to-understand-your-data-ingestion)。
- [ブックをデプロイして、データ インジェストを視覚化します](#deploy-a-workbook-to-visualize-data-ingestion)。
- インジェストが定義済みのしきい値を超えたときにアラートを送信できる[コスト管理プレイブックを使用します](#use-a-playbook-for-cost-management-alerts)。
- [Common Event Format (CEF) のデータ インジェストを把握します](#understand-cef-ingestion-volume)。

### <a name="manage-data-ingestion-and-retention"></a>データ インジェストと保持を管理する

以下の方法を使用して、Azure Sentinel ワークスペースのデータ インジェストと保持を管理します。

#### <a name="set-or-change-pricing-tier"></a>価格レベルを設定または変更します

節約が最大になるように最適化するには、インジェスト量を監視して、インジェスト量のパターンと最もよく一致するコミットメント レベルを使用するようにします。 コミットメント レベルを増やしたり減らしたりして、データ量の変化に合わように調整できます。

いつでもコミットメント レベルを増やすことができ、31 日間のコミットメント期間が再開されます。 ただし、従量課金制またはより低いコミットメント レベルに戻すには、31 日のコミットメント期間が終了するまで待つ必要があります。 コミットメント レベルに対する課金は 1 日単位です。

Azure Sentinel の現在の価格レベルを確認するには、Azure Sentinel の左側のナビゲーションで **[設定]** を選択してから、 **[価格]** タブを選択します。現在の価格レベルには、 **[現在のレベル]** と表示されます。

価格レベルのコミットメントを変更するには、価格ページで他のいずれかのレベルを選択して、 **[適用]** を選択します。 価格レベルを変更するには、Azure Sentinel での **共同作成者** または **所有者** ロールが必要です。

![現在の価格レベルとして従量課金制が示されている、Azure Sentinel の設定の価格ページのスクリーンショット。](media/billing/pricing.png)

> [!NOTE]
> ポータルの一部の使用状況グラフの **[Security Insights]\(セキュリティ分析情報\)** には、Azure Sentinel のデータ インジェスト量が表示されます。

Azure Sentinel の価格レベルには、Log Analytics の料金は含まれません。 Log Analytics の価格レベルのコミットメントを変更するには、「[価格レベルの変更](../azure-monitor/logs/manage-cost-storage.md#changing-pricing-tier)」を参照してください。

#### <a name="define-a-data-volume-cap-in-log-analytics"></a>Log Analytics でデータ量の上限を定義する

Log Analytics では、ワークスペースの 1 日のインジェストを制限する 1 日の量の上限を有効にできます。 1 日の上限は、データ量の予期しない増加を管理し、制限内に留め、計画外の料金を制限するのに役立ちます。

1 日の量の上限を定義するには、Log Analytics ワークスペースの左側のナビゲーションで **[使用とコストの見積もり]** を選択し、 **[日次上限]** を選択します。 **[オン]** を選択し、1 日の量の上限を入力して、 **[OK]** を選択します。

![[使用とコストの見積もり] 画面と [日次上限] ウィンドウを示すスクリーンショット。](media/billing/daily-cap.png)

**[使用とコストの見積もり]** 画面には、過去 31 日間に取り込まれたデータ量の傾向と、保持されている合計データ量も示されます。

> [!IMPORTANT]
> 1 日あたりの上限では、すべてのデータの種類の収集が制限されることはありません。 Log Analytics での 1 日の上限の管理に関する詳細については、「[ご自分のデータの 1 日の最大ボリュームを管理する](../azure-monitor/logs/manage-cost-storage.md#manage-your-maximum-daily-data-volume)」を参照してください。

#### <a name="optimize-log-analytics-costs-with-dedicated-clusters"></a>専用クラスターを使用して Log Analytics のコストを最適化する

少なくとも 1 TB/日を Azure Sentinel ワークスペースまたは同じリージョン内のワークスペースに取り込む場合は、コストを削減するために Log Analytics 専用クラスターへの移行を検討してください。 Log Analytics 専用クラスターのコミットメント レベルにより、まとめると合計 1 TB/日以上を取り込むすべてのワークスペースのデータ量が集約されます。

Log Analytics 専用クラスターは、Azure Sentinel のコミットメント レベルには適用されません。 Azure Sentinel のコストは、専用クラスター内のワークスペースごとに引き続き適用されます。

複数の Azure Sentinel ワークスペースを Log Analytics 専用クラスターに追加できます。 Azure Sentinel に Log Analytics 専用クラスターを使用すると、次のような利点があります。

- クエリに含まれるすべてのワークスペースが専用クラスター内にある場合、クロスワークスペース クエリの実行速度が向上します。 それでも、環境内のワークスペースは可能な限り少なくするのが最善であり、専用クラスターで 1 つのクロスワークスペース クエリに含めることができる[ワークスペースには 100 の制限](../azure-monitor/logs/cross-workspace-query.md)がやはりあります。

- 専用クラスター内のすべてのワークスペースで、クラスターに設定されている Log Analytics コミットメント レベルを共有できます。 ワークスペースごとに個別の Log Analytics コミットメント レベルにコミットする必要がないと、コストを削減して効率化できます。 専用クラスターを有効にすると、1 日あたり 1 TB のインジェストの最小 Log Analytics コミットメント レベルにコミットすることになります。

コストの最適化のために専用クラスターに移行する場合の、その他の考慮事項を次に示します。

- リージョンおよびサブスクリプションごとのクラスターの最大数は 2 です。
- クラスターにリンクされているすべてのワークスペースは、同じリージョンに存在する必要があります。
- クラスターにリンクされるワークスペースの最大数は 1000 です。
- クラスターからリンクされたワークスペースのリンクを解除することができます。 特定のワークスペースでのリンク操作の回数は、30 日間に 2 回に制限されます。
- 既存のワークスペースをカスタマー マネージド キー (CMK) クラスターに移動することはできません。 クラスター内にワークスペースを作成する必要があります。
- 別のリソース グループまたはサブスクリプションへのクラスターの移動は、現時点ではサポートされていません。
- ワークスペースが別のクラスターにリンクされている場合、クラスターへのワークスペースのリンクは失敗します。

専用クラスターの詳細については、「[Log Analytics 専用クラスター](../azure-monitor/logs/manage-cost-storage.md#log-analytics-dedicated-clusters)」を参照してください。

#### <a name="separate-non-security-data-in-a-different-workspace"></a>セキュリティ以外のデータを別のワークスペースに分離する

Azure Sentinel により、Azure Sentinel が有効な Log Analytics ワークスペースに取り込まれたすべてのデータが分析されます。 セキュリティ以外の運用データ用に別のワークスペースを用意し、Azure Sentinel のコストが発生しないようにするのが最善です。

Azure Sentinel で脅威をハンティングまたは調査するときは、これらのスタンドアロン Azure Log Analytics ワークスペースに格納されている運用データにアクセスすることが必要な場合があります。 このデータには、ログ探索エクスペリエンスとブックでクロスワークスペース クエリを使用してアクセスできます。 ただし、すべてのワークスペースで Azure Sentinel が有効になっていない限り、クロスワークスペース間分析ルールとハンティング クエリは使用できません。

#### <a name="reduce-long-term-data-retention-costs-with-adx"></a>ADX を使用して長期のデータ保持コストを削減する

Azure Sentinel のデータ保持は、最初の 90 日間は無料です。 Log Analytics でデータ保持期間を調整するには、左側のナビゲーションで **[使用とコストの見積もり]** を選択してから、 **[データ保持]** を選択し、スライダーを調整します。

Azure Sentinel のセキュリティ データは、数か月後にその一部の値が失われる可能性があります。 セキュリティ オペレーション センター (SOC) のユーザーは、古いデータには新しいデータほど頻繁にアクセスする必要がない場合がありますが、それでも散発的な調査や監査のためにデータにアクセスすることが必要になる場合があります。 Azure Sentinel データの保持コストを削減するには、低コストでの長期データ保持に Azure Data Explorer を使用できます。 Azure Sentinel のセキュリティ インテリジェンスを必要としない古いデータに対しては、ADX によってコストと使いやすさの適切なバランスが提供されます。

ADX を使用すると、より低い価格でデータを格納できますが、引き続き Azure Sentinel と同じ Kusto クエリ言語 (KQL) のクエリを使用してデータを探索できます。 また、ADX のプロキシ機能を使用して、クロスプラットフォーム クエリを実行できます。 これらのクエリでは、ADX、Application Insights、Azure Sentinel、Log Analytics に分散されたデータが集計されて関連付けられます。

詳細については、「[長期的なログ保持のために Azure Data Explorer を統合する](store-logs-in-azure-data-explorer.md)」を参照してください。

### <a name="understand-monitor-and-alert-for-changes-in-data-ingestion-and-costs"></a>データ インジェストとコストの変化を把握し、監視し、アラートを受け取る

Azure Sentinel ワークスペース内の変化を把握、監視、アラートするには、次の方法を使用します。

#### <a name="run-queries-to-understand-your-data-ingestion"></a>クエリを実行して、データ インジェストを把握する

データ インジェストの量を把握するために使用できるいくつかのクエリを次に示します。

ソリューション別にデータ インジェストの量を表示するには、次のクエリを実行します。 

```kusto
Usage
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution
| extend Solution = iif(Solution == "SecurityInsights", "AzureSentinel", Solution)
| render columnchart
```

データの種類別にデータ インジェストの量を表示するには、次のクエリを実行します。 

```kusto
Usage
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType
| render columnchart
```
ソリューションとデータの種類の両方ごとにデータ インジェストの量を表示するには、次のクエリを実行します。 

```kusto
Usage
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| extend Solution = iif(Solution == "SecurityInsights", "AzureSentinel", Solution)
| sort by Solution asc, DataType asc
```

#### <a name="deploy-a-workbook-to-visualize-data-ingestion"></a>ブックをデプロイして、データ インジェストを視覚化する

**ワークスペース使用状況レポート ブック** により、ワークスペースのデータ消費量、コスト、使用統計が提供されます。 ブックでは、ワークスペースのデータ インジェストの状態と、無料データと課金対象データの量が示されます。 ブックのロジックを使用して、データ インジェストとコストを監視し、カスタム ビューやルールベースのアラートを作成することができます。

また、このブックではインジェストの詳細も提供されます。 ブックにより、ワークスペース内のデータがデータ テーブル別に分割され、テーブルとエントリごとの量が提供されて、インジェスト パターンの理解を深めるのに役立ちます。

ワークスペース使用状況レポート ブックを有効にするには:

1. Azure Sentinel の左側のナビゲーションで、 **[Threat management]\(脅威の管理\)**  >  **[ブック]** を選択します。
1. 検索バーに「*ワークスペース使用状況*」と入力し、 **[Workspace Usage Report]\(ワークスペース使用状況レポート\)** を選択します。
1. ブックをそのまま使用するには **[テンプレートの表示]** を選択し、ブックの編集可能なコピーを作成するには **[保存]** を選択します。 コピーを保存する場合は、 **[保存されたブックの表示]** を選択します。
1. ブックで、表示する **サブスクリプション** と **ワークスペース** を選択し、**TimeRange** に表示する時間枠を設定します。 **[ヘルプの表示]** トグルを **[はい]** に設定すると、ブックにその場で説明が表示されます。

#### <a name="use-a-playbook-for-cost-management-alerts"></a>コスト管理のアラートにプレイブックを使用する

Azure Sentinel の予算を管理するために、コスト管理のプレイブックを作成することができます。 Azure Sentinel ワークスペースが特定の期間について定義した予算を超えた場合、プレイブックによってアラートが送信されます。

Azure Sentinel GitHub コミュニティから、GitHub で [Send-IngestionCostAlert](https://github.com/iwafula025/Azure-Sentinel/tree/master/Playbooks/Send-IngestionCostAlert) コスト管理プレイブックが提供されています。 このプレイブックは繰り返しトリガーによってアクティブ化され、高い柔軟性を備えています。 要件に基づいて、実行頻度、インジェスト量、トリガーするメッセージを制御できます。

#### <a name="understand-cef-ingestion-volume"></a>CEF のインジェスト量について

CEF は、Azure Sentinel でサポートされている Syslog イベント形式です。 CEF を使用して、さまざまなソースから Azure Sentinel ワークスペースに重要なセキュリティ情報を取り込むことができます。 CEF ログは Azure Sentinel の CommonSecurityLog テーブルに格納され、これにはすべての標準的な最新の CEF フィールドが含まれます。

多くのデバイスとデータ ソースでは、標準の CEF スキーマを超えるフィールドのログを記録できます。 これらの追加フィールドは、AdditionalExtensions テーブルに格納されます。 これらのフィールド内のイベント コンテンツは可変でもかまわないため、これらのフィールドは標準の CEF フィールドよりインジェスト量が多くなる可能性があります。

## <a name="understand-your-azure-sentinel-costs-and-bill"></a>Azure Sentinel のコストと課金について

Azure Sentinel のコストを把握し、追跡することが重要です。 [Azure Cost Management + Billing](../cost-management-billing/costs/quick-acm-cost-analysis.md) ハブには便利な機能があります。 Azure portal で **Cost Management + Billing** を開いた後、左側のナビゲーションで **[コスト管理]** を選択し、Azure サブスクリプションやリソース グループなど、調査する[スコープ](..//cost-management-billing/costs/understand-work-scopes.md)またはリソースのセットを選択します。

Azure の請求書を表示するには、**Cost Management + Billing** の左側のナビゲーションで **[コスト分析]** を選択します。 **[コスト分析]** 画面で、 **[表示]** フィールドのドロップダウン キャレットを選択し、 **[請求書の詳細]** を選択します。

> [!NOTE]
> この画像で示されているコストは、例示だけを目的としたものです。 実際のコストを反映したものではありません。

![サンプルの Azure 請求書の Azure Sentinel セクションを示すスクリーンショット。](media/billing/sample-bill.png)

Azure の請求書には、Azure Sentinel と Log Analytics の料金が、選択されている価格プランに基づく個別の明細項目として表示されます。 特定の月についてワークスペースのコミットメント レベルの使用量を超過した場合、Azure の請求書には、コミットメント レベルとそれに関連する固定費が示された 1 つの明細項目と、コミットメント レベルを超えたインジェストについて、同じコミットメント レベルの料金で請求された別の明細項目が表示されます。

次の表に、Azure 請求書の **[サービス名]** 列と **[測定]** 列に Azure Sentinel と Log Analytics のコストがどのように表示されるかを示します。

|[コスト]|[サービス名]|測定|
|----|------------|----------------|
|Azure Sentinel コミットメント レベル|**sentinel**|**`n` gb コミットメント レベル**|
|Log Analytics コミットメント レベル|**azure monitor**|**`n` gb コミットメント レベル**|
|Azure Sentinel のコミットメント レベル超過分または従量課金制|**sentinel**|**analysis**|
|Log Analytics のコミットメント レベル超過分または従量課金制|**log analytics**|**data ingestion**|

Azure 請求書の表示とダウンロードの詳細については、[Azure のコストと課金の情報](../cost-management-billing/understand/download-azure-daily-usage.md)に関する記事を参照してください。

**[コスト分析]** 画面には、Azure の使用状況とコストの詳細なビューと、さまざまな制御やフィルターを適用するためのオプションも表示されます。

たとえば、特定の期間の日単位のコストのグラフを表示するには:
1. **[表示]** フィールドのドロップダウン キャレットを選択し、 **[累積コスト]** または **[1 日あたりのコスト]** を選択します。
1. 日付フィールドのドロップダウン キャレットを選択して、日付範囲を選択します。
1. **[細分性]** の横にあるドロップダウン キャレットを選択し、 **[日次]** を選択します。

> [!NOTE]
> この画像で示されているコストは、例示だけを目的としたものです。 実際のコストを反映したものではありません。

![Cost Management + Billing のコスト分析画面を示すスクリーンショット。](media/billing/cost-management.png)

さらに、制御を適用することもできます。 たとえば、Azure Sentinel に関連するコストのみを表示するには、 **[フィルターの追加]** を選択し、 **[サービス名]** を選択して、サービス名として **sentinel**、**log analytics**、**azure monitor** を選択します。

## <a name="next-steps"></a>次のステップ
Log Analytics のデータ量を減らすためのその他のヒントについては、「[データ量の削減のためのヒント](../azure-monitor/logs/manage-cost-storage.md#tips-for-reducing-data-volume)」を参照してください。