---
title: Azure App Configuration データ監視に関するリファレンス
description: App Configuration を監視する際に必要となる重要なリファレンス資料
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 05/05/2021
ms.openlocfilehash: ec6515403574af29c47b03b55eb3cbc42ecd4a7a
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968340"
---
# <a name="monitoring-app-configuration-data-reference"></a>App Configuration データ監視のリファレンス

この記事は、App Configuration によって収集された監視データに関するリファレンスです。 「[App Configuration の監視](monitor-app-configuration.md)」で App Configuration の監視データの収集と分析に関する説明をお読みください。

## <a name="metrics"></a>メトリック 
リソースのプロバイダーと種類: [App Configuration プラットフォームのメトリック](../azure-monitor/essentials/metrics-supported.md#microsoftappconfigurationconfigurationstores)

| メトリック | ユニット | 説明 |
|-------|-----| ----- |
| Http 着信要求数   | Count | Http 着信要求の総数 |
|Http 着信要求の時間 | ミリ秒 | Http 要求のサーバー側の時間 |
| 調整された Http 要求数 | Count |    調整された要求とは、429 状態コードを返す Http 要求です (要求数が多すぎます) |

詳細については、[Azure Monitor でサポートされているすべてのプラットフォーム メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を参照してください。


## <a name="metric-dimensions"></a>メトリック ディメンション
App Configuration では、次のディメンションがメトリックに関連付けられています。

| メトリックの名前 | ディメンションの説明 |
|-------|-----|
| Http 着信要求数 | Http 要求の総数。 サポートされているディメンションは、各要求の **HttpStatusCode** または **AuthenticationScheme** です。 **AuthenticationScheme** を AAD または HMAC 認証によってフィルター処理できます。   |
| Http 着信要求の時間 | 各要求のサーバー側の時間。 サポートされているディメンションは、各要求の **HttpStatusCode** または **AuthenticationScheme** の使用です。 **AuthenticationScheme** を AAD または HMAC 認証によってフィルター処理できます。 |
| 調整された Http 要求数 | このメトリックにはディメンションがありません |

 メトリック ディメンションの詳細については、「[多次元メトリック](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)」を参照してください。

## <a name="resource-logs"></a>リソース ログ
このセクションでは、App Configuration の収集されるリソース ログのカテゴリの種類の一覧を示します。 

| リソース ログの種類 | 詳細情報|
|-------|-----|
| HttpRequest | [App Configuration リソース ログのカテゴリの情報](../azure-monitor/essentials/resource-logs-categories.md) |

詳細については、[Azure Monitor でサポートされているすべてのリソース ログのカテゴリの種類](../azure-monitor/essentials/resource-logs-schema.md)の一覧も参照してください。
 
## <a name="azure-monitor-logs-tables"></a>Azure Monitor ログ テーブル

このセクションでは、App Configuration に関連し、Log Analytics によるクエリに使用できるすべての Azure Monitor ログの Kusto テーブルを参照します。

|リソースの種類 | Notes |
|-------|-----|
| [AACHttpRequest](/azure/azure-monitor/reference/tables/aachttprequest) | 選択したアプリ構成リソースに送信されたすべての Http 要求のエントリ。 |
| [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) | Azure で発生したサブスクリプション レベルまたは管理グループ レベルのイベントに関する分析情報を提供する、Azure のアクティビティ ログからのエントリ。 |

すべての Azure Monitor ログ/Log Analytics テーブルのリファレンスについては、[Azure Monitor ログ テーブル リファレンス](/azure/azure-monitor/reference/tables/tables-resourcetype)に関するページを参照してください。

### <a name="diagnostics-tables"></a>診断テーブル

App Configuration では [AACHttpRequest テーブル](/azure/azure-monitor/reference/tables/aachttprequest)を使用してリソース ログ情報が格納されます。

**Http 要求**

|プロパティ | 種類 | 説明 |
|-------|-----| ----- |
|カテゴリ   |string |イベントのログ カテゴリ (常に HttpRequest)。 
|ClientIPAddress |  string| 要求を送信したクライアントの IP アドレス。
|ClientRequestId|   string| クライアントによって指定された要求 ID。
|CorrelationId| string| 相関ログの GUID。
|DurationMs|    INT |操作時間 (ミリ秒)。
|Method 文字列| HTTP| HTTP 要求メソッド (get または post)
|RequestId| string| サーバーによって生成された一意の要求 ID。
|RequestLength| INT |HTTP 要求の長さ (バイト単位)。
|RequestURI|    string| 要求の URI (キーとラベル名を含めることができます)。 
|_ResourceId|   string| レコードが関連付けられているリソースの一意識別子
|ResponseLength|    INT|    HTTP 応答の長さ (バイト単位)。
|SourceSystem| string|  
|StatusCode|    INT |要求の HTTP 状態コード。
|TenantId|  string  |要求の WorkspaceId。 
|TimeGenerated| DATETIME|   要求が送信されたためにログが生成されたときのタイムスタンプ (UTC)
|種類   |string|    テーブルの名前
|UserAgent| string| クライアントによって提供されるユーザー エージェント。


## <a name="see-also"></a>関連項目

* Azure App Configuration 監視の詳細については、「[Azure App Configuration の監視](monitor-app-configuration.md)」を参照してください。
* Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。