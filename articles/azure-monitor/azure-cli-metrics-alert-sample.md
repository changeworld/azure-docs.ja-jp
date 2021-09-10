---
title: Azure CLI でメトリック アラートの監視を作成する
description: Azure CLI コマンドを使用して Azure Monitor でメトリック アラートを作成する方法について説明します。 これらのサンプルでは、仮想マシンと App Service プラン用のアラートを作成します。
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 08/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 60d2b7f706c985eaa13c634e0323927eb39446ad
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117854"
---
# <a name="create-metric-alert-monitors-in-azure-cli"></a>Azure CLI でメトリック アラートの監視を作成する

これらのサンプルでは、Azure CLI コマンドを使用して Azure Monitor のメトリック　アラートの監視を作成します。 最初のサンプルでは、仮想マシン用のアラートを作成します。 2 番目のコマンドでは、App Service プランのディメンションを含むアラートを作成します。  

[!INCLUDE [Prepare your Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-alert"></a>アラートを作成する

このアラートでは、`ContosoVMRG` という名前のリソース グループにある、`VM07` という名前の既存の仮想マシンを監視します。

[az group create](/cli/azure/group#az-group-create) コマンドを使ってリソース グループを作成できます。 仮想マシンの作成の詳細については、「[Azure CLI で Windows 仮想マシンを作成する](../virtual-machines/windows/quick-create-cli.md)」、「[Azure CLI でLinux 仮想マシンを作成する](../virtual-machines/linux/quick-create-cli.md)」、および [az vm create](/cli/azure/vm#az-vm-create) コマンドを参照してください。

```azurecli
# resource group name: ContosoVMRG
# virtual machine name: VM07

# Create scope
scope=$(az vm show --resource-group ContosoVMRG --name VM07 --output tsv --query id)

# Create action
action=$(az monitor action-group create --name ContosoWebhookAction \
  --resource-group ContosoVMRG --output tsv --query id \
  --action webhook https://alerts.contoso.com usecommonalertschema)

# Create condition
condition=$(az monitor metrics alert condition create --aggregation Average \
  --metric "Percentage CPU" --op GreaterThan --type static --threshold 90 --output tsv)

# Create metrics alert
az monitor metrics alert create --name alert-01 --resource-group ContosoVMRG \
  --scopes $scope --action $action --condition $condition --description "Test High CPU"
```

このサンプルでは、引用符などの不要な記号が含まれていない `tsv` の出力の種類を使用します。 詳細については、「[Azure CLI を効果的に使用する](/cli/azure/use-cli-effectively)」を参照してください。

## <a name="create-an-alert-with-a-dimension"></a>ディメンションを含むアラートを作成する

このサンプルでは、App Service プランを作成し、それに対するメトリック アラートを作成します。 この例では、ディメンションを使用して、App Service プランのすべてのインスタンスがこのメトリックに分類されることを指定します。 このサンプルでは、リソース グループとアプリケーション サービス プランを作成します。

```azurecli
# Create resource group
az group create --name ContosoRG --location eastus2
 
# Create application service plan
az appservice plan create --resource-group ContosoRG --name ContosoAppServicePlan \
   --is-linux --number-of-workers 4 --sku S1 
 
# Create scope
scope=$(az appservice plan show --resource-group ContosoRG --name ContosoAppServicePlan \
   --output tsv --query id) 
 
# Create dimension
dim01=$(az monitor metrics alert dimension create --name Instance --value * --op Include --output tsv)
 
# Create condition
condition=$(az monitor metrics alert condition create --aggregation Average \
   --metric CpuPercentage --op GreaterThan --type static --threshold 90 \
   --dimension $dim01 --output tsv)
```

使用可能なメトリックの一覧を表示するには、[az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) コマンドを実行します。 `--output` パラメーターにより、読み取り可能な形式で値が表示されます。


```azurecli
az monitor metrics list-definitions --resource $scope --output table 
 
# Create metrics alert
az monitor metrics alert create --name alert-02 --resource-group ContosoRG \
   --scopes $scope --condition $condition --description "Service Plan High CPU"
```

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

これらのコマンドをテストするためにリソース グループを作成した場合は、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループとそのすべての内容を削除できます。

```azurecli
az group delete --name ContosoVMRG

az group delete --name ContosoRG
```

保持する既存のリソースを使用した場合は、[az monitor metrics alert delete](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-delete) コマンドを使用して、プラクティス アラートを削除します。

```azurecli
az monitor metrics alert delete --name alert-01

az monitor metrics alert delete --name alert-02
```

## <a name="azure-cli-commands-used-in-this-article"></a>この記事で使用されている Azure CLI コマンド

この記事では、次の Azure CLI コマンドが使用されています。

- [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create)
- [az appservice plan show](/cli/azure/appservice/plan#az_appservice_plan_show)
- [az group create](/cli/azure/group#az-group-create)
- [az group delete](/cli/azure/group#az-group-delete)
- [az monitor action-group create](/cli/azure/monitor/action-group#az_monitor_action_group_create)
- [az monitor metrics alert condition create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-condition-create)
- [az monitor metrics alert create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-create)
- [az monitor metrics alert delete](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-delete)
- [az monitor metrics alert dimension create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-dimension-create)
- [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions)
- [az vm show](/cli/azure/vm#az_vm_show)

## <a name="next-steps"></a>次のステップ

- [Azure Monitor CLI のサンプル](cli-samples.md)
- [Azure Monitor でのメトリック アラートのしくみを理解する](alerts/alerts-metric-overview.md)
