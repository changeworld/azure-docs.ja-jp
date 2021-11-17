---
title: Azure Monitor で Azure リソースを管理する | Microsoft Docs
description: Azure Monitor を使用し、Azure のリソースから監視データを収集して分析する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/15/2021
ms.openlocfilehash: f93cd688ef22d7765edbd50e742262e9febec14c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297861"
---
# <a name="tutorial-monitor-azure-resources-with-azure-monitor"></a>チュートリアル: Azure Monitor で Azure リソースを管理する
Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この監視は Azure Monitor によって提供されます。これは Azure のフル スタックの監視サービスであり、他のクラウドやオンプレミスのリソースに加えて、Azure リソースを監視するための完全な機能セットが提供されます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Monitor とは何か、および他の Azure サービスのためにポータルにどのように統合されているか
> * Azure リソースのために Azure Monitor によって収集されるデータの種類
> * データの収集と分析に使用される Azure Monitor ツール

> [!NOTE]
> このチュートリアルでは Azure Monitor の概念について説明し、さまざまなメニュー項目について説明します。 Azure Monitor の機能をすぐに使用するには、「[チュートリアル: Azure リソースのメトリックを分析する](../essentials/tutorial-metrics.md)」から始めてください。


## <a name="monitoring-data"></a>データの監視

### <a name="azure-monitor-data-collection"></a>Azure Monitor データ収集
Azure リソースを作成するとすぐに、Azure Monitor が有効にされ、メトリックとアクティビティのログの収集が開始されます。 一部の構成では、追加の監視データを収集し、追加の機能を有効にすることができます。 Azure Monitor データ プラットフォームは、Azure Monitor メトリックと Azure Monitor ログで構成されます。 それぞれによって異なる種類のデータが収集され、異なる Azure Monitor 機能が有効になります。

- [Azure Monitor メトリック](../essentials/data-platform-metrics.md)は、監視対象のリソースから時系列データベースに数値データを格納します。 Azure サブスクリプションごとにメトリック データベースが自動的に作成されます。 Azure Monitor ログからのデータは、[メトリック エクスプローラー](../essentials/tutorial-metrics.md)を使用して分析します。
- [Azure Monitor ログ](../logs/data-platform-logs.md)は、ログとパフォーマンス データを収集します。ログとパフォーマンス データは、ログ クエリを使用してさまざまな方法で取得および分析できます。 ログ データを収集するには、Log Analytics ワークスペースを作成する必要があります。 Azure Monitor ログからのデータは、[Log Analytics](../logs/log-analytics-tutorial.md) を使用して分析します。

### <a name="monitoring-data-from-azure-resources"></a>Azure リソースからのデータの監視
異なる Azure サービスからのリソースには、異なる監視要件がありますが、同じ形式の監視データが生成されるため、同じ Azure Monitor ツールを使用してすべての Azure リソースを分析できます。

Azure リソースでは、次の監視データが生成されます。

- [アクティビティ ログ](./platform-logs-overview.md) - Azure リソースごとに操作を追跡するサブスクリプションレベルのイベント。たとえば、新しいリソースの作成や、仮想マシンの起動などがあります。 アクティビティ ログ イベントは、Azure portal で表示するために自動的に生成および収集されます。 アクティビティ ログを Azure Monitor ログに送信するための診断設定を作成することができます。
- [プラットフォーム メトリック](../essentials/data-platform-metrics.md) - 一定の間隔で自動的に収集される、特定の時刻におけるリソースのいくつかの側面が記述されている数値。 プラットフォーム メトリックは、Azure Monitor メトリックで自動的に生成および収集されます。
- [リソース ログ](./platform-logs-overview.md) - Azure リソースによって実行された操作に関する分析情報が提供されます。たとえば、キー コンテナーからのシークレットの取得や、データベースに対する要求などです。 リソース ログは自動的に生成されますが、Azure Monitor ログに送信するための診断設定を作成する必要があります。
- [仮想マシンのゲストのメトリックとログ]() - Azure 仮想マシンのゲスト オペレーティング システムからのパフォーマンスとログ データ。 このデータを収集して Azure Monitor メトリックと Azure Monitor ログに送信するには、仮想マシンにエージェントをインストールする必要があります。


## <a name="menu-options"></a>メニュー オプション
Azure portal の **[監視]** メニューから Azure Monitor 機能にアクセスできますが、Azure Monitor 機能はさまざまな Azure サービスのメニューから直接アクセスできます。 Azure サービスの種類が違うとエクスペリエンスが多少異なる場合がありますが、Azure portal での監視オプションのセットは共通しています。 これには、 **[概要]** と **[アクティビティ ログ]** 、およびメニューの **[監視]** セクション内の複数のオプションがあります。 

:::image type="content" source="media/monitor-azure-resource/menu-01.png" lightbox="media/monitor-azure-resource/menu-01.png" alt-text="監視メニュー 1":::

:::image type="content" source="media/monitor-azure-resource/menu-02.png" lightbox="media/monitor-azure-resource/menu-02.png" alt-text="監視メニュー 2":::


## <a name="overview-page"></a>[概要] ページ
**[概要]** ページには、リソースの詳細と、多くの場合は現在の状態が表示されます。 たとえば、仮想マシンでは、現在の実行状態が表示されます。 多くの Azure サービスには、一連の主要なメトリックのグラフを含む **[監視]** タブがあります。 これにより、リソースの操作を簡単に確認できます。また、グラフをクリックしてメトリック エクスプローラーで開き、詳細な分析を行うことができます。 

メトリックス エクスプローラーの使用に関するチュートリアルについては、「[チュートリアル: Azure リソースのメトリックを分析する](../essentials/tutorial-metrics.md)」を参照してください。

![[概要] ページ](media/monitor-azure-resource/overview-page.png)
### <a name="activity-log"></a>アクティビティ ログ 
**[アクティビティ ログ]** メニュー項目では、現在のリソースの[アクティビティ ログ](../essentials/activity-log.md)のエントリを表示できます。 

:::image type="content" source="media/monitor-azure-resource/activity-log.png" lightbox="media/monitor-azure-resource/activity-log.png" alt-text="アクティビティ ログ":::

## <a name="alerts"></a>アラート
**[アラート]** ページには、リソースに対して発生した最近のアラートが表示されます。 アラートでは、監視データで重要な条件が検出されたときに事前に通知され、Azure Monitor メトリックまたは Azure Monitor ログのデータを使用できます。

アラート規則の作成とアラートの表示のチュートリアルについては、「[チュートリアル: Azure リソースのメトリック アラートを作成する](../alerts/tutorial-metric-alert.md)」または「[チュートリアル:Azure リソースのログ クエリ アラートを作成する](../alerts/tutorial-log-alert.md)」を参照してください。

:::image type="content" source="media/monitor-azure-resource/alerts-view.png" lightbox="media/monitor-azure-resource/alerts-view.png" alt-text="アラート ビュー":::

## <a name="metrics"></a>メトリック
**[メトリック]** メニュー項目では[メトリック エクスプローラー](./metrics-getting-started.md)が開き、ここでは個々のメトリックまたは複数の組み合わせを使用して、相関関係や傾向を特定することができます。 これは、 **[概要]** ページのいずれかのグラフをクリックしたときに開くメトリックス エクスプローラーと同じものです。

メトリックス エクスプローラーの使用に関するチュートリアルについては、「[チュートリアル: Azure リソースのメトリックを分析する](../essentials/tutorial-metrics.md)」を参照してください。

:::image type="content" source="media/monitor-azure-resource/metrics.png" lightbox="media/monitor-azure-resource/metrics.png" alt-text="メトリックス エクスプローラー":::


## <a name="diagnostic-settings"></a>診断設定
**[診断設定]** ページでは、リソースのリソース ログを収集するための[診断設定](../essentials/diagnostic-settings.md)を作成できます。 これらは複数の場所に送信できますが、最も一般的なのは、Log Analytics で分析できるように、Log Analytics ワークスペースに送信することです。

診断設定の作成に関するチュートリアルについては、「[チュートリアル: Azure リソースからリソース ログを収集して分析する](../essentials/tutorial-resource-logs.md)」を参照してください。

:::image type="content" source="media/monitor-azure-resource/diagnostic-settings.png" lightbox="media/monitor-azure-resource/diagnostic-settings.png" alt-text="診断設定":::



## <a name="insights"></a>洞察 
**[分析情報]** メニュー項目では、Azure サービスに存在する場合、リソースの分析情報が表示されます。 [分析情報](../monitor-reference.md)では、Azure Monitor データ プラットフォームと標準機能に基づいてカスタマイズされた監視エクスペリエンスが提供されます。 


利用可能な分析情報の一覧とドキュメントへのリンクについては、[Insights とコア ソリューション](../monitor-reference.md#insights-and-curated-visualizations)に関するページを参照してください。

:::image type="content" source="media/monitor-azure-resource/insights.png" lightbox="media/monitor-azure-resource/insights.png" alt-text="分析情報のスクリーンショット":::

## <a name="next-steps"></a>次の手順
Azure Monitor に関する基本的な知識が得られたので、Azure リソースのいくつかのメトリックの分析を開始します。

> [!div class="nextstepaction"]
> [Azure リソースのメトリックを分析する](../essentials/tutorial-metrics.md)
