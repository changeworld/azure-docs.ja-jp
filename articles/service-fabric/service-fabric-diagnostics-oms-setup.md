---
title: "Azure Service Fabric - OMS Log Analytics での監視の設定 | Microsoft Docs"
description: "Azure Service Fabric クラスターの監視に OMS を使用したイベントの視覚化と分析を設定する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2017
ms.author: dekapur
ms.openlocfilehash: 53b06c5a1395f34c96d4011366835a920d5c670b
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2018
---
# <a name="set-up-oms-log-analytics-for-a-cluster"></a>クラスターに OMS Log Analytics を設定する

OMS ワークスペースは、Azure Resource Manager、PowerShell、または Azure Marketplace から設定できます。 将来のためにデプロイの更新された Resource Manager テンプレートを保持している場合は、同じテンプレートを使って OMS 環境を設定します。 診断が有効な状態でデプロイされているクラスターが既にある場合は、Marketplace を使用したほうが簡単にデプロイを行えます。 OMS をデプロイするアカウントにサブスクリプション レベルのアクセス許可がない場合は、PowerShell を使うか、または Resource Manager テンプレートを使ってデプロイします。

> [!NOTE]
> クラスターまたはプラットフォーム レベルのイベントを確認するには、クラスターの診断を有効にする必要があります。これは、OMS が正常にクラスターを監視できるように設定可能にするためです。

## <a name="deploying-oms-using-azure-marketplace"></a>Azure Marketplace を使用した OMS のデプロイ

クラスターをデプロイした後で OMS ワークスペースを追加した方がよい場合は、(Portal で) Azure Marketplace に移動して、"*Service Fabric Analytics*" を探します。

1. 左側のナビゲーション メニューで **[新規]** をクリックします。 

2. 「*Service Fabric Analytics*」を検索します。 表示されるリソースをクリックします。

3. **[作成]** をクリックします。

    ![Marketplace 内の OMS SF Analytics](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Service Fabric Analytics 作成ウィンドウで *[OMS ワークスペース]* フィールドの **[ワークスペースを選択します]** をクリックし、**[新しいワークスペースの作成]** をクリックします。 必要なエントリを入力します。ここでの要件は、Service Fabric クラスターと OMS ワークスペースのサブスクリプションが同じであることだけです。 入力が検証されると、OMS ワークスペースはデプロイを開始します。 わずか数分で完了します。

5. 完了したら、Service Fabric Analytics 作成ウィンドウの下部にある **[作成]** をもう一度クリックします。 新しいワークスペースが *[OMS ワークスペース]* に表示されていることを確認します。 作成したワークスペースにソリューションが追加されます。

Windows を使っている場合は、次の手順に進み、クラスター イベントが格納されているストレージ アカウントに OMS を接続します。 Linux クラスターでこのエクスペリエンスを適切に有効にする作業はまだ進行中です。 それまでの間は、引き続きクラスターに OMS エージェントを追加してください。  

1. ワークスペースは引き続き、クラスターから取得する診断データとの接続を維持しておく必要があります。 Service Fabric Analytics ソリューションを作成したリソース グループに移動します。 *ServiceFabric(\<nameOfOMSWorkspace\>)* が表示されます。 ソリューションをクリックし、その概要ページに移動します。概要ページでソリューション設定やワークスペース設定を変更し、OMS ポータルに移動できます。

2. 左側のナビゲーション メニューの *[ワークスペースのデータ ソース]* の下にある **[ストレージ アカウント ログ]** をクリックします。

3. *[ストレージ アカウント ログ]* ページの上部にある **[追加]** をクリックし、クラスターのログをワークスペースに追加します。

4. **[ストレージ アカウント]** をクリックし、クラスターで作成された適切なアカウントを追加します。 既定の名前を使用した場合、ストレージ アカウントの名前は *sfdg\<resourceGroupName\>* になります。 これはクラスターの展開に使用した Azure Resource Manager テンプレートを確認する方法でも確認できます。`applicationDiagnosticsStorageAccountName` に使用された値を確認します。 アカウント名が表示されない場合、下にスクロールし、**[さらに読み込む]** をクリックしてください。 正しいストレージ アカウント名が表示されたらクリックして選択します。

5. 次に、*[データ型]* を指定する必要があります。これは **[Service Fabric イベント]** に設定します。

6. *[ソース]* は自動的に *[WADServiceFabric\*EventTable]* に設定されます。

7. **[OK]** をクリックし、ワークスペースをクラスターのログに接続します。

    ![OMS にストレージ アカウント ログを追加する](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

現在、アカウントがワークスペースのデータ ソースの *[ストレージ アカウント ログ]* に表示されているはずです。

この表示で、クラスターのプラットフォームとアプリケーション ログ テーブルに正しく接続されている OMS Log Analytics ワークスペースに Service Fabric Analytics ソリューションが追加されていることになります。 同じ方法でソースをワークスペースに追加できます。


## <a name="deploying-oms-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用した OMS のデプロイ

Resource Manager テンプレートを使用してクラスターをデプロイする場合は、テンプレートが新しい OMS ワークスペースを作成して Service Fabric ソリューションをそこに追加し、適切なストレージ テーブルからデータを読み取るように構成します。

[こちら](https://azure.microsoft.com/resources/templates/service-fabric-oms/)に使用可能なテンプレートのサンプルがあります。このテンプレートは、必要に応じて変更できます。 OMS ワークスペースを設定するためのさまざまなオプションを使用できるその他のテンプレートについては、[Service Fabric と OMS のテンプレート](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS)をご覧ください。

主な変更点は次のとおりです。

1. パラメーターに `omsWorkspaceName` と `omsRegion` を追加します。 これは *template.json* ファイルで定義されたパラメーターに、次のスニペットを追加することを意味します。 既定の値は、環境に応じて自由に変更してください。 新しい 2 つのパラメーターを *parameters.json* に追加して、その値をリソースのデプロイ向けに定義する必要もあります。
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
    ```

    `omsRegion` の値は、特定の一連の値に一致している必要があります。 お使いのクラスターのデプロイに最も近いものを選択してください。

2. OMS に任意のアプリケーション ログを送信する場合は、`applicationDiagnosticsStorageAccountType` と `applicationDiagnosticsStorageAccountName` がテンプレートのパラメーターに含まれていることを確認します。 含まれていない場合は、それらを変数のセクションに追加し、必要に応じてその値を編集します。 必要に応じて、それらの値を上記で使用している形式に従ってパラメーターとして含めることもできます。

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. テンプレートの変数に Service Fabric OMS ソリューションを追加します。

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. お使いのリソース セクションの最後で、Service Fabric クラスター リソースの宣言の後に、次を追加します。

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', parameters('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "id": "[resourceId('Microsoft.OperationsManagement/solutions/', variables('solution'))]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
    ```
    
    > [!NOTE]
    > 変数として `applicationDiagnosticsStorageAccountName` を追加した場合は、それに対する各参照を `parameters('applicationDiagnosticsStorageAccountName')` ではなく `variables('applicationDiagnosticsStorageAccountName')` に変更してください。

5. テンプレートを Resource Manager のアップグレードとして、クラスターにデプロイします。 これを行うには AzureRM PowerShell モジュール内の `New-AzureRmResourceGroupDeployment` API を使用します。 以下はコマンド例です。

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager では、これが既存のリソースに対する更新であることを認識することが可能です。 既存のデプロイで使用されているテンプレートと、指定された新しいテンプレートの間の変更点だけを処理します。

## <a name="deploying-oms-using-azure-powershell"></a>Azure PowerShell を使用した OMS のデプロイ

PowerShell を使って OMS Log Analytics リソースをデプロイすることもできます。 これは、`New-AzureRmOperationalInsightsWorkspace` コマンドを使って行います。 そのためには、[Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1) がインストールされていることを確認します。 このスクリプトを使って、新しい OMS Log Analytics ワークスペースを作成し、Service Fabric ソリューションを追加します。 

```ps

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<OMS Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

それが完了した後、クラスターが Windows クラスターである場合は、前のセクションの手順に従って、適切なストレージ アカウントに OMS Log Analytics を接続します。

PowerShell を使って、他のソリューションを追加したり、OMS ワークスペースに他の変更を行うこともできます。 詳しくは、「[PowerShell を使用した Log Analytics の管理](../log-analytics/log-analytics-powershell-workspace-configuration.md)」をご覧ください

## <a name="next-steps"></a>次の手順
* お使いのノードに [OMS エージェントをデプロイ](service-fabric-diagnostics-oms-agent.md)してパフォーマンス カウンターを収集し、Docker の統計とコンテナーのログを収集する
* Log Analytic の一部として提供されている[ログ検索とクエリ](../log-analytics/log-analytics-log-searches.md)機能に詳しくなる
* [Log Analytics のビュー デザイナーを使用してカスタム ビューを作成する](../log-analytics/log-analytics-view-designer.md)
