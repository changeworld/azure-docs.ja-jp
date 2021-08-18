---
title: Azure Container Registry の監視データのリファレンス
description: Azure Container Registry を監視する際に必要となる重要な参考資料。 メトリック、リソース ログ、およびログ スキーマに関する詳細を提供します。
author: dlepow
ms.author: danlep
ms.topic: reference
ms.custom: subject-monitoring
ms.service: container-registry
ms.date: 03/19/2021
ms.openlocfilehash: 99b9206e305e57898a2eb4bc6d0de7dd6fe94473
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290419"
---
# <a name="monitoring-azure-container-registry-data-reference"></a>Azure Container Registry の監視データのリファレンス

Azure Container Registry の監視データの収集と分析の詳細については、「[Azure Container Registry の監視](monitor-service.md)」を参照してください。

## <a name="metrics"></a>メトリック

### <a name="container-registry-metrics"></a>Container Registry メトリック

リソース プロバイダーと種類: [Microsoft.ContainerRegistry/registries](../azure-monitor/essentials/metrics-supported.md#microsoftcontainerregistryregistries)

| メトリック | 診断設定を使用したエクスポートが可能か? | ユニット | 集計の種類 | 説明 | Dimensions  |  
|:-------|:-----|:-----|:------------|:------------------|:----- |
|     AgentPoolCPUTime   | はい |   Seconds   | 合計 |   専用[エージェント プール](tasks-agent-pools.md)で実行されている [ACR タスク](container-registry-tasks-overview.md)によって使用される CPU 時間             | なし | 
|     RunDuration   | はい |  ミリ秒   |  合計 |  [ACR タスク](container-registry-tasks-overview.md)の実行時間       | なし | 
|     StorageUsed   |  いいえ | バイト   |   Average | コンテナー レジストリによって使用されるストレージ<br/><br/>一意および共有のレイヤー、マニフェスト ファイル、およびすべてのリポジトリ内のレプリカ コピーのストレージの合計<sup>1</sup>            | 地理的位置情報 | 
|     SuccessfulPullCount | はい  |   Count   | 合計 | レジストリからのコンテナー イメージと他の成果物の成功プル数              | なし | 
|     SuccessfulPushCount   | はい |   Count   | 合計 | レジストリへのコンテナー イメージと他の成果物の成功プッシュ数          | なし | 
|     TotalPullCount   |   はい | Count   |     合計 |  レジストリからのコンテナー イメージと他の成果物の合計プル数         | なし | 
|     TotalPushCount   | はい |  Count   |   合計 |  レジストリへのコンテナー イメージと他の成果物の合計プッシュ数           | なし | 

<sup>1</sup>レイヤー共有のため、使用されるレジストリ ストレージは、個々のリポジトリのストレージの合計よりも少ない場合があります。 リポジトリまたはタグを[削除](container-registry-delete.md)すると、マニフェスト ファイルで使用されるストレージと参照される一意のレイヤーのみが回復されます。

詳細については、[Azure Monitor でサポートされているすべてのプラットフォーム メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を参照してください。

## <a name="metric-dimensions"></a>メトリック ディメンション

メトリック ディメンションの詳細については、「[多次元メトリック](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)」を参照してください。

Azure Container Registry には、そのメトリックに関連付けられた次のディメンションがあります。

| ディメンション名 | 説明 |
| ------------------- | ----------------- |
| **位置情報** | レジストリまたは [geo レプリカ](container-registry-geo-replication.md)の Azure リージョン。 |


## <a name="resource-logs"></a>リソース ログ

このセクションでは、Azure Container Registry 用に収集できるリソース ログの種類を示します。 

[Azure Monitorでサポートされているすべてのリソース ログのカテゴリの種類](../azure-monitor/essentials/resource-logs-schema.md)の一覧も参照してください。

### <a name="container-registries"></a>コンテナー レジストリ

リソース プロバイダーと種類: [Microsoft.ContainerRegistry/registries](../azure-monitor/essentials/resource-logs-categories.md#microsoftcontainerregistryregistries)

| カテゴリ | 表示名 | 詳細  |
|:---------|:-------------|------------------|
| ContainerRegistryLoginEvents  | ログイン イベント | 受信 ID や IP アドレスを含むレジストリ認証イベントと状態 |
| ContainerRegistryRepositoryEvents | リポジトリ イベント           | レジストリ リポジトリ内のイメージとその他の成果物に対する操作<br/><br/> 次の操作がログに記録されます。プッシュ、プル、タグ解除、削除 (リポジトリの削除を含む)、タグの消去、マニフェストの消去<sup>1</sup> |

<sup>1</sup>消去イベントは、レジストリ[保持ポリシー](container-registry-retention-policy.md)が構成されている場合にのみログに記録されます。

## <a name="azure-monitor-logs-tables"></a>Azure Monitor ログ テーブル

このセクションでは、Azure Container Registry に関連し、Log Analytics によるクエリに使用できるすべての Azure Monitor ログの Kusto テーブルを参照します。 

### <a name="container-registry"></a>Container Registry

| テーブル |  説明 | 
|:---------|:-------------|
| [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity)   | Azure で発生したサブスクリプション レベルまたは管理グループ レベルのイベントに関する分析情報を提供する、Azure のアクティビティ ログからのエントリ。 | 
| [AzureMetrics](/azure/azure-monitor/reference/tables/azuremetrics) | Azure サービスによって生成される、その正常性とパフォーマンスを測定したメトリック データ。    |  
|  [ContainerRegistryLoginEvents](/azure/azure-monitor/reference/tables/containerregistryloginevents)               | Azure Container Registry ログイン監査ログ                             |                                                     
|  [ContainerRegistryRepositoryEvents](/azure/azure-monitor/reference/tables/containerregistryrepositoryevents)               | Azure Container Registry リポジトリ監査ログ                         |                                                    

すべての Azure Monitor ログ/Log Analytics テーブルのリファレンスについては、[Azure Monitor ログ テーブル リファレンス](/azure/azure-monitor/reference/tables/tables-resourcetype)に関するページを参照してください。

## <a name="activity-log"></a>アクティビティ ログ

次の表は、[アクティビティ ログ](../azure-monitor/essentials/activity-log.md)で作成される可能性がある Azure Container Registry に関連する操作を示しています。 このリストは全てを網羅しているわけではありません。

| 操作 | 説明 |
|:---|:---|
| コンテナー レジストリの作成または更新 | コンテナー レジストリを作成するか、レジストリ プロパティを更新します |
| コンテナー レジストリの削除 | コンテナー レジストリを削除します |
| コンテナー レジストリのログイン資格情報の一覧表示 | レジストリの管理者アカウントの資格情報を表示します |
| イメージのインポート | イメージまたはその他の成果物をレジストリへインポートします |
| ロール割り当ての作成 | リソースにアクセスするための RBAC ロールを ID に割り当てます  |


## <a name="schemas"></a>スキーマ

Azure Container Registry のリソース ログでは、次のスキーマが使用されています。

| スキーマ | 説明 |
|:--- |:---|
| [ContainerRegistryLoginEvents](/azure/azure-monitor/reference/tables/ContainerRegistryLoginEvents)  | 受信 ID や IP アドレスを含む、レジストリ認証イベントと状態のスキーマ |
| [ContainerRegistryRepositoryEvents](/azure/azure-monitor/reference/tables/ContainerRegistryRepositoryEvents) | レジストリ リポジトリ内のイメージとその他の成果物に対する操作のスキーマ |
## <a name="next-steps"></a>次のステップ

- Azure コンテナー レジストリの監視の詳細については、「[Azure Container Registry の監視](monitor-service.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/overview.md)」を参照してください。