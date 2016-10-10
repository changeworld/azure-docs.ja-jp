<properties
	pageTitle="Azure Monitor パートナーとの統合 | Microsoft Azure"
	description="Azure Monitor のパートナーと、そのパートナーとの統合に関するドキュメントにアクセスする方法について説明します。"
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/26/2016"
	ms.author="johnkem"/>

# Azure Monitor パートナーとの統合

|パートナー|||
|-----------|-----------|-----------|
| [![パートナーのロゴ][alertlogic-logo]<br/>**AlertLogic**][alertlogic-anchor] | [![パートナーのロゴ][appdynamics-logo]<br/>**AppDynamics**][appdynamics-anchor] | [![パートナーのロゴ][atlassian-logo]<br/>**Atlassian**][atlassian-anchor] |
| [![パートナーのロゴ][cloudmonix-logo]<br/>**CloudMonix**][cloudmonix-anchor] | [![パートナーのロゴ][cloudyn-logo]<br/>**Cloudyn**][cloudyn-anchor] | [![パートナーのロゴ][datadog-logo]<br/>**DataDog**][datadog-anchor] |
| [![パートナーのロゴ][dynatrace-logo]<br/>**Dynatrace**][dynatrace-anchor] | [![パートナーのロゴ][newrelic-logo]<br/>**NewRelic**][newrelic-anchor] | [![パートナーのロゴ][opsgenie-logo]<br/>**OpsGenie**][opsgenie-anchor] |
| [![パートナーのロゴ][pagerduty-logo]<br/>**PagerDuty**][pagerduty-anchor] | [![パートナーのロゴ][splunk-logo]<br/>**Splunk**][splunk-anchor] | [![パートナーのロゴ][sumologic-logo]<br/>**Sumo Logic**][sumologic-anchor] |

## Alert Logic Log Manager
Alert Logic Log Manager は、セキュリティの分析と保持の目的で、VM、アプリケーション、Azure プラットフォームのログを収集します。これには、Azure Insights API による Azure 監査ログが含まれます。この情報を使って、不正を検出し、コンプライアンス要件に対応します。

[ドキュメントを確認する。][alertlogic-doc]

## AppDynamics
AppDynamics アプリケーション パフォーマンス管理 (APM) により、アプリケーション所有者が、パフォーマンスのボトルネックのトラブルシューティングを迅速に行い、Azure 環境で実行されているアプリケーションのパフォーマンスを最適化できます。AppDynamics APM は、Azure Marketplace にシームレスに統合され、Azure Cloud Services (PaaS) (Web および worker ロールを含む)、Virtual Machines (IaaS)、リモート サービス検出 (Microsoft Azure Service Bus)、Microsoft Azure キュー Microsoft Azure リモート サービス (Azure BLOB)、Azure キュー (Microsoft Service Bus)、データ ストレージ、Microsoft Azure BLOB ストレージの監視に使用できます。

[ドキュメントを確認する。][appdynamics-doc]

## Atlassian JIRA
近日対応予定

## CloudMonix
CloudMonix は、Microsoft Azure Platform の監視、自動化、および自己復旧サービスを提供します。

[ドキュメントを確認する。][cloudmonix-doc]

## Cloudyn
Cloudyn は、マルチプラットフォーム、ハイブリッド クラウド デプロイを管理および最適化することで、企業がクラウドの可能性を最大限に引き出せるようにします。SaaS ソリューションは、スマートな最適化とクラウド ガバナンスのために、使用状況、パフォーマンス、コストを可視化し、洞察と実行可能な推奨事項を提供します。Cloudyn を使用すると、正確な配賦および階層化されたコスト割り当て管理により、説明責任が可能になります。この Cloudyn は Azure 監視に統合されており、洞察と実行可能な推奨事項を提供し、Azure のデプロイを最適化します。

[ドキュメントを確認する。][cloudyn-doc]

## Datadog
Datadog は、世界をリードするクラウド規模のアプリケーションを対象とした監視サービスで、サーバー、データベース、ツール、サービス、およびサービスのデータをまとめて、スタック全体の統合ビューに表示します。これらの機能は、SaaS ベースのデータ分析プラットフォームに用意されています。開発チームと運用チームはこのプラットフォームで連携し、ダウンタイムを回避する、パフォーマンスに関する問題を解決する、開発サイクルとデプロイ サイクルが時間どおりに完了する、といった作業を行うことができます。Datadog と Azure を統合すると、インフラストラクチャ全体のメトリックを収集して表示し、VM メトリックとアプリケーション レベルのメトリックを関連付けることができます。また、プロパティとカスタム タグを組み合わせて使用し、さまざまな側面からメトリックを分析できます。

[ドキュメントを確認する。][datadog-doc]

## Dynatrace
Dynatrace OneAgent は、対応する Azure 拡張機能メカニズムによって、Azure VM と App Services に統合されています。これにより、ホスト、ネットワーク、およびサービスのパフォーマンス メトリックを収集できます。また、メトリックだけでなく、環境をエンド ツー エンドで視覚化して、クライアント側からデータベース レイヤーへのトランザクションも表示します。さらに、AI ベースで問題が相互に関連付けられ、根本原因の分析 (コードおよびデータベースに対するメソッド レベルの洞察を含む) が完全に統合されているため、トラブルシューティングとパフォーマンスの最適化が大幅に容易になります。

[ドキュメントを確認する。][dynatrace-doc]

## NewRelic

[詳細情報。][newrelic-doc]

## OpsGenie
OpsGenie は、Azure によって生成されたアラートのディスパッチャーとして動作します。この OpsGenie では、対応スケジュールとエスカレーションに基づいて適切な通知先を特定し、電子メール、テキスト メッセージ (SMS)、電話、プッシュ通知を使用して通知します。検出された問題のアラートを Azure が生成するだけで、OpsGenie は、その問題に対応する適切な担当者を確保してくれます。

[ドキュメントを確認する。][opsgenie-doc]

## PagerDuty
トップクラスのインシデント管理ソリューション PagerDuty は、メトリックに関して、最高レベルのAzure アラート サポートを提供します。現時点で PagerDuty がサポートするのは、Azure サービスのプラットフォーム レベルのメトリックに関する通知のほか、Azure Application Insights アラートの通知、自動スケールの通知、および監査ログ イベントの通知です。こうした拡張機能により、ユーザーは、Azure コア プラットフォームを高度に視覚化しながら、PagerDuty のインシデント管理機能を最大限に利用し、リアルタイムな応答を得ることができます。Azure 統合の強化することで、webhook を使用して、すばやく簡単に設定やカスタマイズを行うことができます。

[ドキュメントを確認する。][pagerduty-doc]

## Microsoft Azure 用の Splunk アドオン
Microsoft Azure 用の Splunk アドオンは、Microsoft Azure で実行されているインフラストラクチャおよび Web サイトの、重要な診断、パフォーマンス、監査、およびセキュリティ データを収集します。

パフォーマンスと診断情報の収集元は、Azure Storage Tables と Azure Storage Blobs です。監査データの収集元は Azure Insights Events API です。ネットワーク セキュリティ グループのデータの収集元は Azure Storage Blog です。このアドオンには、事前に組み込まれたパネルがいくつか含まれています。

[ドキュメントを確認する。][splunk-doc]

## Sumo Logic

[詳細情報。][sumologic-doc]

## 次のステップ
- [アクティビティ ログ (以前の監査ログ) の詳細を確認する](../resource-group-audit.md)
- [Azure アクティビティ ログを Event Hubs にストリーミングする](./monitoring-stream-activity-logs-event-hubs.md)

<!--Connectors Documentation-->
[alertlogic-anchor]: #alertlogic-log-manager "AlertLogic"
[appdynamics-anchor]: #appdynamics "AppDynamics"
[atlassian-anchor]: #atlassian-jira "Atlassian"
[cloudmonix-anchor]: #cloudmonix "CloudMonix"
[cloudyn-anchor]: #cloudyn "Cloudyn"
[datadog-anchor]: #datadog "Datadog"
[dynatrace-anchor]: #dynatrace "Dynatrace"
[newrelic-anchor]: #newrelic "NewRelic"
[opsgenie-anchor]: #opsgenie "OpsGenie"
[pagerduty-anchor]: #pagerduty "PagerDuty"
[splunk-anchor]: #splunk-add-on-for-microsoft-azure "Splunk"
[sumologic-anchor]: #sumologic "Sumo Logic"

<!--Icon references-->
[alertlogic-logo]: ./media/partner-logos/alertlogic.png
[appdynamics-logo]: ./media/partner-logos/appdynamics.png
[atlassian-logo]: ./media/partner-logos/atlassian.png
[cloudmonix-logo]: ./media/partner-logos/cloudmonix.png
[cloudyn-logo]: ./media/partner-logos/cloudyn.png
[datadog-logo]: ./media/partner-logos/datadog.png
[dynatrace-logo]: ./media/partner-logos/dynatrace.png
[newrelic-logo]: ./media/partner-logos/newrelic.png
[opsgenie-logo]: ./media/partner-logos/opsgenie.png
[pagerduty-logo]: ./media/partner-logos/pagerduty.png
[splunk-logo]: ./media/partner-logos/splunk.png
[sumologic-logo]: ./media/partner-logos/sumologic.png

<!--Partner Documentation-->
[alertlogic-doc]: https://docs.alertlogic.com/userGuides/log-manager-collection-sources.htm "AlertLogic ドキュメント。"
[appdynamics-doc]: https://docs.appdynamics.com/display/PRO42/Register+for+AppDynamics+for+Windows+Azure "AppDynamics ドキュメント。"
[cloudmonix-doc]: http://cloudmonix.com/features/azure-management/ "CloudMonix の概要。"
[cloudyn-doc]: https://www.cloudyn.com/azure-monitoring "Cloudyn の概要。"
[datadog-doc]: http://docs.datadoghq.com/integrations/azure/ "DataDog ドキュメント。"
[dynatrace-doc]: https://blog.ruxit.com/ruxit-monitoring-azure-web-apps/ "Dynatrace ドキュメント。"
[newrelic-doc]: https://newrelic.com/azure "NewRelic ドキュメント。"
[opsgenie-doc]: https://www.opsgenie.com/docs/integrations/azure-integration "OpsGenie ドキュメント。"
[pagerduty-doc]: https://www.pagerduty.com/docs/guides/azure-integration-guide/ "PagerDuty ドキュメント"
[splunk-doc]: https://splunkbase.splunk.com/app/3084/#/details "Splunk ドキュメント。"
[sumologic-doc]: https://www.sumologic.com/azure "SumoLogic ドキュメント"

<!---HONumber=AcomDC_0928_2016-->