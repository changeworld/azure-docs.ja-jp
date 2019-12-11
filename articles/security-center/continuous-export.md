---
title: Azure Security Center のアラートと推奨事項を SIEM にエクスポートする | Microsoft Docs
description: この記事では、SIEM へのセキュリティ アラートと推奨事項の連続エクスポートを設定する方法について説明します
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: f994f4ec6d41fa0aab37e36d713eaefb22e85b28
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665069"
---
# <a name="export-security-alerts-and-recommendations-preview"></a>セキュリティ アラートと推奨事項のエクスポート (プレビュー)

Azure Security Center では、詳細なセキュリティ アラートと推奨事項が生成されます。 これらは、ポータル内またはプログラム ツールで表示できます。 場合によっては、この情報のエクスポートや、環境内の他の監視ツールへの送信が必要になることもあるでしょう。 

この記事では、アラートと推奨事項を手動または継続的な方法でエクスポートできるようにする一連の (プレビュー) ツールについて説明します。

これらのツールを使用すると、次のことができます。

* 詳細なレポートを CSV として生成する
* Log Analytics ワークスペースにエクスポートする
* Azure Event Hubs にエクスポートする (サードパーティ SIEM との統合のため)

## <a name="setting-up-a-continuous-export"></a>連続エクスポートを設定する

1. Security Center のサイドバーで、 **[価格と設定]** をクリックします。

1. データのエクスポートを構成する特定のサブスクリプションを選択します。
    
1. そのサブスクリプションの設定ページのサイドバーで、 **[連続エクスポート (プレビュー)]** を選択します。

    [![Azure Security Center のエクスポート オプション](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) - ここにエクスポート オプションが表示されます。 使用可能なエクスポート ターゲットごとにタブがあります。 

1. エクスポートするデータの種類を選択し、それぞれの種類でフィルターを選択します (たとえば、重大度が高いアラートのみをエクスポートします)。

1. [エクスポート ターゲット] 領域で、データを保存する場所を選択します。 データは別のサブスクリプションのターゲットにも保存できます (たとえば、中央のイベント ハブ インスタンスや中央の Log Analytics ワークスペースなど)。

1. **[Save]** をクリックします。

## <a name="continuous-export-through-azure-event-hubs"></a>Azure Event Hubs を使用した連続エクスポート  

> [!NOTE]
> ほとんどの場合、監視データを外部ツールにストリーム配信するうえで最も効率的なのは、Azure Event Hubs を使用する方法です。 [この記事](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)では、各種ソースからイベント ハブに監視データをストリーム配信する方法について簡単に説明するほか、詳細なガイダンスへのリンクを紹介しています。

> [!NOTE]
> これまで Azure アクティビティ ログを使用して Security Center アラートを SIEM にエクスポートしていた場合は、代わりに次の手順で同じことができます。

### <a name="to-integrate-with-a-siem"></a>SIEM と統合するには 

選択した Security Center データを Azure Event Hubs に連続エクスポートするように構成した後、次の手順に従って SIEM に適切なコネクタを設定できます。

使用している SIEM に該当する手順に従い ([このページ](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/?cdn=disable)を参照してください)、関連するコネクタを使用します。

* **Splunk** - [Splunk 向けの Azure Monitor アドオン](https://splunkbase.splunk.com/app/3534/)を使用する
* **IBM QRadar** - [手動で構成されたログ ソース](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_logsource_microsoft_azure_event_hubs.html)を使用する
* **ArcSight** – [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292) を使用する

**Azure Sentinel** を使用している場合は、そこで提供されているネイティブの Azure Security Center アラート [データ コネクタ](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)を使用します。

また、連続エクスポートされたデータを構成済みのイベント ハブから Azure データ エクスプローラーに自動的に移動する場合は、「[イベント ハブから Azure Data Explorer にデータを取り込む](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)」の手順を使用します。


## <a name="continuous-export-to-log-analytics-workspace"></a>Log Analytics ワークスペースへの連続エクスポート

Log Analytics ワークスペースにエクスポートするには、ワークスペースで Security Center の Free または Standard レベルの Log Analytics ソリューションが有効になっている必要があります。 Azure portal を使用している場合は、連続エクスポートを有効にすると、Security Center の Free レベルのソリューションが自動的に有効になります。 一方、連続エクスポートの設定をプログラムによって構成する場合は、必要なワークスペースに対して、 **[価格と設定]** 内から手動で Free または Standard の価格レベルを選択する必要があります。  

セキュリティのアラートと推奨事項はそれぞれ、*SecurityAlert* テーブルと *SecurityRecommendations* テーブルに格納されます。 これらのテーブルを含む Log Analytics ソリューションの名前は、使用しているレベルが Free か Standard かに応じて ([価格](security-center-pricing.md)を参照してください)、Security または SecurityCenterFree となります。

![Log Analytics の *SecurityAlert* テーブル](./media/continuous-export/log-analytics-securityalert-solution.png)

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>エクスポートされたセキュリティ アラートと推奨事項を Azure Monitor で表示する

場合によっては、エクスポートされたセキュリティ アラートや推奨事項を [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview) で表示することもできます。 

Azure Monitor は、診断ログ、メトリック アラート、および Log Analytics ワークスペース クエリに基づくカスタム アラートなど、さまざまな Azure アラートの統合されたアラート エクスペリエンスを提供します。

Azure Monitor の Security Center からアラートと推奨事項を表示するには、Log Analytics クエリ (ログ アラート) に基づいてアラート ルールを構成します。

1. Azure Monitor の **[アラート]** ページから、 **[新しいアラート ルール]** をクリックします。

    ![Azure Monitor の [アラート] ページ](./media/continuous-export/azure-monitor-alerts.png)

1. ルールの作成ページで、([Azure Monitor でログ アラート ルール](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)を構成するのと同じ方法で) 新しいルールを構成します。

    * **[リソース]** には、セキュリティ アラートと推奨事項のエクスポート先の Log Analytics ワークスペースを選択します。

    * **[条件]** には、 **[Custom log search]\(カスタム ログ検索\)** を選択します。 表示されたページで、クエリ、ルックバック期間、および頻度の期間を構成します。 検索クエリでは、「*SecurityAlert*」または「*Securityalert*」と入力して、Log Analytics への連続エクスポート機能を有効にしたときに Security Center が連続してエクスポートするデータ型に対してクエリを実行できます。 
    
    * 必要に応じて、トリガーする[アクション グループ](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)を構成します。 アクション グループは、メール送信、ITSM チケット、Webhook などをトリガーできます。
    ![Azure Monitor のアラート ルール](./media/continuous-export/azure-monitor-alert-rule.png)

これで、アクション グループの自動トリガーが設定された (指定されている場合)、新しい Azure Security Center アラートまたは推奨事項 (構成によって異なる) が Azure Monitor アラートに表示されるようになりました。

## <a name="manual-one-time-export-of-security-alerts"></a>セキュリティ アラートの 1 回限りの手動エクスポート

アラートまたは推奨事項の CSV レポートをダウンロードするには、 **[セキュリティ アラート]** または **[推奨事項]** ページを開き、 **[レポートを CSV にダウンロード (プレビュー)]** ボタンをクリックします。

[![アラート データを CSV ファイルとしてダウンロードする](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> これらのレポートには、Azure Portal の [ディレクトリ + サブスクリプション] フィルターで現在選択されているサブスクリプションのリソースに関するアラートと推奨事項が含まれています。![ディレクトリとサブスクリプションを選択するためのフィルター](./media/continuous-export/filter-for-export-csv.png)

## <a name="next-steps"></a>次の手順

この記事では、推奨事項とアラートの連続エクスポートを構成する方法について説明しました。 また、アラート データを CSV ファイルとしてダウンロードする方法も説明しました。 

関連資料については、次のドキュメントを参照してください。 

- [Azure Event Hubs のドキュメント](https://docs.microsoft.com/azure/event-hubs/)
- [Azure Sentinel のドキュメント](https://docs.microsoft.com/azure/sentinel/)
- [Azure Monitor のドキュメント](https://docs.microsoft.com/azure/azure-monitor/)