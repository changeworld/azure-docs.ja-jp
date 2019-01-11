---
title: Azure Monitor のアラートと監視の統合によるクラシックなアラートと監視の置換
description: Azure portal で [アラート] (クラシック) に表示されていたクラシック監視サービスと機能の提供終了の概要。 クラシックなアラートと監視には、Azure リソースのクラシック メトリック アラート、Application Insights のクラシック メトリック アラート、Application Insights のクラシック Web テスト アラート、Application Insights のクラシック カスタム メトリック ベースのアラート、およびApplication Insights SmartDetection v1 のクラシック アラートが含まれます。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 15a3073cde3f9e9ec8c70212cc3b1a591e703915
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052225"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Azure Monitor のアラートと監視の統合によるクラシックなアラートと監視の置換

Azure Monitor は、フル スタックの統合監視サービスになり、リソース全体で "ワン メトリック" と "ワン アラート" をサポートするようになりました。詳細については、[新しい Azure Monitor に関するブログ記事](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/)を参照してください。この新しい Azure の監視およびアラート プラットフォームは、処理速度が速く、より賢く、拡張できるように構築され、Microsoft のインテリジェント クラウド哲学に沿って、成長するクラウド コンピューティングの拡大に対応します。 

新しい Azure の監視およびアラート プラットフォームの配置に伴い、Azure アラートの "*クラシック アラートの表示*" セクションでホストされている "クラシック" な監視およびアラート プラットフォームは、その提供を終了し、**2019 年 6 月に廃止される予定**です。

 ![Azure portal のクラシック アラート](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

新しいプラットフォームの使用を開始し、新しいプラットフォームでアラートを再作成することをお勧めします。 多数のアラートを作成しているお客様のために、中断や追加コストなしで既存のクラシック アラートを新しいアラート システムに移動するための自動化された方法を提供するための作業が進んでいます。

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Application Insights でのメトリックとアラートの統合

Azure Monitor の新しいメトリック プラットフォームは、Application Insights による監視にパワーを与えています。 この動きは、Application Insights がアクション グループと連携して、以前の電子メールと Webhook 呼び出しを超える多くのオプションを提供できることを意味します。 音声通話、Azure Functions、Logic Apps、SMS、ServiceNow や Automation Runbooks などの ITSM ツールからもアラートをトリガーできるようになりました。 ほぼリアルタイムの監視とアラートによって、Application Insights ユーザーは、新しいプラットフォームで、他の Azure リソースを監視するパワーと Microsoft 製品の土台となるテクノロジーを活用できます。

Application Insights での新しい監視とアラートの統合には、以下が含まれます。

- **Application Insights プラットフォーム メトリック** – Application Insights 製品から一般的な構築済みのメトリックを提供します。 詳細については、[新しい Azure Monitor での Application Insights のプラットフォーム メトリック](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)の使用に関する記事を参照してください。
- **Application insights の可用性と Web テスト** - Web アプリまたはサーバーの応答性と可用性を評価する機能を提供します。 詳細については、[新しい Azure Monitor での Application Insights の可用性テストとアラート](../../azure-monitor/app/monitor-web-app-availability.md)の使用に関する記事を参照してください。
- **Application Insights カスタム メトリック** – 監視とアラート用の独自のメトリックを定義して発行できます。 詳細については、[新しい Azure Monitor での Application Insights のカスタム メトリック](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)の使用に関する記事を参照してください。
- **Application Insights エラーの異常 (スマート検出の一部)** – Web アプリで HTTP 要求または依存する呼び出しの失敗率が異常に増加した場合に、ほぼリアルタイムで自動的に通知します。 新しい Azure Monitor の一部としての Application Insights のエラーの異常 (スマート検出の一部) は間もなく利用可能になる予定であり、数か月後に次のバージョンがロールアウトされたときに、このドキュメントもリンク付きで更新される予定です。

## <a name="unified-metrics--alerts-for-other-azure-resources"></a>他の Azure リソースでのメトリックとアラートの統合

2018 年 3 月以降、Azure リソースに対して、次世代のアラートと多次元監視を利用できるようになっています。 新しいメトリック プラットフォームとアラートは高速であり、ほぼリアルタイムの機能を提供するようになっています。 さらに重要なのは、新しいメトリック プラットフォームでは、アラートがより高い粒度で提供されることです。これは、新しいプラットフォームには、特定の値の組み合わせ、条件、または操作でフィルター処理できるディメンション オプションが含まれているためです。 新しい Azure Monitor のすべてのアラートと同じように、新しいメトリック アラートは、ActionGroups を使用して拡張でき、電子メールや Webhook による通知に加え、SMS、Voice、Azure Function、Automation Runbook などによる通知まで展開できます。
Azure リソースの新しいメトリックは、以下のように使用できます。

- **Azure Monitor の標準プラットフォーム メトリック** – さまざまな Azure サービスと製品から一般的な設定済みのメトリックを提供します。 詳細については、[Azure Monitor でサポートされるメトリック](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)と[Azure Monitor でのメトリック アラートのサポート](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts)に関する記事を参照してください。
- **Azure Monitor のカスタム メトリック**– Azure 診断エージェントを含む、ユーザーが操作するソースからメトリックを提供します。 詳細については、記事「[Azure Monitor のカスタム メトリック](../../azure-monitor/platform/metrics-custom-overview.md)」を参照してください。 カスタム メトリックを使用して、[Windows Azure 診断エージェント](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md)と [InfluxData Telegraf エージェント](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md)によって収集されたメトリックを公開することもできます。

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>クラシック監視およびアラート プラットフォームの提供終了

前述のとおり、Azure portal の[アラート (クラシック) セクション](../../azure-monitor/platform/alerts-classic.overview.md) で現在使用できるクラシック監視およびアラート プラットフォームは、新しいシステムへの引継ぎが進行中であるため、数か月後に提供終了される予定です。
古いクラシックな監視とアラートは、関連する API、Azure portal のインターフェイス、およびその中のサービスも含めて、2019年 6 月 30 日付けで提供が終了します。 具体的には、次の機能が廃止されます。

- Azure portal の [アラート (クラシック) セクション](../../azure-monitor/platform/alerts-classic.overview.md)で現在利用できる Azure リソースの古い (クラシック) メトリックとアラート; [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) リソースとしてアクセス可能
- Application Insights の古い (クラシック) プラットフォームとカスタム メトリック、Azure portal の[アラート (クラシック) セクション](../../azure-monitor/platform/alerts-classic.overview.md) で現在使用でき、[microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) リソースとしてアクセス可能なアラート
- Azure portal で [Application Insights 内のスマート検出](../../azure-monitor/app/proactive-diagnostics.md)として現在使用できる古い (クラシック) エラーの異常; Azure portal の[アラート (クラシック) セクション](../../azure-monitor/platform/alerts-classic.overview.md)に表示されるように構成されたアラート

対応する [API](https://msdn.microsoft.com/library/azure/dn931945.aspx)、[PowerShell](../../azure-monitor/platform/alerts-classic-portal.md)、[CLI](../../azure-monitor/platform/alerts-classic-portal.md)、[Azure Portal ページ](../../azure-monitor/platform/alerts-classic-portal.md)、および[リソース テンプレート](../../azure-monitor/platform/alerts-enable-template.md)を含むすべてのクラシック監視およびアラート システムは、2019 年 6 月末まで使用可能なままです。 

2019 年 6 月末時点の Azure Monitor:

- クラシック監視およびアラート サービスは廃止され、新しいアラート ルールの作成には使用可能できなくなります。
- 2019 年 6 月の後もアラート (クラシック) に引き続き存在するアラート ルールはすべて、引き続き通知を実行して起動しますが、変更することはできなくなります。
- 2019 年 7 月から、クラシック監視およびアラートのアラート ルールはすべて、Microsoft によって新しい Azure 監視プラットフォームにある同等のアラート ルールに自動的に移行されます。 このプロセスは、ダウンタイムなしでシームレスに実行され、お客様の監視範囲が失われることはありません。

お客様の意思で Azure portal の[アラート (クラシック) セクション](../../azure-monitor/platform/alerts-classic.overview.md) から新しい Azure アラートに移行するためのツールは間もなく提供される予定です。 アラート (クラシック) 内に構成済みで新しい Azure Monitor に移行されるすべてのルールは無料のままであり、課金対象になりません。 移行後のクラシック アラート ルールでは、電子メール、Webhook、または LogicApp による通知のプッシュにも料金はかかりません。 ただし、新しい種類の通知またはアクション (SMS、音声通話、ITSM 統合など) の使用では、その使用が、移行後のアラートに追加されたか新しいアラートに追加されたかに応じて課金対象になります。 詳細については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

さらに、[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)として、以下が課金対象になります。

- 新しい Azure Monitor プラットフォーム上で、無料ユニットを超えて作成されたすべての新しい (移行ではない) アラート ルール
- Azure Monitor に含まれる無料ユニットを超えて取り込まれて保持されるすべてのデータ
- Application Insights によって実行されるすべてのマルチテスト Web テスト
- Azure Monitor に含まれる無料ユニットを超えて格納されるすべてのカスタム メトリック

この記事は、新しい Azure の監視とアラートに関するリンクと詳細に加え、ユーザーが新しい Azure Monitor プラットフォームを導入するために役立つツールを紹介するために継続的に更新される予定です。


## <a name="next-steps"></a>次の手順

* [新しい統合された Azure Monitor](../../azure-monitor/overview.md)について確認する。
* 新しい [Azure のアラート](../../azure-monitor/platform/alerts-overview.md)の詳細を確認する。
