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
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: 32c09f06cea97024437e7e339407d344194a14ae
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2017
---
# <a name="set-up-oms-log-analytics-for-a-cluster"></a>クラスターに OMS Log Analytics を設定する

OMS ワークスペースは、Azure Resource Manager、または Azure Marketplace から設定できます。 今後の使用に備えてデプロイのテンプレートを維持する場合は、前者を使用します。 診断が有効な状態でデプロイされているクラスターが既にある場合は、Marketplace を使用したほうが簡単にデプロイを行えます。

> [!NOTE]
> クラスターまたはプラットフォーム レベルのイベントを確認するには、クラスターの診断を有効にする必要があります。これは、OMS が正常にクラスターを監視できるように設定可能にするためです。

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
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]",
    ```

3. テンプレートの変数に Service Fabric OMS ソリューションを追加します。

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric",
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
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
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

5. テンプレートを Resource Manager のアップグレードとして、クラスターにデプロイします。 これを行うには AzureRM PowerShell モジュール内の `New-AzureRmResourceGroupDeployment` API を使用します。 以下はコマンド例です。

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager では、これが既存のリソースに対する更新であることを認識することが可能です。 既存のデプロイで使用されているテンプレートと、指定された新しいテンプレートの間の変更点だけを処理します。

## <a name="deploying-oms-using-azure-marketplace"></a>Azure Marketplace を使用した OMS のデプロイ

クラスターをデプロイした後に OMS ワークスペースを追加する場合は、(ポータル内の) Azure Marketplace に移動して *"Service Fabric Analytics"* を探します。

1. 左側のナビゲーション メニューで **[新規]** をクリックします。 

2. 「*Service Fabric Analytics*」を検索します。 表示されるリソースをクリックします。

3. **[作成]** をクリックします。

    ![Marketplace 内の OMS SF Analytics](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Service Fabric Analytics 作成ウィンドウで *[OMS ワークスペース]* フィールドの **[ワークスペースを選択します]** をクリックし、**[新しいワークスペースの作成]** をクリックします。 必要なエントリを入力します。ここでの要件は、Service Fabric クラスターと OMS ワークスペースのサブスクリプションが同じであることだけです。 入力が検証されると、OMS ワークスペースは展開を開始します。 わずか数分で完了します。

5. 完了したら、Service Fabric Analytics 作成ウィンドウの下部にある **[作成]** をもう一度クリックします。 新しいワークスペースが *[OMS ワークスペース]* に表示されていることを確認します。 作成したワークスペースにソリューションが追加されます。

6. ワークスペースは引き続き、クラスターから取得する診断データとの接続を維持しておく必要があります。 Service Fabric Analytics ソリューションを作成したリソース グループに移動します。 *ServiceFabric(\<nameOfOMSWorkspace\>)* が表示されます。 ソリューションをクリックし、その概要ページに移動します。概要ページでソリューション設定やワークスペース設定を変更し、OMS ポータルに移動できます。

7. 左側のナビゲーション メニューの *[ワークスペースのデータ ソース]* の下にある **[ストレージ アカウント ログ]** をクリックします。

8. *[ストレージ アカウント ログ]* ページの上部にある **[追加]** をクリックし、クラスターのログをワークスペースに追加します。

9. **[ストレージ アカウント]** をクリックし、クラスターで作成された適切なアカウントを追加します。 既定の名前を使用した場合、ストレージ アカウントの名前は *sfdg\<resourceGroupName\>* になります。 これはクラスターの展開に使用した Azure Resource Manager テンプレートを確認する方法でも確認できます。`applicationDiagnosticsStorageAccountName` に使用された値を確認します。 アカウント名が表示されない場合、下にスクロールし、**[さらに読み込む]** をクリックしてください。 正しいストレージ アカウント名が表示されたらクリックして選択します。

10. 次に、*[データ型]* を指定する必要があります。これは **[Service Fabric イベント]** に設定します。

11. *[ソース]* は自動的に *[WADServiceFabric\*EventTable]* に設定されます。

12. **[OK]** をクリックし、ワークスペースをクラスターのログに接続します。

    ![OMS にストレージ アカウント ログを追加する](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

現在、アカウントがワークスペースのデータ ソースの *[ストレージ アカウント ログ]* に表示されているはずです。

この表示で、クラスターのプラットフォームとアプリケーション ログ テーブルに正しく接続されている OMS Log Analytics ワークスペースに Service Fabric Analytics ソリューションが追加されていることになります。 同じ方法でソースをワークスペースに追加できます。

## <a name="next-steps"></a>次のステップ
* お使いのノードに [OMS エージェントをデプロイ](service-fabric-diagnostics-oms-agent.md)してパフォーマンス カウンターを収集し、Docker の統計とコンテナーのログを収集する
* Log Analytic の一部として提供されている[ログ検索とクエリ](../log-analytics/log-analytics-log-searches.md)機能に詳しくなる
* [Log Analytics のビュー デザイナーを使用してカスタム ビューを作成する](../log-analytics/log-analytics-view-designer.md)
