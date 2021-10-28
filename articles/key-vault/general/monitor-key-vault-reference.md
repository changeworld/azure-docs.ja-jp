---
title: Azure Key Vault データ参照の監視
description: Key Vault を監視する際に必要となる重要なリファレンス資料
author: msmbaldwin
ms.topic: reference
ms.author: mbaldwin
ms.service: key-vault
ms.custom: subject-monitoring
ms.date: 07/07/2021
ms.openlocfilehash: 060646995472027a931ad473223d37375b5ea2bc
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258679"
---
# <a name="monitoring-key-vault-data-reference"></a>Key Vault データ参照の監視

Key Vault の監視データの収集と分析の詳細については、[Key Vault の監視](monitor-key-vault.md)に関する記事を参照してください。

## <a name="metrics"></a>メトリック


このセクションでは、Key Vault 用に自動的に収集されるすべてのプラットフォーム メトリックを示します。  

|メトリックの種類 | リソース プロバイダー/種類の名前空間<br/> および個々のメトリックへのリンク |
|-------|-----|
| Key Vault | [Microsoft.KeyVault/vaults](../../azure-monitor/essentials/metrics-supported.md#microsoftkeyvaultvaults) |
| Managed HSM | [Microsoft.KeyVault/managedhsms](../../azure-monitor/essentials/resource-logs-categories.md#microsoftkeyvaultmanagedhsms) 

### <a name="key-vault-metrics"></a>Key Vault メトリック

リソース プロバイダーと種類: [Microsoft.KeyVault/vaults](../../azure-monitor/essentials/metrics-supported.md#microsoftkeyvaultvaults)

| 名前 | メトリック | ユニット | 種類 | 説明 |
|:-------|:-----|:------------|:------------------|
| コンテナーの全体的な可用性 | 可用性      | Percent    | Average | コンテナー要求の可用性            | 
| コンテナーの全体的な飽和度 | SaturationShoebox | Percent | Average| 使用されているコンテナーの容量 | 
| サービス API ヒット数の合計 | ServiceApiHit | Count | Count | サービス API ヒット数の合計 |
| サービス API の全体的な待機時間 | ServiceApiLatency | MilliSeconds | Average | サービス API 要求の全体的な待機時間 |
| サービス API 結果数の合計 | ServiceApiResult | Count | Count | サービス API 結果数の合計 |

詳細については、[Azure Monitor でサポートされているすべてのプラットフォーム メトリック](../../azure-monitor/essentials/metrics-supported.md)の一覧を参照してください。

## <a name="metric-dimensions"></a>メトリック ディメンション

メトリック ディメンションの詳細については、「[多次元メトリック](../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)」を参照してください。

Key Vault では、次のディメンションがそのメトリックに関連付けられています。

- ActivityType
- ActivityName
- TransactionType
- StatusCode
- StatusCodeClass

## <a name="resource-logs"></a>リソース ログ

このセクションでは、Key Vault 用に収集できるリソース ログの種類を示します。

参照については、[Microsoft.KeyVault/vault](../../azure-monitor/essentials/resource-logs-categories.md#microsoftkeyvaultvaults) の一覧を参照してください。  詳細については、[Azure Key Vault のログ記録](logging.md)に関する記事を参照してください。

|リソース ログの種類 | リソース プロバイダー/種類の名前空間<br/> および個々のメトリックへのリンク |
|-------|-----|
| Key Vault | [Microsoft.KeyVault/vaults](../../azure-monitor/essentials/resource-logs-categories.md#microsoftkeyvaultmanagedhsms) |
| Managed HSM | [Microsoft.KeyVault/managedhsms](../../azure-monitor/essentials/resource-logs-categories.md#microsoftkeyvaultvaults) 

## <a name="azure-monitor-logs-tables"></a>Azure Monitor ログ テーブル

このセクションでは、Key Vault に関連し、Log Analytics によるクエリに使用できるすべての Azure Monitor ログの Kusto テーブルを参照します。 

|リソースの種類 | メモ |
|-------|-----|
| [Key Vault](/azure/azure-monitor/reference/tables/tables-resourcetype#key-vaults) | |

すべての Azure Monitor ログ/Log Analytics テーブルのリファレンスについては、[Azure Monitor ログ テーブル リファレンス](/azure/azure-monitor/reference/tables/tables-resourcetype)に関するページを参照してください。

### <a name="diagnostics-tables"></a>診断テーブル

Key Vault では、[Azure Diagnostics](/azure/azure-monitor/reference/tables/azurediagnostics)、[Azure Activity](/azure/azure-monitor/reference/tables/azureactivity) テーブル、[Azure Metrics](/azure/azure-monitor/reference/tables/azuremetrics) テーブルを使用し、リソース ログ情報を格納します。 関係する列を次に示します。

**Azure Diagnostics**

| プロパティ | 説明 |
|:--- |:---|
| _ResourceId | レコードが関連付けられているリソースの一意識別子 |
| CallerIPAddress | 操作、UPN 要求、または可用性に基づく SPN 要求を実行したユーザーの IP アドレス。 |
| DurationMs | 操作時間 (ミリ秒)。 |
| httpStatusCode_d | 要求によって返された HTTP 状態コード (例: *200*) |
| Level | イベントのレベル。 Critical、Error、Warning、Informational、Verbose のいずれかの値。 |
| OperationName | 操作の名前 (例: Alert) |
| properties_s |  |
| Region_s | |
| requestUri_s | クライアント要求の URI。 |
| リソース | |
| ResourceProvider | メトリックを報告する Azure リソースのリソース プロバイダー。 |
| ResultSignature | |
| TimeGenerated | レコードが作成された日付と時刻 |

## <a name="see-also"></a>関連項目

- Azure Key Vault 監視の詳細については、「[Azure Key Vault の監視](monitor-key-vault.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。