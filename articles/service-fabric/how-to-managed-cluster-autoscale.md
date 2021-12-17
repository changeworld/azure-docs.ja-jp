---
title: Service Fabric マネージド クラスター ノードの自動スケールを構成する
description: Service Fabric マネージド クラスターで自動スケール ポリシーを構成する方法について説明します。
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 6c2dd14535b726d242d7076bcac94894e4c560dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092147"
---
# <a name="introduction-to-autoscaling-on-service-fabric-managed-clusters-preview"></a>Service Fabric マネージド クラスターでの自動スケールの概要 (プレビュー)
[自動スケーリング](../azure-monitor/autoscale/autoscale-overview.md)により、優れた弾力性が得られ、セカンダリ ノード タイプでオンデマンドでノードを追加または縮小できるようになります。 この自動化された柔軟な動作により、ワークロードを処理するノードの量を監視および最適化することで、管理のオーバーヘッドと潜在的なビジネスへの影響が軽減されます。 ワークロードの規則を構成し、残りの部分を自動スケールによって処理できるようにします。 定義したしきい値に達すると、自動スケーリング規則が実行されてノード タイプの容量が調整されます。 自動スケールは、いつでも有効にしたり、無効にしたり、構成したりすることができます。 この記事では、デプロイの例、自動スケールを有効または無効にする方法、および自動スケール ポリシーの例を構成する方法について説明します。


**要件とサポートされるメトリクス:**
* マネージド クラスターで自動スケーリングを使用するには、API バージョン `2021-07-01-preview` 以降を使用している必要があります。
* クラスター SKU は Standard である必要があります。
* は、クラスター内のセカンダリ ノード タイプでのみ構成できます。
* ノードの種類に対して自動スケーリングを有効にした後、リソースを再デプロイするときに `vmInstanceCount` プロパティを `-1` に構成します。
* [発行された Azure Monitor メトリクス](../azure-monitor/essentials/metrics-supported.md)のみがサポートされます。

自動スケーリングが役に立つ一般的なシナリオは、特定のサービスにかかる負荷が時間の経過と共に変化する場合です。 たとえば、ゲートウェイなどのサービスは、着信要求を処理するために必要なリソースの量に基づいてスケーリングできます。 これらのスケーリング ルールの例を見てみましょう。これらのルールは、記事の後半で使用します。
* ゲートウェイのすべてのインスタンスが平均して 70% を超えて使用している場合は、インスタンスを 2 つ追加することで、ゲートウェイ サービスをスケールアウトします。 これは 30 分ごとに実行されますが、合計で 20 個を超えるインスタンスを持つことはできません。
* ゲートウェイのすべてのインスタンスが平均して 40% 未満のコアを使用している場合は、インスタンスを 1 つ削除することで、サービスをスケールインします。 この操作を 30 分ごとに行いますが、インスタンスの合計数が 3 つより少なくなることはありません。

## <a name="example-autoscale-deployment"></a>自動スケーリング デプロイの例

この例では、次の手順を実行します。 
* 標準 SKU を作成するには、2 つのノード タイプを持つ Service Fabric マネージド クラスター `NT1` と `NT2` を既定で作成します。
* セカンダリ ノード タイプ `NT2` に自動スケーリング規則を追加します。

>[!NOTE] 
> ノードの種類の自動スケールは、マネージド クラスターの VMSS CPU ホスト メトリックに基づいて行われます。 VMSS リソースは、テンプレートで自動解決されます。 


次に、自動スケールが構成されたクラスターの設定手順について説明します。

1) リージョンにリソース グループを作成する

   ```powershell 
   Login-AzAccount
   Select-AzSubscription -SubscriptionId $subscriptionid
   New-AzResourceGroup -Name $myresourcegroup -Location $location
   ```

2) クラスター リソースを作成する

   このサンプルの [Standard SKU Service Fabric マネージド クラスターのサンプル](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-Autoscale/azuredeploy.json)をダウンロードします。次のコマンドを実行して、クラスター リソースをデプロイします。

   ```powershell
   $parameters = @{ 
   clusterName = $clusterName
   adminPassword = $VmAdminPassword
   clientCertificateThumbprint = $clientCertificateThumbprint
   } 
   New-AzResourceGroupDeployment -Name "deploy_cluster" -ResourceGroupName $resourceGroupName -TemplateFile .\azuredeploy.json -TemplateParameterObject $parameters -Verbose
   ```

3) セカンダリ ノード タイプで自動スケーリング規則を構成して有効にする
 
   次のコマンドを使用して、自動スケーリングを構成するために使用する、[マネージド クラスターの自動スケーリングのサンプル テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-Autoscale/sfmc-deploy-autoscale.json)をダウンロードします。

   ```powershell
   $parameters = @{ 
   clusterName = $clusterName
   }
   New-AzResourceGroupDeployment -Name "deploy_autoscale" -ResourceGroupName $resourceGroupName -TemplateFile .\sfmc-deploy-autoscale.json -TemplateParameterObject $parameters -Verbose 
   ```

>[!NOTE]
> このデプロイが完了した後、将来のクラスター リソースのデプロイでは、自動スケーリング規則が有効になっているセカンダリ ノード タイプで `vmInstanceCount` プロパティを `-1` に設定する必要があります。 これにより、クラスター デプロイが自動スケールと競合しないようになります。


## <a name="enable-or-disable-autoscaling-on-a-secondary-node-type"></a>セカンダリ ノード タイプで自動スケールを有効または無効にする

Service Fabric マネージド クラスターによってデプロイされたノードの種類では、既定で自動スケールが有効になりません。 自動スケールは、構成され、使用可能なノードの種類ごとに、いつでも有効または無効にすることができます。

この機能を有効にするには、次に示すように、ARM テンプレートのタイプ `enabled` の下で `Microsoft.Insights/autoscaleSettings` プロパティを構成します。

```JSON
    "resources": [
            {
            "type": "Microsoft.Insights/autoscaleSettings",
            "apiVersion": "2015-04-01",
            "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
            "location": "[resourceGroup().location]",
            "properties": {
                "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
                "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'), '/nodetypes/', parameters('nodeType2Name'))]",
                "enabled": true,
            ...
```

自動スケーリングを無効にするには、値を `false` に設定します。

## <a name="delete-autoscaling-rules"></a>自動スケール ルールを削除する

ノードの種類に設定されている自動スケール ポリシーを削除するには、次の PowerShell コマンドを実行します。

```PowerShell
Remove-AzResource -ResourceId "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/microsoft.insights/autoscalesettings/$name" -Force
```

## <a name="set-policies-for-autoscaling"></a>自動スケールのポリシーを設定する

 Service Fabric マネージド クラスターは、既定では[自動スケーリングのポリシー](../azure-monitor/autoscale/autoscale-understanding-settings.md)を構成しません。 基になるリソースでスケーリング アクションを実行するには、自動スケール ポリシーを構成する必要があります。

次の例では、`nodeType2Name` のポリシーを 3 ノード以上に設定しますが、最大 20 ノードまでスケールアップできます。 これは、過去 30 分間の平均 CPU 使用率が 70% であり、1 分単位でスケールアップをトリガーします。 過去 30 分間の平均 CPU 使用率が 40% 未満であり、1 分単位でスケールダウンをトリガーします。

```JSON
    "resources": [
            {
            "type": "Microsoft.Insights/autoscaleSettings",
            "apiVersion": "2015-04-01",
            "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
            "location": "[resourceGroup().location]",
            "properties": {
                "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
                "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'), '/nodetypes/', parameters('nodeType2Name'))]",
                "enabled": "[parameters('enableAutoScale')]",
                "profiles": [
                    {
                        "name": "Autoscale by percentage based on CPU usage",
                        "capacity": {
                            "minimum": "3",
                            "maximum": "20",
                            "default": "3"
                        },
                        "rules": [
                            {
                                "metricTrigger": {
                                  "metricName": "Percentage CPU",
                                  "metricNamespace": "",
                                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/SFC_', reference(resourceId('Microsoft.ServiceFabric/managedClusters', parameters('clusterName')), '2021-07-01-preview').clusterId,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('nodeType2Name'))]",
                                  "timeGrain": "PT1M",
                                  "statistic": "Average",
                                  "timeWindow": "PT30M",
                                  "timeAggregation": "Average",
                                  "operator": "GreaterThan",
                                  "threshold": 70
                                },
                                "scaleAction": {
                                  "direction": "Increase",
                                  "type": "ChangeCount",
                                  "value": "5",
                                  "cooldown": "PT5M"
                                }
                            },
                            {
                                "metricTrigger": {
                                  "metricName": "Percentage CPU",
                                  "metricNamespace": "",
                                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/SFC_', reference(resourceId('Microsoft.ServiceFabric/managedClusters', parameters('clusterName')), '2021-07-01-preview').clusterId,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('nodeType2Name'))]",
                                  "timeGrain": "PT1M",
                                  "statistic": "Average",
                                  "timeWindow": "PT30M",
                                  "timeAggregation": "Average",
                                  "operator": "LessThan",
                                  "threshold": 40
                                },
                                "scaleAction": {
                                  "direction": "Decrease",
                                  "type": "ChangeCount",
                                  "value": "1",
                                  "cooldown": "PT5M"
                                }
                            }
                            ]
                    }
                    ]
                }
            }
        
    ]                           
```

上記の例を含むこの [ARM テンプレートをダウンロードして、自動スケーリングを有効](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-Autoscale/sfmc-deploy-autoscale.json)にすることができます


## <a name="view-configured-autoscale-definitions-of-your-managed-cluster-resource"></a>マネージド クラスター リソースの構成された自動スケール定義を表示する

構成された自動スケーリング設定を表示するには、[Azure Resource Explorer](https://resources.azure.com/) を使用します。

1) [Azure Resource Explorer](https://resources.azure.com/) に移動します

2) `subscriptions` -> `SubscriptionName` -> `resource group` -> `microsoft.insights` -> `autoscalesettings` -> 自動スケーリング ポリシー名 (`sfmc01-NT2` など) に移動します。 

   ナビゲーション ツリーには次のような内容が表示されます。

   ![Azure Resource Explorer ツリービューの例][autoscale-are-tree]


3) 右側に、この自動スケール設定の定義がすべて表示されます。 

   この例では、自動スケーリングの構成に CPU% ベースのスケールアウトとスケールイン規則が使用されています。

   ![ノード タイプの自動スケールの詳細の Azure Resource Explorer 例][autoscale-nt-details]



## <a name="troubleshooting"></a>トラブルシューティング

以下の点を考慮してください。 

* マネージド クラスターのセカンダリ ノードの種類に対してトリガーされている自動スケール イベントを確認する

   1) クラスターのアクティビティ ログにアクセスする
   2) 自動スケールのスケールアップまたはダウン完了操作のアクティビティ ログを確認する

* ノードの種類に対して構成されている VM の数はいくつでしょうか。それらのすべてでワークロードが発生しているか、一部のみでしょうか。

* スケールインとスケールアウトのしきい値の間に十分な差を確保していますか。

   たとえば、平均 CPU 使用率が 5 分間にわたって 50% を超えた場合にスケールアウトし、平均 CPU 使用率が 50% を割り込んだときにスケールインする規則を設定したとします。 この設定は、CPU 使用率がこのしきい値に近付くと、スケール アクションによってセットのサイズが絶え間なく増減される "フラッピング" 問題をよく引き起こします。 この設定が原因で、自動スケーリング サービスはこの "フラッピング" を防止しようとするため、自動スケーリングが実行されなくなる場合があります。 そのため、スケールアウトとスケールインのしきい値の間に十分な差を付けて、自動スケーリングが実行される余裕を確保する必要があります。

* ノードの種類をスケールインまたはスケールアウトできますか。
   ノードの種類レベルでノードの数を調整し、正常に完了することを確認します。 [マネージド クラスターでノードの種類をスケールする方法](./how-to-managed-cluster-modify-node-type.md#scale-a-node-type-manually-with-a-template)

* Microsoft ServiceFabric/managedclusters/nodetypes と Microsoft を確認します。Azure Resource Explorer 内のリソースのインサイト

   Azure Resource Explorer は Azure Resource Manager リソースの状態を表示できるため、トラブルシューティングには欠かせません。 サブスクリプションをクリックし、トラブルシューティングを行うリソース グループを表示します。 `ServiceFabric/managedclusters/clustername` リソース プロバイダーの下で、作成したノードの種類の `NodeTypes` の下を確認し、検証する `provisioningState` プロパティが `Succeeded` であることを確認します。 次に、`clustername` の下の Microsoft.Insights リソース プロバイダーに移動し、自動スケーリング規則が適切かどうかを確認します。 

* 生成されたメトリック値は期待どおりですか。
   `Get-AzMetric` [PowerShell モジュールを使用して、リソースのメトリック値を取得し](/powershell/module/az.monitor/get-azmetric)、確認します。


これまでの手順を終えても、自動スケーリングに関する問題が解決しない場合は、「[サポート リクエストを記録](./service-fabric-support.md#create-an-azure-support-request)」リソースをご利用ください。 テンプレートと、パフォーマンス データのビューを共有できるように準備しておいてください。


## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Azure Monitor 自動スケーリングのサポートについて確認する](../azure-monitor/autoscale/autoscale-overview.md)
> [!div class="nextstepaction"]
> [Azure Monitor のメトリックをレビューする](../azure-monitor/essentials/data-platform-metrics.md)
> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスターの構成オプション](how-to-managed-cluster-configuration.md)

[autoscale-are-tree]: ./media/how-to-managed-cluster-autoscale/autoscale-are-tree.png
[autoscale-nt-details]: ./media/how-to-managed-cluster-autoscale/autoscale-nt-details.png


