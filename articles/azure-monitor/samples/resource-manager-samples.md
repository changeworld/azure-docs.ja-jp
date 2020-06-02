---
title: Azure Monitor 用の Resource Manager テンプレートのサンプル
description: Resource Manager テンプレートを使用して Azure Monitor の機能をデプロイし構成します
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: 2c9287e6f4bda429309dd0041215b271678d03d3
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860700"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Azure Monitor 用の Resource Manager テンプレートのサンプル

[Azure Resource Manager テンプレート](../../azure-resource-manager/templates/template-syntax.md)を使用すると、Azure Monitor を大規模にデプロイし、構成することができます。 以下の記事では、さまざまな Azure Monitor 機能向けのサンプル テンプレートを紹介しています。 これらのサンプルは特定の要件に合わせて変更可能であり、Resource Manager テンプレートの標準的なデプロイ方法によってデプロイできます。 

## <a name="deploying-the-sample-templates"></a>サンプル テンプレートのデプロイ
サンプルの基本的な使用手順は次のとおりです。

1. テンプレートをコピーし JSON ファイルとして保存します。
2. パラメーターを環境に合わせて変更し、JSON ファイルとして保存します。
4. [任意の Resource Manager テンプレートのデプロイ方法](../../azure-resource-manager/templates/deploy-powershell.md)に従って、テンプレートをデプロイします。 

たとえば、PowerShell または Azure CLI を使用してサブスクリプションにテンプレートとパラメーター ファイルをデプロイする場合は、次のコマンドを実行します。


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>サンプル テンプレート一覧

- [エージェント](resource-manager-agent.md) - Log Analytics エージェントと診断拡張機能をデプロイして構成します。
- 警告
  - [ログ アラート ルール](resource-manager-alerts-log.md) - ログ クエリと Azure アクティビティ ログからのアラート。
  - [メトリック アラート ルール](resource-manager-alerts-metric.md) - さまざまなロジックを使用したメトリックからのアラート。
- Application Insights - 近日公開予定。
- [診断設定](resource-manager-diagnostic-settings.md) - 各種リソースからのログおよびメトリックを転送する診断設定を作成します。
- [ログ クエリ](resource-manager-log-queries.md) - ログ クエリを作成して Log Analytics ワークスペースに保存します。
- [Log Analytics ワークスペース](resource-manager-workspace.md) - Log Analytics ワークスペースを作成し、Log Analytics エージェントの各種データ ソースから成るコレクションを構成します。
- [ブック](resource-manager-workbooks.md) - ブックを作成します。
- [Azure Monitor for containers](resource-manager-container-insights.md) - Azure Monitor for containers にクラスターをオンボードします。
- [Azure Monitor for VMs](resource-manager-vminsights.md) - Azure Monitor for VMs に仮想マシンをオンボードします。



## <a name="next-steps"></a>次のステップ

- [Resource Manager テンプレート](../../azure-resource-manager/templates/overview.md)の詳細を確認します
