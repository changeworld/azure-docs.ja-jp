---
title: 'クイックスタート: Azure Resource Manager テンプレートを使用して NSG フロー ログを構成する'
description: Azure Resource Manager テンプレート (ARM テンプレート) と Azure PowerShell を使用して、プログラムで NSG フロー ログを有効にする方法について説明します。
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG flow logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 96f30c05527754cbce3b7593c8d62fb56844d41e
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042760"
---
# <a name="quickstart-configure-nsg-flow-logs-using-an-arm-template"></a>クイックスタート: ARM テンプレートを使用して NSG フロー ログを構成する

このクイックスタートでは、[Azure Resource Manager](../azure-resource-manager/management/overview.md) テンプレート (ARM テンプレート) と Azure PowerShell を使用して [NSG フロー ログ](network-watcher-nsg-flow-logging-overview.md)を有効にします。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

まず、NSG フロー ログ オブジェクトのプロパティの概要と、いくつかのサンプル テンプレートを提供します。 次に、ローカルの PowerShell インスタンスを使用してテンプレートをデプロイします。

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create)からのものです。

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

テンプレートでは、複数のリソースが定義されています。

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>NSG フロー ログ オブジェクト

すべてのパラメーターが指定された NSG フロー ログ オブジェクトを以下に示します。 プロパティの詳細については、「[Microsoft.Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs)」を参照してください。

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
        "enabled": "boolean",
        "workspaceResourceId": "string",
        "trafficAnalyticsInterval": "integer"
      },
      "retentionPolicy": {
        "days": "integer",
        "enabled": "boolean"
      },
      "format": {
        "type": "string",
        "version": "integer"
      }
    }
  }
}
```

`Microsoft.Network/networkWatchers/flowLogs` リソースを作成するには、テンプレートのリソース セクションに上記の JSON を追加します。

## <a name="creating-your-template"></a>テンプレートを作成する

ARM テンプレートを初めて使用する場合は、以下のリンクから詳細を確認できます。

- [ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [チュートリアル:初めての ARM テンプレートを作成してデプロイする](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create)からのものです。

以下に示す完全なテンプレートの例は、NSG フロー ログを設定するための最小限のパラメーターを渡す最も単純なバージョンです。 その他の例については、こちらの[ハウツー ガイド](network-watcher-nsg-flow-logging-azure-resource-manager.md)に移動してください。

**例** :次のテンプレートを使用すると、ターゲット NSG の NSG フロー ログが有効になり、特定のストレージ アカウントに格納されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {},
        "retentionPolicy": {},
        "format": {}
      }
    }
  ]
}
```

> [!NOTE]
> - リソースの名前の形式は、" _親リソース_子リソース_ " となります。 ここで、親リソースはリージョンの Network Watcher インスタンス (形式:NetworkWatcher_RegionName。 例:NetworkWatcher_centraluseuap)
> - `targetResourceId` は、ターゲット NSG のリソース ID です。
> - `storageId` は、コピー先のストレージ アカウントのリソース ID です。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

このチュートリアルでは、既存のリソース グループと、フローのログ記録を有効にできる NSG があることを前提としています。
上記の例のテンプレートは、すべて `azuredeploy.json` としてローカルに保存できます。 プロパティ値を更新して、サブスクリプション内の有効なリソースを参照するようにします。

テンプレートをデプロイするには、PowerShell で次のコマンドを実行します。

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> 上記のコマンドでは、NetworkWatcherRG リソース グループにリソースがデプロイされ、NSG を含むリソース グループにはデプロイされません

## <a name="validate-the-deployment"></a>デプロイの検証

デプロイが成功したかどうかを確認するには、いくつかの方法があります。 PowerShell コンソールでは、`ProvisioningState` が `Succeeded` と表示されます。 また、変更内容を確認するには、[NSG フロー ログのポータル ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)を参照してください。 デプロイに問題がある場合は、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](../azure-resource-manager/templates/common-deployment-errors.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure では、`Complete` デプロイ モードによってリソースを削除できます。 フロー ログ リソースを削除するには、削除するリソースを含めずに、`Complete` モードでデプロイを指定します。 [完全デプロイ モード](../azure-resource-manager/templates/deployment-modes.md#complete-mode)の詳細をお読みください。

または、以下の手順に従って、NSG フロー ログを Azure portal から無効にすることもできます。

1. Azure portal にログインします。
1. ポータルの左上隅の **[すべてのサービス]** を選択します。 _[フィルター]_ ボックスに「 **Network Watcher** 」と入力します。 検索結果に **[Network Watcher]** が表示されたら、それを選択します。
1. **[ログ]** の **[NSG フロー ログ]** を選択します。
1. NSG の一覧から、フロー ログを無効にする NSG を選択します。
1. **[フローのログ設定]** で、フロー ログの状態を **[オフ]** に設定します。
1. 下へスクロールし、 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、NSG フロー ログを有効にしました。 この後は、以下の記事を使用して、NSG フロー データを視覚化する方法について学習しましょう。

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [オープン ソース ツール](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure Traffic Analytics](traffic-analytics.md)
