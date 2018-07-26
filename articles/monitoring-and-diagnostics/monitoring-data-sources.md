---
title: Azure で使用する監視データのソース
description: 現在 Azure で使用できるすべての監視データ ソースについて説明します。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 3820a42e44896f75a2191b0426f2afa5a40461d7
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248035"
---
# <a name="consume-monitoring-data-from-azure"></a>Azure の監視データの使用

Azure プラットフォーム全体にわたって、Azure Monitor パイプラインで監査データを 1 か所に集めていますが、現状、このパイプラインですべての監査データを利用できるわけではありません。 この記事では、プログラムで Azure サービスの監査データにアクセスする方法を説明します。

## <a name="options-for-data-consumption"></a>データの使用のオプション

| データ型 | Category | サポートされているサービス | アクセスの方法 |
| --- | --- | --- | --- |
| Azure Monitor プラットフォーム レベルのメトリック | メトリック | [この一覧をご覧ください](monitoring-supported-metrics.md) | <ul><li>**REST API:** [Azure Monitor メトリック API](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Storage BLOB またはイベント ハブ:** [診断設定](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| ゲスト OS メトリック ( Perf counters など) のコンピューティング | メトリック | [Windows](../virtual-machines-dotnet-diagnostics.md) と Linux の仮想マシン (v2)、[Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)、[Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**ストレージ テーブルまたは Storage BLOB:** [Windows または Linux の Azure 診断](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**イベント ハブ:** [Windows Azure 診断](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| カスタムまたはアプリケーション メトリック | メトリック | Application Insights を使用してインストルメント化されたすべてのアプリケーション | <ul><li>**REST API:** [Application Insights REST API](https://dev.applicationinsights.io/reference)</li></ul> |
| Storage のメトリック | メトリック | Azure Storage | <ul><li>**ストレージ テーブル:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| 課金データ | メトリック | すべての Azure サービス | <ul><li>**REST API:** [Azure Resource Usage API と RateCard API](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| アクティビティ ログ | events | すべての Azure サービス | <ul><li>**REST API:** [Azure Monitor イベント API](https://docs.microsoft.com/en-us/rest/api/monitor/eventcategories)</li><li>**Storage BLOB またはイベント ハブ:** [ログ プロファイル](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Azure Monitor 診断ログ | events | [この一覧をご覧ください](monitoring-diagnostic-logs-schema.md) | <ul><li>**Storage BLOB またはイベント ハブ:** [診断設定](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| ゲスト OS ログ ( IIS、ETW、Syslog など) のコンピューティング | events | [Windows](../virtual-machines-dotnet-diagnostics.md) と Linux の仮想マシン (v2)、[Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)、[Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**ストレージ テーブルまたは Storage BLOB:** [Windows または Linux の Azure 診断](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**イベント ハブ:** [Windows Azure 診断](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| App Service ログ | events | App Services | <ul><li>**ファイル、テーブル、または BLOB Storage:** [Web アプリ診断](../app-service/web-sites-enable-diagnostic-log.md)</li></ul> |
| ストレージ ログ | events | Azure Storage | <ul><li>**ストレージ テーブル:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Security Center のアラート | events | Azure Security Center | <ul><li>**REST API:** [セキュリティ アラート](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Active Directory レポート | events | Azure Active Directory | <ul><li>**REST API:** [Azure Active Directory Graph API](../active-directory/active-directory-reporting-api-getting-started.md)</li></ul> |
| Security Center リソースのステータス | Status | [サポートされているすべてのリソース](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**REST API:** [セキュリティ ステータス](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| リソース正常性 | Status | サポートされているサービス | <ul><li>**REST API:** [Resource Health REST API](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Azure Monitor メトリックのアラート | 通知 | [この一覧をご覧ください](monitoring-supported-metrics.md) | <ul><li>**Webhook:** [Azure メトリックのアラート](insights-webhooks-alerts.md)</li></ul> |
| Azure Monitor アクティビティ ログのアラート | 通知 | すべての Azure サービス | <ul><li>**Webhook:** Azure アクティビティ ログのアラート</li></ul> |
| 自動スケールの通知 | 通知 | [この一覧をご覧ください](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**Webhook:** [自動スケール通知の Webhook ペイロード スキーマ](insights-autoscale-to-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| ログ検索クエリのアラート | 通知 | Log Analytics | <ul><li>**webhook:** [ログ アラート ルールの webhook アクション](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md)</li></ul> |
| Application Insights メトリックのアラート | 通知 | Application Insights | <ul><li>**Webhook:** [Application Insights のアラート](../application-insights/app-insights-alerts.md)</li></ul> |
| Application Insights の Web テスト | 通知 | Application Insights | <ul><li>**Webhook:** [Application Insights のアラート](../application-insights/app-insights-alerts.md)</li></ul> |

## <a name="next-steps"></a>次の手順

- [Azure Monitor メトリック](monitoring-overview-metrics.md)の詳細を確認する
- [Azure アクティビティ ログ](monitoring-overview-activity-logs.md)の詳細を確認する
- [Azure 診断ログ](monitoring-overview-of-diagnostic-logs.md)の詳細を確認する
