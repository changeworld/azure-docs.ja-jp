---
title: Event Hubs への Azure 監視データのストリーミング
description: パートナー SIEM または分析ツールにデータを取得するために、すべての Azure 監視データをイベント ハブにストリーミングする方法を説明します。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 7/31/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 2990ba290dfdaf45d8a341138ea515bad16d5b30
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628173"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング

Azure Monitor には、Azure 環境のすべての監視データにアクセスするための単一のパイプラインがあります。これを使用すると、そのデータを使用するようにパートナー SIEM および監視ツールを簡単にセットアップすることができます。 この記事では、Azure 環境のデータのさまざまな層を、外部ツールでデータを収集できるように単一の Event Hubs 名前空間またはイベント ハブに送信されるようにセットアップする手順を説明します。

> [!VIDEO https://www.youtube.com/embed/SPHxCgbcvSw]

## <a name="what-data-can-i-send-into-an-event-hub"></a>イベント ハブに送信することができるデータの種類 

Azure 環境内には監視データの "層" がいくつかあり、各層のデータへのアクセス方法は少し異なります。 通常、これらの層は次のようになっています。

- **アプリケーション監視データ:** 自分で記述して Azure で実行しているコードのパフォーマンスと機能に関するデータ。 アプリケーション監視データの例として、パフォーマンス トレース、アプリケーション ログ、ユーザー テレメトリなどがあります。 アプリケーション監視データは、通常、次のいずれかの方法で収集されます。
  - コードを SDK ([Application Insights SDK](../application-insights/app-insights-overview.md) など) でインストルメント化することによって。
  - アプリケーションを実行しているマシン上の新しいアプリケーション ログをリッスンする監視エージェント ([Windows Azure Diagnostic Agent](./azure-diagnostics.md)、[Linux Azure Diagnostic Agent](../virtual-machines/linux/diagnostic-extension.md) など) を実行することによって。
- **ゲスト OS 監視データ:** アプリケーションが実行されているオペレーティング システムに関するデータ。 ゲスト OS 監視データの例として、Linux の syslog や Windows のシステム イベントがあります。 この種類のデータを収集するには、[Windows Azure Diagnostic Agent](./azure-diagnostics.md) や [Linux Azure Diagnostic Agent](../virtual-machines/linux/diagnostic-extension.md) のようなエージェントをインストールする必要があります。
- **Azure リソース監視データ:** Azure リソースの操作に関するデータ。 仮想マシンなど、一部の種類の Azure リソースには、その Azure サービスの内部を監視するためのゲスト OS およびアプリケーションがあります。 ネットワーク セキュリティ グループなど、他の Azure リソースでは、リソース監視データは使用可能なデータの最上位層です (それらのリソースで実行されるゲスト OS またはアプリケーションがないため)。 このデータは、[リソース診断設定](./monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)を使用して収集することができます。
- **Azure サブスクリプション監視データ:** Azure サブスクリプションの操作および管理に関するデータと、Azure 自体の正常性および操作に関するデータ。 [アクティビティ ログ](./monitoring-overview-activity-logs.md)には、サービス正常性インシデントや Azure Resource Manager の監査など、ほとんどのサブスクリプション監視データが含まれています。 このデータは、ログ プロファイルを使用して収集できます。
- **Azure テナントの監視データ:** Azure Active Directory など、テナント レベルの Azure サービスの操作に関するデータ。 テナントの監視データの例として、Azure Active Directory の監査とサインインがあります。 このデータは、テナントの診断設定を使用して収集できます。

どの層のデータも、イベント ハブに送信し、パートナー ツールに取り込むことができます。 以降のセクションでは、各層のデータをイベント ハブにストリーミングするように構成する方法について説明します。 手順では、その層のアセットが既に監視されるようになっていることを前提としています。

## <a name="set-up-an-event-hubs-namespace"></a>Event Hubs 名前空間を設定する

開始する前に、[Event Hubs 名前空間とイベント ハブを作成](../event-hubs/event-hubs-create.md)する必要があります。 この名前空間とイベント ハブが、すべての監視データの送信先です。 Event Hubs 名前空間は、同じアクセス ポリシーを共有するイベント ハブの論理的なグループであり、ストレージ アカウントがそのストレージ アカウント内に個別の BLOB ストレージを持つのと似たようなものです。 Event Hubs 名前空間やイベント ハブの作成にあたっては、以下に点に注意してください。
* Standard Event Hubs 名前空間を使用することをお勧めします。
* 通常、必要なスループット単位は 1 つだけです。 ログの使用量が増加し、スケール アップする必要が生じた場合は、名前空間のスループット単位数を後からいつでも手動で増やすことができますし、自動インフレを有効にすることもできます。
* イベント ハブのスループット スケールは、スループット単位の数によって増やすことができます。 多数のコンシューマー間での消費量は、パーティションの数によって並列化することができます。 1 つのパーティションで、最大 20MBps まで対応できます (1 秒あたり約 20,000 件のメッセージ)。 データの消費元のツールによっては、複数のパーティションからの消費をサポートできない場合もあります。 設定するべきパーティションの数がよくわからない場合は、4 つのパーティションから始めることをお勧めします。
* イベント ハブでのメッセージのリテンション期間は 7 日間に設定することをお勧めします。 そうすれば、消費元のツールが 1 日以上ダウンした場合でも、ダウンした時点から (最大 7 日前までのイベントを) 復旧することができます。
* イベント ハブには既定のコンシューマー グループを使用することをお勧めします。 2 つの異なるコンシューマー グループが同じイベント ハブから同じデータを使用するのでないかぎり、他のコンシューマー グループを作成したり、個別のコンシューマー グループを使用する必要はありません。
* Azure Activity Log については、Event Hubs 名前空間を選択すると、Azure Monitor によってその名前空間 (insights-logs-operationallogs) 内にイベント ハブが作成されます。 その他のログ タイプについては、既存のイベント ハブを選択する (同じ insights-logs-operationallogs イベント ハブを再利用できます) か、Azure Monitor によってログ カテゴリごとにイベント ハブを作成することができます。
* 通常、イベント ハブからデータを消費するコンピューターでは、ポート 5671 と 5672 を開く必要があります。

「[Event Hubs のよく寄せられる質問](../event-hubs/event-hubs-faq.md)」もご覧ください。

## <a name="how-do-i-set-up-azure-tenant-monitoring-data-to-be-streamed-to-an-event-hub"></a>Azure テナントの監視データがイベント ハブにストリーミングされるように設定する方法

Azure テナントの監視データは、現在、Azure Active Directory でのみ使用できます。 このデータは、[Azure Active Directory レポート](../active-directory/reports-monitoring/overview-reports.md)から使用できます。このデータには、サインイン アクティビティの履歴と、特定のテナント内で行われた変更の監査証跡が含まれています。

### <a name="stream-azure-active-directory-data-into-an-event-hub"></a>Azure Active Directory データをイベント ハブにストリーミングする

Azure Active Directory ログから Event Hubs 名前空間にデータを送信するには、AAD テナントにテナント診断設定を設定します。 [このガイドに従って](../active-directory/reports-monitoring/quickstart-azure-monitor-stream-logs-to-event-hub.md)テナント診断を設定します。

## <a name="how-do-i-set-up-azure-subscription-monitoring-data-to-be-streamed-to-an-event-hub"></a>Azure サブスクリプション監視データがイベント ハブにストリーミングされるように設定する方法

Azure サブスクリプション監視データは、[Azure アクティビティ ログ](./monitoring-overview-activity-logs.md)で入手できます。 このログには、Resource Manager での作成、更新、および削除操作、[Azure サービスの正常性](../service-health/service-health-overview.md)のサブスクリプションのリソースに影響する可能性がある変化、[リソース正常性](../service-health/resource-health-overview.md)の状態遷移など、さまざまなサブスクリプション レベルのイベントが含まれています。 Azure アクティビティ ログに記録されるイベントのすべてのカテゴリについては、[こちらの記事](./monitoring-activity-log-schema.md)で詳しく説明しています。

### <a name="stream-azure-activity-log-data-into-an-event-hub"></a>Azure アクティビティ ログ データをイベント ハブにストリーミングする

Azure アクティビティ ログのデータを Event Hubs 名前空間に送信するには、サブスクリプションにログ プロファイルをセットアップします。 サブスクリプションにログ プロファイルをセットアップするには、[このガイド](./monitoring-stream-activity-logs-event-hubs.md)に従ってください。 監視するサブスクリプションごとに、この手順を行います。

> [!TIP]
> 現在、ログ プロファイルでは、Event Hubs 名前空間だけを選択することができます。ここにイベント ハブが "insights-operational-logs" という名前で作成されます。 まだ、ログ プロファイルに独自のイベント ハブ名を指定することはできません。

## <a name="how-do-i-set-up-azure-resource-monitoring-data-to-be-streamed-to-an-event-hub"></a>Azure リソース監視データがイベント ハブにストリーミングされるようにセットアップする方法

Azure リソースは、次の 2 種類の監視データを出力します。
1. [リソース診断ログ](./monitoring-overview-of-diagnostic-logs.md)
2. [メトリック](monitoring-overview-metrics.md)

どちらの種類のデータも、リソース診断設定を使用してイベント ハブに送信されます。 特定のリソースにリソース診断設定をセットアップするには、[このガイド](./monitoring-stream-diagnostic-logs-to-event-hubs.md)に従ってください。 ログの収集先の各リソースで、リソース診断設定を設定します。

> [!TIP]
> Azure Policy を使用すると、特定のスコープ内のすべてのリソースを常に確実に診断設定でセットアップできます。それには、[ポリシー規則の DeployIfNotExists 効果を使用](../azure-policy/policy-definition.md#policy-rule)します。 現時点では、DeployIfNotExists は組み込みポリシーだけでサポートされています。

## <a name="how-do-i-set-up-guest-os-monitoring-data-to-be-streamed-to-an-event-hub"></a>ゲスト OS 監視データがイベント ハブにストリーミングされるようにセットアップする方法

ゲスト OS 監視データをイベント ハブに送信するには、エージェントをインストールする必要があります。 Windows でも Linux でも、イベント ハブに送信するデータと、データの送信先のイベント ハブを構成ファイルで指定し、その構成ファイルを VM 上で実行されているエージェントに渡します。

### <a name="stream-linux-data-to-an-event-hub"></a>Linux データをイベント ハブにストリーミングする

[Linux Azure Diagnostic Agent](../virtual-machines/extensions/diagnostics-linux.md) を使用して、Linux マシンの監視データをイベント ハブに送信することができます。 そうするには、LAD 構成ファイルの保護設定 JSON にイベント ハブをシンクとして追加します。 イベント ハブ シンクを Linux Azure Diagnostic Agent に追加する方法の詳細については、[こちらの記事](../virtual-machines/extensions/diagnostics-linux.md#protected-settings)を参照してください。

> [!NOTE]
> イベント ハブへのゲスト OS 監視データのストリーミングをポータルでセットアップすることはできません。 代わりに、構成ファイルを手動で編集する必要があります。

### <a name="stream-windows-data-to-an-event-hub"></a>Windows データをイベント ハブにストリーミングする

[Windows Azure Diagnostic Agent](./azure-diagnostics.md) を使用して、Windows マシンの監視データをイベント ハブに送信することができます。 そうするには、WAD 構成ファイルの privateConfig セクションにイベント ハブをシンクとして追加します。 イベント ハブ シンクを Windows Azure Diagnostic Agent に追加する方法の詳細については、[こちらの記事](./azure-diagnostics-streaming-event-hubs.md)を参照してください。

> [!NOTE]
> イベント ハブへのゲスト OS 監視データのストリーミングをポータルでセットアップすることはできません。 代わりに、構成ファイルを手動で編集する必要があります。

## <a name="how-do-i-set-up-application-monitoring-data-to-be-streamed-to-event-hub"></a>アプリケーション監視データがイベント ハブにストリーミングされるようにセットアップする方法

アプリケーション監視データでは、コードが SDK でインストルメント化されている必要があるため、アプリケーション監視データを Azure のイベント ハブにルーティングするための汎用ソリューションはありません。 ただし、[Azure Application Insights](../application-insights/app-insights-overview.md) は、Azure のアプリケーション レベルのデータを収集するために使用できるサービスの 1 つです。 Application Insights を使用している場合は、次の手順を実行して、監視データをイベント ハブにストリーミングすることができます。

1. ストレージ アカウントへの Application Insights データの[連続エクスポートをセットアップ](../application-insights/app-insights-export-telemetry.md)します。

2. [BLOB ストレージからデータをプル](../connectors/connectors-create-api-azureblobstorage.md#add-action)し、[イベント ハブにメッセージとしてプッシュ](../connectors/connectors-create-api-azure-event-hubs.md#add-action)する、タイマーによってトリガーされる Logic App をセットアップします。

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>イベント ハブに送信された監視データを処理する方法

Azure Monitor で監視データをイベント ハブにルーティングすると、パートナー SIEM や監視ツールに簡単に統合することができます。 ほとんどのツールは、イベント ハブからデータを読み取るために、イベント ハブ接続文字列と、Azure サブスクリプションへの特定のアクセス許可が必要です。 Azure Monitor と統合できるツールの一覧 (一部) を次に示します。

* **IBM QRadar** - Microsoft Azure DSM および Microsoft Azure Event Hub Protocol は、[IBM サポート Web サイト](http://www.ibm.com/support)からダウンロードすることができます。 Azure との統合の詳細については、[こちら](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)を参照してください。
* **Splunk** -Splunk の設定によって、次の 2 つの方法があります。
    1. [Splunk 向けの Azure Monitor アドオン](https://splunkbase.splunk.com/app/3534/)は、Splunkbase およびオープン ソース プロジェクトで入手できます。 ドキュメントは[こちら](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk)にあります。
    2. Splunk インスタンスにアドオンをインストールできない場合 (例:  プロキシを使用している場合、Splunk Cloud で実行している場合など)、[イベント ハブの新しいメッセージによってトリガーされるこの機能](https://github.com/Microsoft/AzureFunctionforSplunkVS)を使用して、Splunk HTTP イベント コレクターにこれらのイベントを転送できます。
* **SumoLogic** - イベント ハブのデータを使用するように SumoLogic をセットアップする手順については、[こちら](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)を参照してください。
* **Syslog サーバー** - Azure Monitor データを Syslog サーバーに直接ストリーミングする場合は、[この Github リポジトリ](https://github.com/miguelangelopereira/azuremonitor2syslog/)をチェックアウトできます。

## <a name="next-steps"></a>次の手順
* [ストレージ アカウントにアクティビティ ログをアーカイブする](monitoring-archive-activity-log.md)
* [Azure アクティビティ ログの概要を確認する](monitoring-overview-activity-logs.md)
* [アクティビティ ログ イベントに基づいてアラートを設定する](insights-auditlog-to-webhook-email.md)

