---
title: Azure CLI での Azure Monitor ログの管理
description: Azure CLI コマンドを使用して、ワークスペースと他の Azure サービスの対話方法など、Azure Monitor ログ内でワークスペースを管理する方法について説明します。
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 08/16/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 530602fdb471b86ba84aea0093d9e6c4d91d3c75
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820590"
---
# <a name="managing-azure-monitor-logs-in-azure-cli"></a>Azure CLI での Azure Monitor ログの管理

ここに記載されている Azure CLI のコマンドを使用して、Azure Monitor で Log Analytics ワークスペースを管理します。

> [!NOTE]
> 2024 年 8 月 31 日に、Log Analytics エージェントは廃止されます。 その後は Azure Monitor エージェントを使用できます。 詳細については、「[Azure Monitor エージェントの概要](../agents/agents-overview.md)」を参照してください。

[!INCLUDE [Prepare your Azure CLI environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-workspace-for-monitor-logs"></a>Monitor ログ用のワークスペースを作成する

[az group create](/cli/azure/group#az_group_create) コマンドを実行してリソース グループを作成するか、既存のリソース グループを使用します。 ワークスペースを作成するには、[az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create) コマンドを使用します。

```azurecli
az group create --name ContosoRG --location eastus2
az monitor log-analytics workspace create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

ワークスペースの詳細については、「[Azure Monitor ログの概要](./data-platform-logs.md)」を参照してください。

## <a name="list-tables-in-your-workspace"></a>ワークスペース内のテーブルを一覧表示する

各ワークスペースには、複数のデータ行がある列を持つテーブルが含まれています。 各テーブルは、データ ソースによって提供される一意のデータ列セットによって定義されます。

ワークスペース内のテーブルを表示するには、[az monitor log-analytics workspace table list](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_list) コマンドを使用します。

```azurecli
az monitor log-analytics workspace table list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

出力値 `table` では、より読みやすい形式で結果が示されます。 詳しくは、「[出力の形式](/cli/azure/use-cli-effectively#output-formatting)」をご覧ください。

テーブルの保持期間を変更するには、[az monitor log-analytics workspace table update](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_update) コマンドを実行します。

```azurecli
az monitor log-analytics workspace table update --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name Syslog --retention-time 45
```

保持期間は 30 - 730 日間です。

テーブルの詳細については、「[データ構造](./data-platform-logs.md#data-structure)」を参照してください。

## <a name="export-data-from-selected-tables"></a>選択したテーブルからデータをエクスポートする

選択したテーブルから Azure ストレージ アカウントまたは Azure Event Hubs にデータを継続的にエクスポートできます。 [az monitor log-analytics workspace data-export create](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_create) コマンドを使用します。

```azurecli
az monitor log-analytics workspace data-export create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name DataExport --table Syslog \
   --destination /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Storage/storageAccounts/exportaccount \
   --enable
```

データのエクスポートを確認するには、[az monitor log-analytics workspace data-export list](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_list) コマンドを実行します。

```azurecli
az monitor log-analytics workspace data-export list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

データのエクスポートを削除するには、[az monitor log-analytics workspace data-export delete](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_delete) コマンドを実行します。 `--yes` パラメーターを指定すると、確認はスキップされます。

```azurecli
az monitor log-analytics workspace data-export delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name DataExport --yes
```

データ エクスポートの詳細については、「[Azure Monitor の Log Analytics ワークスペースのデータ エクスポート](./logs-data-export.md)」を参照してください。

## <a name="manage-a-linked-service"></a>リンクされたサービスを管理する

リンクされたサービスは、ワークスペースから別の Azure リソースへの関係を定義します。 Azure Monitor ログと Azure リソースでは、操作でこの接続が使用されます。 リンクされたサービスの使用例としては、カスタマー マネージド キーへの Automation アカウントやワークスペースの関連付けがあります。

リンクされたサービスを作成するには、[az monitor log-analytics workspace linked-service create](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_create) コマンドを実行します。

```azurecli
az monitor log-analytics workspace linked-service create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name linkedautomation \
   --resource-id /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Web/sites/ContosoWebApp09

az monitor log-analytics workspace linked-service list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

リンクされたサービスの関係を削除するには、[az monitor log-analytics workspace linked-service delete](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_delete) コマンドを実行します。

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name linkedautomation
```

詳細については、「[az monitor log-analytics workspace linked-service](/cli/azure/monitor/log-analytics/workspace/linked-service)」を参照してください。

## <a name="manage-linked-storage"></a>リンクされたストレージを管理する

ログ分析用に独自のストレージ アカウントを用意して管理する場合は、これらの Azure CLI コマンドを使用して管理できます。

ワークスペースをストレージ アカウントにリンクするには、[az monitor log-analytics workspace linked-storage create](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_create) コマンドを実行します。

```azurecli
az monitor log-analytics workspace linked-storage create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace \
   --storage-accounts /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Storage/storageAccounts/contosostorage \
   --type Alerts

az monitor log-analytics workspace linked-storage list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

ストレージ アカウントへのリンクを削除するには、[az monitor log-analytics workspace linked-storage delete](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_delete) コマンドを実行します。

```azurecli
az monitor log-analytics workspace linked-storage delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --type Alerts
```

詳細については、「[Azure Monitor Log Analytics でのカスタマー マネージド ストレージ アカウントの使用](./private-storage.md)」を参照してください。

## <a name="manage-intelligence-packs"></a>インテリジェンス パックを管理する

利用可能なインテリジェンス パックを表示するには、[az monitor log-analytics workspace pack list](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_list) コマンドを実行します。 このコマンドにより、パックが有効になっているかどうかも示されます。

```azurecli
az monitor log-analytics workspace pack list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

[az monitor log-analytics workspace pack enable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_enable) または [az monitor log-analytics workspace pack disable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_disable) コマンドを使用します。

```azurecli
az monitor log-analytics workspace pack enable --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name NetFlow

az monitor log-analytics workspace pack disable --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name NetFlow
```

## <a name="manage-saved-searches"></a>保存された検索条件の管理

保存した検索条件を作成するには、[az monitor log-analytics workspace saved-search](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_create) コマンドを実行します。

```azurecli
az monitor log-analytics workspace saved-search create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01 \
   --category "Log Management" --display-name SavedSearch01 \
   --saved-query "AzureActivity | summarize count() by bin(TimeGenerated, 1h)" --fa Function01 --fp "a:string = value"
```

保存した検索条件を表示するには、[az monitor log-analytics workspace saved-search show](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_show) コマンドを使用します。 すべての保存した検索条件を表示するには、[az monitor log-analytics workspace saved-search list](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_list) を使用します。

```azurecli
az monitor log-analytics workspace saved-search show --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01
az monitor log-analytics workspace saved-search list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

保存した検索条件を削除するには、[az monitor log-analytics workspace saved-search delete](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_delete) コマンドを実行します。

```azurecli
az monitor log-analytics workspace saved-search delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01 --yes
```

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

これらのコマンドをテストするためにリソース グループを作成した場合は、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループとそのすべての内容を削除できます。

```azurecli
az group delete --name ContosoRG
```

既存のリソース グループから新しいワークスペースを削除する場合は、[az monitor log-analytics workspace delete](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_delete) コマンドを実行します。

```azurecli
az monitor log-analytics workspace delete --resource-group ContosoRG 
   --workspace-name ContosoWorkspace --yes
```

Log Analytics ワークスペースには、論理的な削除オプションがあります。 削除したワークスペースは、削除してから 2週間は復旧できます。 [az monitor log-analytics workspace recover](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_recover) コマンドを実行します。

```azurecli
az monitor log-analytics workspace recover --resource-group ContosoRG 
   --workspace-name ContosoWorkspace
```

delete コマンドに `--force` パラメーターを追加すると、ワークスペースは即時削除されます。

## <a name="azure-cli-commands-used-in-this-article"></a>この記事で使用されている Azure CLI コマンド

- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az-group-delete)
- [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create)
- [az monitor log-analytics workspace data-export create](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_create)
- [az monitor log-analytics workspace data-export delete](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_delete)
- [az monitor log-analytics workspace data-export list](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_list)
- [az monitor log-analytics workspace delete](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_delete)
- [az monitor log-analytics workspace linked-service create](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_create)
- [az monitor log-analytics workspace linked-service delete](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_delete)
- [az monitor log-analytics workspace linked-storage create](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_create)
- [az monitor log-analytics workspace linked-storage delete](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_delete)
- [az monitor log-analytics workspace pack disable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_disable)
- [az monitor log-analytics workspace pack enable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_enable)
- [az monitor log-analytics workspace pack list](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_list)
- [az monitor log-analytics workspace recover](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_recover)
- [az monitor log-analytics workspace saved-search delete](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_delete)
- [az monitor log-analytics workspace saved-search list](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_list)
- [az monitor log-analytics workspace saved-search show](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_show)
- [az monitor log-analytics workspace saved-search](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_create)
- [az monitor log-analytics workspace table list](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_list)
- [az monitor log-analytics workspace table update](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_update)

## <a name="next-steps"></a>次のステップ

[Azure Monitor の Log Analytics の概要](log-analytics-overview.md)