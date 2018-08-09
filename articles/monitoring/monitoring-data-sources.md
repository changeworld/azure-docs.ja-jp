---
title: Azure で使用する監視データのソース | Microsoft Docs
description: Azure リソースとそれらで実行されているアプリケーションの正常性とパフォーマンスを監視するために使用できるデータについて説明します。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: bwren
ms.openlocfilehash: 48cbfac78b41b47419799584837e094d45757628
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627459"
---
# <a name="sources-of-monitoring-data-in-azure"></a>Azure で使用する監視データのソース
この記事では、Azure リソースとそれらで実行されているアプリケーションの正常性とパフォーマンスを監視するために使用できるデータについて説明します。  「[Azure での監視データの収集](monitoring-data-collection.md)」で示すツールを使用して、このデータを収集および分析します。

Azure における監視データはさまざまなソースから取得されます。データは複数の階層に分類することが可能で、最上位の階層はアプリケーション、最下位の階層は Azure プラットフォームです。 データの階層を次の図に示します。各階層については、以降のセクションで詳しく説明します。

![監視データの階層](media/monitoring-data-sources/monitoring-tiers.png)


## <a name="azure-platform"></a>Azure プラットフォーム
Azure 自体の正常性と操作に関連するテレメトリには、Azure のサブスクリプションまたはテナントの操作および管理に関するデータが含まれています。 また、Azure アクティビティ ログと Azure Active Directory からの監査ログに格納されているサービスの正常性データが含まれています。

![Azure の収集](media/monitoring-data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../monitoring-and-diagnostics/monitoring-service-notifications.md) は、アプリケーションとリソースが依存するサブスクリプション内の Azure サービスの正常性に関する情報を提供します。 アプリケーションに影響を及ぼす可能性のある現在の問題および予測される重大な問題について通知するアラートを作成できます。 Service Health レコードは [Azure アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)に格納され、アクティビティ ログ エクスプローラーに表示したり Log Analytics にコピーしたりできます。

### <a name="azure-activity-log"></a>[Azure Activity Log (Azure アクティビティ ログ)]
[Azure アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)には、サービスの正常性レコードと Azure リソースに対して行われた構成の変更に関するレコードが含まれています。 アクティビティ ログは、すべての Azure リソースについて記録されます。このログはリソースの_外部_ビューを表します。 アクティビティ ログにおける各種のレコードについては、「[Azure アクティビティ ログのイベント スキーマ](../monitoring-and-diagnostics/monitoring-activity-log-schema.md)」をご覧ください。

特定のリソースのアクティビティ ログは Azure Portal のそのリソースのページで確認できます。また、[アクティビティ ログ エクスプローラー](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)では、複数のリソースのログを表示できます。 この機能は、Log Analytics にログ エントリをコピーして他の監視データと結合する際に役立ちます。 [Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md) を使用して、ログ エントリを他の場所に送信することもできます。


### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory 監査ログ
[Azure Active Directory レポート](../active-directory/reports-monitoring/overview-reports.md)には、サインイン アクティビティの履歴と、特定のテナント内で行われた変更の監査証跡が含まれています。 現時点では、Azure Active Directory 監査データを他の監視データと結合することはできません。監査データにアクセスするには Azure Active Directory と [Azure Active Directory レポート API](../active-directory/reports-monitoring/concept-reporting-api.md) を使用する必要があるためです。


## <a name="azure-services"></a>Azure サービス
メトリックおよびリソース レベルの診断ログは、Azure リソースの_内部_操作に関する情報を提供します。 これらはほとんどの Azure サービスで利用できます。特定のサービスに関する追加の分析情報は管理ソリューションが提供します。

![Azure リソースの収集](media/monitoring-data-sources/azure-resource-collection.png)


### <a name="metrics"></a>メトリック
ほとんどの Azure サービスでは、そのパフォーマンスと操作を反映するメトリックを生成します。 [リソースの種類によってメトリックは異なります](../monitoring-and-diagnostics/monitoring-supported-metrics.md)。  メトリックにはメトリックス エクスプ ローラーからアクセスできます。また、メトリックを Log Analytics にコピーして傾向分析やその他の解析に利用できます。


### <a name="resource-diagnostic-logs"></a>リソース診断ログ
アクティビティ ログは Azure リソースで実行される操作に関する情報を提供しますが、リソース レベルの[診断ログ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)はリソース自体の操作に関する分析情報を提供します。   これらのログの構成の要件とコンテンツは[リソースの種類によって異なります](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md)。

診断ログを Azure Portal で直接表示することはできませんが、[診断ログを Azure Storage に送信してアーカイブ](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)したり、[イベント ハブ](../event-hubs/event-hubs-what-is-event-hubs.md)にエクスポートして他のサービスにリダイレクトしたり、[Log Analytics](../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) にコピーして解析に使用したりできます。 Log Analytics に直接書き込むことのできるリソースと、ストレージ アカウントに書き込んでから [Log Analytics](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage) にインポートするリソースがあります。

### <a name="management-solutions"></a>管理ソリューション
 [管理ソリューション](../monitoring/monitoring-solutions.md)は、データを収集して、特定のサービスの操作に関する追加の分析情報を提供します。 このソリューションでは、データを Log Analytics に収集し、[クエリ言語](../log-analytics/log-analytics-log-search.md)や通常はソリューションに付属しているビューを使用して分析できます。

## <a name="guest-operating-system"></a>ゲスト オペレーティング システム
すべての Azure サービスで生成されるテレメトリに加えて、コンピューティング リソースには監視対象のゲスト オペレーティング システムがあります。 1 つ以上のエージェントをインストールすると、ゲストから同じ監視ツール (たとえば、Azure サービス自体) にテレメトリを収集できます。

![Azure コンピューティング リソースの収集](media/monitoring-data-sources/compute-resource-collection.png)

### <a name="diagnostic-extension"></a>診断の拡張機能
[Azure 診断の拡張機能](../monitoring-and-diagnostics/azure-diagnostics.md)では、Azure コンピューティング リソースのクライアント オペレーティング システムからログとパフォーマンス データを収集できます。 クライアントから収集されたメトリックとログは Azure ストレージ アカウントに格納されます。このアカウントから[インポートを行うように Log Analytics を構成](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage)できます。  メトリックス エクスプローラーは、このストレージ アカウントからの読み取り方法を認識して、収集された他のメトリックにクライアントのメトリックを追加します。


### <a name="log-analytics-agent"></a>Log Analytics エージェント
任意の Windows/Linux 仮想マシンまたは物理コンピューターに Log Analytics エージェントをインストールできます。 仮想マシンは Azure、別のクラウド、またはオンプレミスで実行できます。  エージェントは Log Analytics に直接または[接続済みの System Center Operations Manager 管理グループ](../log-analytics/log-analytics-om-agents.md)を経由して接続します。エージェントを使用すると、設定した[データ ソース](../log-analytics/log-analytics-data-sources.md)または仮想マシンで実行されているアプリケーションに関する追加の分析情報を提供する[管理ソリューション](../monitoring/monitoring-solutions.md)からデータを収集できます。

### <a name="service-map"></a>サービス マップ
[Service Map](../operations-management-suite/operations-management-suite-service-map.md) には、Windows および Linux 仮想マシン上の Dependency Agent が必要です。 これは Log Analytics エージェントと連携して、仮想マシンで実行されているプロセスおよび外部プロセスとの依存関係に関するデータを収集します。 このデータは Log Analytics に格納されます。Service Map には、収集したデータおよび Log Analytics に格納されているその他のデータを視覚的に表示するコンソールが用意されています。

## <a name="applications"></a>[アプリケーション]
アプリケーションがゲスト オペレーティング システムに書き込むことのできるテレメトリに加え、アプリケーションの詳細な監視が [Application Insights](https://docs.microsoft.com/azure/application-insights/) を使用して行われます。 Application Insights では、さまざまなプラットフォームで実行されているアプリケーションからデータを収集できます。 アプリケーションは Azure、別のクラウド、またはオンプレミスで実行できます。

![アプリケーション データの収集](media/monitoring-data-sources/application-collection.png)


#### <a name="application-data"></a>アプリケーション データ
インストルメンテーション パッケージをインストールすることでアプリケーションに対して有効にした Application Insights は、アプリケーションのパフォーマンスおよび操作に関連するメトリックとログを収集します。 これには、ページ ビュー、アプリケーションの要求、および例外に関する詳細情報が含まれます。 Application Insights では、収集したデータを Azure メトリックスと Log Analytics に格納します。 このデータを分析するためのさまざまなツールが用意されていますが、メトリックス エクスプローラーやログ検索などのツールを使用して、他のソースのデータと共に分析を行うこともできます。

Application Insights を使用して[カスタム メトリックを作成する](../application-insights/app-insights-api-custom-events-metrics.md)こともできます。  これにより、数値を算出して他のメトリックと共に格納するための独自のロジックを定義できます。この数値とメトリックにはメトリックス エクスプローラーからアクセスできます。また、[自動スケール](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md)やメトリック アラートに数値とメトリックを使用できます。

#### <a name="dependencies"></a>依存関係
アプリケーションのさまざまな論理操作を監視するには、[複数のコンポーネントにわたってテレメトリを収集する](../application-insights/app-insights-transaction-diagnostics.md)必要があります。 Application Insights では、[分散したテレメトリの関連付け](../application-insights/application-insights-correlation.md)をサポートします。これにより、コンポーネント間の依存関係を識別し、それらをまとめて分析できます。

#### <a name="availability-tests"></a>可用性テスト
Application Insights の[可用性テスト](../application-insights/app-insights-monitor-web-app-availability.md)では、パブリック インターネット上のさまざまな場所からアプリケーションの可用性と応答性をテストできます。 シンプルな ping テストを実行してアプリケーションが動作していることを確認したり、Visual Studio を使用してユーザー シナリオをシミュレートする Web テストを作成したりできます。  可用性テストには、アプリケーションでのインストルメンテーションは必要ありません。

## <a name="next-steps"></a>次の手順

- データの収集と分析に使用する [Azure ツールと監視データの種類](monitoring-data-collection.md)について学習します。
