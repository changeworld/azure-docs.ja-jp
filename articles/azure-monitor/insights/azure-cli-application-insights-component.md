---
title: Azure CLI の Application Insights コンポーネントを管理する
description: Application Insights のコンポーネントを管理するには、このサンプル コードを使用します。 この機能は、Azure Monitor の一部です。
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/10/2012
ms.custom: devx-track-azurecli
ms.openlocfilehash: b8c7b81847a1ac2484a1eaa0df56bde35eec6385
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128709101"
---
# <a name="manage-application-insights-components-by-using-azure-cli"></a>Azure CLI を使用して Application Insights コンポーネントを管理する

Azure Monitor において、コンポーネントは、分散またはマイクロサービス アプリケーションの、個別にデプロイできる部分です。 これらの Azure CLI コマンドを使用して Application Insights のコンポーネントを管理します。

この記事の例では、次の管理タスクを実行します。

- コンポーネントを作成する。
- webapp にコンポーネントを接続する。
- コンポーネントを含むストレージ アカウントに、コンポーネントをリンクする。
- コンポーネントの連続エクスポート構成を作成する。

[!INCLUDE [Prepare your Azure CLI environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-component"></a>コンポーネントを作成する

リソース グループとワークスペースがまだない場合は、[az group create](/cli/azure/group#az_group_create) および [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create) を使用して作成します。

```azurecli
az group create --name ContosoAppInsightRG --location eastus2
az monitor log-analytics workspace create --resource-group ContosoAppInsightRG \
   --workspace-name AppInWorkspace
```

コンポーネントを作成するには、[az monitor app-insights component create](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create) コマンドを実行します。 [az monitor app-insights component show](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_show) コマンドを実行すると、コンポーネントが表示されます。

```azurecli
az monitor app-insights component create --resource-group ContosoAppInsightRG \
   --app ContosoApp --location eastus2 --kind web --application-type web \
   --retention-time 120
az monitor app-insights component show --resource-group ContosoAppInsightRG --app ContosoApp
```

## <a name="connect-a-webapp"></a>webapp を接続する

この例では、コンポーネントを webapp に接続します。 webapp は、[az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) と [az webapp create](/cli/azure/webapp#az_webapp_create) コマンドを使用して作成できます。

```azurecli
az appservice plan create --resource-group ContosoAppInsightRG --name ContosoAppService
az webapp create --resource-group ContosoAppInsightRG --name ContosoApp \
   --plan ContosoAppService --name ContosoApp8765
```

コンポーネントを webapp に接続するには、[az monitor app-insights component connect-webapp](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_webapp) コマンドを実行します。

```azurecli
az monitor app-insights component connect-webapp --resource-group ContosoAppInsightRG \
   --app ContosoApp --web-app ContosoApp8765 --enable-debugger false --enable-profiler false
```

Azure 関数に接続するには、代わりに [az monitor app-insights component connect-function](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_function) コマンドを使用します。

## <a name="link-a-component-to-storage"></a>コンポーネントをストレージにリンクする

コンポーネントをストレージ アカウントにリンクすることができます。 ストレージ アカウントを作成するには、[az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを使用します。

```azurecli
az storage account create --resource-group ContosoAppInsightRG \
   --name contosolinkedstorage --location eastus2 --sku Standard_LRS
```

コンポーネントをストレージ アカウントにリンクするには、[az monitor app-insights component linked-storage link](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_link) コマンドを実行します。 既存のリンクを確認するには、[az monitor app-insights component linked-storage show](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_show) コマンドを使用します。


```azurecli
az monitor app-insights component linked-storage link  --resource-group ContosoAppInsightRG \
   --app ContosoApp --storage-account contosolinkedstorage
az monitor app-insights component linked-storage show --resource-group ContosoAppInsightRG \
   --app ContosoApp
```

ストレージとのリンクを解除するには、[az monitor app-insights component linked-storage unlink](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_unlink) コマンドを実行します。

```AzureCLI
az monitor app-insights component linked-storage unlink  \
   --resource-group ContosoAppInsightRG --app ContosoApp
```

## <a name="set-up-continuous-export"></a>連続エクスポートを設定する

連続エクスポートでは、Application Insights ポータルからイベントが JSON 形式でストレージ コンテナーに保存されます。

> [!NOTE]
> 連続エクスポートは、従来の Application Insights リソースに対してのみサポートされます。 [ワークスペース ベースの Application Insights リソース](../app/create-workspace-resource.md)では、[診断設定](../app/create-workspace-resource.md#export-telemetry)を使用する必要があります。
>

ストレージ コンテナーを作成するには、[az storage container create](/cli/azure/storage/container#az_storage_container_create) コマンドを実行します。 

```azurecli
az storage container create --name contosostoragecontainer --account-name contosolinkedstorage \
   --public-access blob 
```

コンテナーを書き込み専用にするにはアクセス権が必要です。 [az storage container policy create](/cli/azure/storage/container/policy#az_storage_container_policy_create) コマンドレットを実行します。

```azurecli
az storage container policy create --container-name contosostoragecontainer \
   --account-name contosolinkedstorage --name WAccessPolicy --permissions w
```

[az storage container generate-sas](/cli/azure/storage/container#az_storage_container_generate_sas) コマンドを使用して、SAS キーを作成します。 引用符などの不要な書式設定を行わずに、`--output tsv` パラメーター値を使用してキーを保存してください。 詳細については、「[Azure CLI を効果的に使用する](/cli/azure/use-cli-effectively)」を参照してください。

```azurecli
containersas=$(az storage container generate-sas --name contosostoragecontainer \
   --account-name contosolinkedstorage --permissions w --output tsv)
```

連続エクスポートを作成するには、[az monitor app-insights component continues-export create](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_create) コマンドを実行します。

```azurecli
az monitor app-insights component continues-export create --resource-group ContosoAppInsightRG \
   --app ContosoApp --record-types Event --dest-account contosolinkedstorage \
   --dest-container contosostoragecontainer --dest-sub-id 00000000-0000-0000-0000-000000000000 \
   --dest-sas $containersas
```

構成した連続エクスポートを削除するには、[az monitor app-insights component continues-export delete](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_delete) コマンドを使用します。 

```azurecli
az monitor app-insights component continues-export list \
   --resource-group ContosoAppInsightRG --app ContosoApp
az monitor app-insights component continues-export delete \
   --resource-group ContosoAppInsightRG --app ContosoApp --id abcdefghijklmnopqrstuvwxyz=
```

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

これらのコマンドをテストするためにリソース グループを作成した場合は、[az group delete](/cli/azure/group#az_group_delete) コマンドを使用して、リソース グループとそのすべての内容を削除できます。

```azurecli
az group delete --name ContosoAppInsightRG 
```

## <a name="azure-cli-commands-used-in-this-article"></a>この記事で使用されている Azure CLI コマンド

- [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az monitor app-insights component connect-webapp](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_webapp)
- [az monitor app-insights component continues-export create](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_create)
- [az monitor app-insights component continues-export delete](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_delete)
- [az monitor app-insights component continues-export list](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_list)
- [az monitor app-insights component create](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create)
- [az monitor app-insights component linked-storage link](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_link)
- [az monitor app-insights component linked-storage unlink](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_unlink)
- [az monitor app-insights component show](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_show)
- [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create)
- [az storage account create](/cli/azure/storage/account#az_storage_account_create)
- [az storage container create](/cli/azure/storage/container#az_storage_container_create)
- [az storage container generate-sas](/cli/azure/storage/container#az_storage_container_generate_sas)
- [az storage container policy create](/cli/azure/storage/container/policy#az_storage_container_policy_create)
- [az webapp create](/cli/azure/webapp#az_webapp_create)

## <a name="next-steps"></a>次のステップ

[Azure Monitor CLI のサンプル](../cli-samples.md)

[パフォーマンスの問題の特定と診断](../app/tutorial-performance.md)

[アプリケーション正常性の監視とアラート通知](../app/tutorial-alert.md)
