---
title: Azure リソース ログでサポートされているサービスとスキーマ
description: Azure リソース ログでサポートされているサービスとイベント スキーマについて説明します。
ms.topic: reference
ms.date: 05/10/2021
ms.openlocfilehash: 1ef9c5ecea1ff45c7e6b3501f7c71928a5ea60d6
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069465"
---
# <a name="common-and-service-specific-schemas-for-azure-resource-logs"></a>Azure リソース ログの共通およびサービス固有のスキーマ

> [!NOTE]
> リソース ログは、以前は診断ログと呼ばれていました。 この名前は、Azure Monitor によって収集されたログの種類が、Azure リソースだけでなくそれ以外のものも含むように移行したため、2019 年 10 月に変更されました。 
>
> この記事には、収集できるリソース ログのカテゴリを一覧表示していました。 一覧は、現在[リソース ログのカテゴリ](resource-logs-categories.md)にあります。 

[Azure Monitor リソース ログ](../essentials/platform-logs-overview.md)は、Azure サービスによって出力されるログであり、そのサービスやリソースの操作が記述されます。 Azure Monitor によって使用可能なすべてのリソース ログは、共通の最上位レベルのスキーマを共有します。 各サービスには、独自のイベントに対して一意のプロパティを生成する柔軟性があります。

(`resourceId` プロパティで使用可能な) リソースの種類とカテゴリを組み合わせて、スキーマを一意に識別します。 この記事では、リソース ログの上位スキーマについて説明し、各サービスのスキーマへのリンクを示します。


## <a name="top-level-common-schema"></a>上位の共通スキーマ

> [!NOTE]
> ここで説明されているスキーマは、リソース ログが Azure Storage またはイベント ハブに送信されるときに該当します。 ログが Log Analytics ワークスペースに送信される場合、列名が異なる場合があります。 Log Analytics ワークスペースの全テーブルに共通する列については「[Azure Monitor ログ内の標準列](../logs/log-standard-columns.md)」を、さまざまなテーブルのリファレンスについては「[Azure Monitor データ参照](/azure/azure-monitor/reference)」を参照してください。

| 名前 | 必須または省略可能 | 説明 |
|---|---|---|
| `time` | 必須 | イベントのタイムスタンプ (UTC)。 |
| `resourceId` | 必須 | イベントを出力したリソースのリソース ID。 テナント サービスの場合、形式は */tenants/tenant-id/providers/provider-name* です。 |
| `tenantId` | テナント ログで必須 | このイベントが関連付けられている Active Directory テナントのテナント ID。 このプロパティは、テナントレベルのログにのみ使用されます。 リソースレベルのログには表示されません。 |
| `operationName` | 必須 | このイベントが表す操作の名前。 イベントが Azure ロールベースのアクセス制御 (RBAC) 操作を表す場合、これは Azure RBAC 操作名 (例: `Microsoft.Storage/storageAccounts/blobServices/blobs/Read`) です。 この名前は、文書化されている Resource Manager 操作でない場合でも、通常は、Resource Manager 操作の形式でモデル化されます (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`)。 |
| `operationVersion` | オプション | `operationName` が API を使用して実行された場合は、操作に関連付けられている API バージョン (例: `http://myservice.windowsazure.net/object?api-version=2016-06-01`)。 この操作に対応する API がない場合、バージョンは、操作に関連付けられているプロパティが今後、変更された場合、その操作のバージョンを表します。 |
| `category` | 必須 | イベントのログ カテゴリ。 カテゴリは細分化されており、これを使用して、特定のリソースのログを有効または無効にすることができます。 イベントのプロパティ BLOB 内に表示されるプロパティは、特定のログ カテゴリとリソースの種類内のものと同じです。 一般的なログ カテゴリは、`Audit`、`Operational`、`Execution`、`Request` です。 |
| `resultType` | 省略可能 | イベントの状態。 一般的な値は、`Started`、`In Progress`、`Succeeded`、`Failed`、`Active`、`Resolved` などです。 |
| `resultSignature` | オプション | イベントのサブステータス。 この操作が REST API 呼び出しに対応している場合、このフィールドは、対応する REST 呼び出しの HTTP 状態コードです。 |
| `resultDescription `| オプション | `Get storage file` など、この操作を説明する静的テキスト。 |
| `durationMs` | 省略可能 | 操作時間 (ミリ秒)。 |
| `callerIpAddress` | 省略可能 | 操作が、一般的に利用できる IP アドレスを持つエンティティからの API 呼び出しに対応している場合は、呼び出し元 IP アドレス。 |
| `correlationId` | オプション | 関連するイベントのセットをグループ化するために使用される GUID。 通常、2 つのイベントの `operationName` 値が同じであるものの、異なる 2 つの状態 (例: `Started` と `Succeeded`) の場合は、同じ `correlationID` 値を共有します。 これは、イベント間の他のリレーションシップを表す場合もあります。 |
| `identity` | 省略可能 | 操作を実行したユーザーまたはアプリケーションの ID を説明する JSON BLOB。 通常、このフィールドには Active Directory からの認証および要求または JWT トークンが含まれます。 |
| `Level` | 省略可能 | イベントの重大度レベル。 `Informational`、`Warning`、`Error`、`Critical` のいずれかである必要があります。 |
| `location` | オプション | `East US` または `France South` など、イベントを出力するリソースの領域。 |
| `properties` | オプション | このイベント カテゴリに関連する拡張プロパティ。 すべてのカスタムまたは一意のプロパティは、このスキーマの "Part B" 内に配置する必要があります。 |

## <a name="service-specific-schemas"></a>サービス固有のスキーマ

リソース ログのスキーマは、リソースとログ カテゴリによって異なります。 リソース ログを使用できるようにするすべての Azure サービスと、そのサービスおよびカテゴリ固有のスキーマへのリンク (使用可能な場合) を、次の一覧に示します。 新しいサービスが追加されると、一覧が変更されます。 必要なものが表示されない場合は、更新できるよう、この記事に関して GitHub 問題を開いてください。

| サービスまたは機能 | スキーマとドキュメント |
| --- | --- |
| Azure Active Directory | [概要](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)、[監査ログ スキーマ](../../active-directory/reports-monitoring/overview-reports.md)、[サインイン スキーマ](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Azure Analysis Services | [Azure Analysis Services: 診断ログを設定する](../../analysis-services/analysis-services-logging.md) |
| Azure API Management | [API Management のリソース ログ](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Azure App Service | [App Service ログ](../../app-service/troubleshoot-diagnostic-logs.md)
| Azure Application Gateway |[Application Gateway のログ記録](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation の Log Analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch のログ記録](../../batch/batch-diagnostics.md) |
| Azure Cognitive Services | [Azure Cognitive Services のログ記録](../../cognitive-services/diagnostic-logging.md) |
| Azure Container Instances | [Azure Container Instances のログ記録](../../container-instances/container-instances-log-analytics.md#log-schema) |
| Azure Container Registry | [Azure Container Registry のログ記録](../../container-registry/monitor-service.md) |
| Azure Content Delivery Network | [Azure Content Delivery Network の診断ログ](../../cdn/cdn-azure-diagnostic-logs.md) |
| Azure Cosmos DB | [Azure Cosmos DB のログ](../../cosmos-db/monitor-cosmos-db.md) |
| Azure Data Explorer | [Azure Data Explorer のログ](/azure/data-explorer/using-diagnostic-logs) |
| Azure Data Factory | [Azure Monitor を使用して、データ ファクトリを監視する](../../data-factory/monitor-using-azure-monitor.md) |
| Azure Data Lake Analytics |[Azure Data Lake Analytics のログへのアクセス](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Azure Data Lake Storage |[Azure Data Lake Storage のログへのアクセス](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure Database for MySQL | [Azure Database for MySQL の診断ログ](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL のログ](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Azure Databricks の診断ログ](/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs) |
| Azure DDoS Protection | [Azure DDoS Protection Standard のログ](../../ddos-protection/diagnostic-logging.md#log-schemas) |
| Azure Digital Twins | [Azure Digital Twins の診断を設定する](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Azure Event Hubs |[Azure Event Hubs のログ](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Azure ExpressRoute | スキーマ使用不可 |
| Azure Firewall | [Azure Firewall のログ](../../firewall/logs-and-metrics.md#diagnostic-logs) |
| Azure Front Door | [Azure Front Door のログ記録](../../frontdoor/front-door-diagnostics.md) |
| Azure IoT Hub | [IoT Hub 操作](../../iot-hub/monitor-iot-hub-reference.md#resource-logs) |
| Azure Key Vault |[Azure Key Vault のログ記録](../../key-vault/general/logging.md) |
| Azure Kubernetes Service |[Azure Kubernetes Service のログ記録](../../aks/monitor-aks-reference.md#resource-logs) |
| Azure Load Balancer |[Azure Load Balancer のログ分析](../../load-balancer/monitor-load-balancer.md) |
| Azure Logic Apps |[Logic Apps B2B カスタム追跡スキーマ](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Azure Machine Learning | [Azure Machine Learning での診断ログ](../../machine-learning/monitor-resource-reference.md) |
| Azure Media Services | [メディア サービス監視スキーマ](../../media-services/latest/monitoring/monitor-media-services-data-reference.md#schemas) |
| ネットワーク セキュリティ グループ |[ネットワーク セキュリティ グループ (NSG) のためのログ分析](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Azure Power BI Embedded | [Azure の Power BI Embedded のログ記録](/power-bi/developer/azure-pbie-diag-logs) |
| 復旧サービス | [Azure Backup のデータ モデル](../../backup/backup-azure-reports-data-model.md)|
| 検索トラフィックの分析 |[検索トラフィックの分析の有効化と使用](../../search/search-traffic-analytics.md) |
| Azure Service Bus |[Azure Service Bus のログ](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Azure SQL データベース | [Azure SQL Database のログ記録](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Azure Storage | [BLOB](../../storage/blobs/monitor-blob-storage-reference.md#resource-logs-preview)、[ファイル](../../storage/files/storage-files-monitoring-reference.md#resource-logs-preview)、[キュー](../../storage/queues/monitor-queue-storage-reference.md#resource-logs-preview)、[テーブル](../../storage/tables/monitor-table-storage-reference.md#resource-logs-preview) |
| Azure Stream Analytics |[ジョブのログ](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Azure の Traffic Manager | [Traffic Manager のログのスキーマ](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Azure Virtual Network | スキーマ使用不可 |
| 仮想ネットワーク ゲートウェイ | スキーマ使用不可 |



## <a name="next-steps"></a>次のステップ

* [収集できるリソース ログのカテゴリを確認する](resource-logs-categories.md)
* [リソース ログの詳細を確認する](../essentials/platform-logs-overview.md)
* [リソース ログを Event Hubs にストリーミングする](./resource-logs.md#send-to-azure-event-hubs)
* [Azure Monitor REST API を使用してリソース ログの診断設定を変更する](/rest/api/monitor/diagnosticsettings)
* [Log Analytics を使用した、Azure ストレージからのログの分析](./resource-logs.md#send-to-log-analytics-workspace)
