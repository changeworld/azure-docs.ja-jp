---
title: Azure Kubernetes Service (AKS) の正常性を監視する (プレビュー) | Microsoft Docs
description: この記事では、AKS コンテナーのパフォーマンスを簡単に調査して、ホストしている Kubernetes 環境の使用率をすばやく把握する方法について説明します。
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2018
ms.author: magoedte
ms.openlocfilehash: 23109a74fa707759cc3300896392dcc129f3e28c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335756"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Azure Kubernetes Service (AKS) の正常性を監視する (プレビュー)

この記事では、Azure Kubernetes Service (AKS) 上でホストされている Kubernetes 環境に展開されているワークロードのパフォーマンスを監視するために、Azure Monitor コンテナーの正常性の監視を設定し使用する方法について説明します。  Kubernetes クラスターとコンテナーの監視は重要なことであり、複数のアプリケーションを含む大規模な運用クラスターを実行するときは特に重要です。

コンテナーの正常性の監視は、Kubernetes で使用可能なコントローラー、ノード、およびコンテナーから Metrics API 経由でメモリやプロセッサ メトリックを収集することにより、パフォーマンス監視機能を提供します。  コンテナーの正常性の監視を有効化すると、コンテナー化されたバージョンの OMS エージェント for Linux を使用してこれらのメトリックが自動的に収集され、[Log Analytics](../log-analytics/log-analytics-overview.md) ワークスペースに保存されます。  存在するコンテナーのワークロードと、Kubernetes クラスターのパフォーマンスの正常性に影響を与えている原因が、含まれている定義済みのビューに表示されるため、以下を実行できます。  

* ノードでどんなコンテナーが実行されており、そのプロセッサおよびメモリの平均使用率を表示することにより、リソースのボトルネックを特定します。
* コントローラーまたはポッドのどこにコンテナーが存在するかを特定し、コントローラーまたはポッド全体のパフォーマンスを表示します。 
* ポッドをサポートする標準プロセスと関連のない、ホスト上で実行されているワークロードのリソース使用率を確認します。
* 平均的な、および最大の負荷条件でのクラスターの動作を理解することにより、容量ニーズを特定し、それが維持できる最大負荷を判断します。 

構成、監査、リソースの使用率を確認するために Docker および Windows コンテナー ホストを監視および管理する必要がある場合は、[コンテナー監視ソリューション](../log-analytics/log-analytics-containers.md)を参照してください。

## <a name="requirements"></a>必要条件 
手順を開始する前に、次の詳細を確認して、サポートされている前提条件を理解してください。

- 新規または既存の AKS クラスター
- microsoft/oms:ciprod04202018 バージョン以上の、コンテナー化された OMS エージェント for Linux。 このエージェントは、コンテナーの正常性の監視を有効化すると自動的にインストールされます。  
- Log Analytics ワークスペース。  新しい AKS クラスターの監視を有効にしたときに作成できます。あるいは [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)、[PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json)、または [Azure Portal](../log-analytics/log-analytics-quick-create-workspace.md) から作成できます。
- コンテナーの監視を有効にするための Log Analytics 共同作成者ロールのメンバー。  Log Analytics ワークスペースへのアクセスを制御する方法の詳細については、「[ワークスペースを管理する](../log-analytics/log-analytics-manage-access.md)」を参照してください。

## <a name="components"></a>コンポーネント 

この機能では、コンテナー化された OMS エージェント for Linux を使用して、クラスター内のすべてのノードからパフォーマンス データおよびイベント データを収集します。  コンテナーの監視を有効にすると、エージェントは自動的に展開され、指定した Log Analytics ワークスペースに登録されます。 

>[!NOTE] 
>AKS クラスターが既に展開されている場合は、この記事の後半で説明されているように、提供されている Azure Resource Manager テンプレートを使用して監視を有効化できます。 `kubectl` を使用してアップグレード、エージェントを削除、再展開、または展開することはできません。  
>

## <a name="sign-in-to-azure-portal"></a>Azure portal にサインインする
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>新しいクラスターのコンテナーの正常性の監視を有効にする
監視を有効にできるのは、Azure Portal から展開した AKS クラスターのみです。  クイック スタートの記事、「[Azure Kubernetes Service (AKS) クラスターのデプロイ](../aks/kubernetes-walkthrough-portal.md)」の手順に従ってください。  **[監視]** ページの **[監視を有効にする]** オプションで **[はい]** を選択して、既存の Log Analytics ワークスペースを選択するか、または新しく作成します。  

監視を有効にし、すべての構成タスクが正常に完了すると、次の 2 つの方法のいずれかを使用して、クラスターのパフォーマンスを監視することができます。

1. 左側のウィンドウで **[正常性]** を選択し、AKS クラスターから直接監視します。<br><br> 
2. 選択したクラスターの AKS クラスター ページで **[Monitor container health]\(コンテナーの正常性を監視\)** タイルをクリックします。  Azure Monitor の左側のウィンドウにある **[正常性]** を選択します。  

![AKS でコンテナーの正常性の監視を選択するオプション](./media/monitoring-container-health/container-performance-and-health-select-01.png)

監視を有効にした後、クラスターの運用データが表示されるまで、15 分ほどかかります。  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>既存のマネージド クラスターのコンテナーの正常性の監視を有効にする
既に展開されている AKS コンテナーの監視を有効にするには、Azure portal を使用するか、提供されている Azure Resource Manager テンプレートで PowerShell コマンドレット **New-AzureRmResourceGroupDeployment** または Azure CLI を使用します。  


### <a name="enable-from-azure-portal"></a>Azure portal から有効にする
Azure portal から AKS コンテナーの監視を有効にするには、次の手順を実行します。

1. Azure Portal で、**[すべてのサービス]** をクリックします。 リソースの一覧で「**Containers**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Kubernetes サービス]** を選択します。<br><br> ![Azure Portal](./media/monitoring-container-health/azure-portal-01.png)<br><br>  
2. コンテナーの一覧で、コンテナーを選択します。
3. コンテナーの概要ページで **[Monitor コンテナーの正常性]** を選択します。**[Onboarding to Container Health and Logs]\(コンテナー正常性およびログのオンボード\)** ページが表示されます。
4. **[Onboarding to Container Health and Logs]\(コンテナー正常性およびログのオンボード\)** ページにクラスターと同じサブスクリプションの既存の Log Analytics ワークスペースが存在する場合は、ドロップダウン リストから選択します。  このリストでは、サブスクリプションで AKS コンテナーが展開されている既定のワークスペースと場所が事前に選択されています。 または、**[新規作成]** を選択して、同じサブスクリプション内に新しいワークスペースを指定することもできます。<br><br> ![AKS コンテナーの正常性の監視を有効にする](./media/monitoring-container-health/container-health-enable-brownfield.png) 

    **[新規作成]** を選択すると、**[新しいワークスペースの作成]** ウィンドウが表示されます。 **[リージョン]** の既定はコンテナー リソースが作成されたリージョンです。既定のままにするか、別のリージョンを選択してからワークスペースの名前を指定します。  **[作成]** をクリックして選択を確定します。<br><br> ![コンテナーの監視のワークスペースを定義する](./media/monitoring-container-health/create-new-workspace-01.png)  

    >[!NOTE]
    >現時点では、米国中西部リージョンに新しいワークスペースを作成することはできません。リージョン内の既存のワークスペースのみを選択することができます。  リストからそのリージョンを選択することはできますが、展開は開始されても、そのすぐ後に失敗します。  
    >
 
監視を有効にした後、クラスターの運用データが表示されるまで、15 分ほどかかります。 

### <a name="enable-using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用を有効にする
この方法には 2 つの JSON テンプレートが含まれています。1 つには、監視を有効にする構成が指定されており、もう 1 つの JSON テンプレートには、次を指定するパラメーター値が含まれています。

* AKS コンテナー リソース ID 
* クラスターが展開されているリソース グループ 
* ワークスペースを作成する Log Analytics ワークスペースとリージョン 

Log Analytics ワークスペースは、手動で作成する必要があります。  ワークスペースは、[Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)、[PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json)、[Azure Portal](../log-analytics/log-analytics-quick-create-workspace.md) のいずれかを使用して作成できます。

PowerShell でテンプレートを使用してリソースを展開する手順の詳細については、[Resource Manager テンプレートおよび Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)または [Resource Manager テンプレートおよび Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy-cli.md)に関する記事を参照してください。

Azure CLI を使用する場合は、まず CLI をインストールしローカルで使用する必要があります。  Azure CLI バージョン 2.0.27 以降を実行している必要があります。 `az --version` を実行してバージョンを確認します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページを参照してください。 

#### <a name="create-and-execute-template"></a>テンプレートを作成して実行する

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    },
    "workspaceRegion": {
    "type": "string",
    "metadata": {
       "description": "Azure Monitor Log Analytics workspace region"
      }
     }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     },
    {
        "type": "Microsoft.Resources/deployments",
        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
        "apiVersion": "2017-05-10",
        "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
        "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
        "properties": {
            "mode": "Incremental",
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "location": "[parameters('workspaceRegion')]",
                        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                        "properties": {
                            "workspaceResourceId": "[parameters('workspaceResourceId')]"
                        },
                        "plan": {
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                            "promotionCode": "",
                            "publisher": "Microsoft"
                        }
                    }
                ]
            },
            "parameters": {}
        }
       }
     ]
    }
    ```

2. このファイルを **existingClusterOnboarding.json** としてローカル フォルダーに保存します。
3. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
       "$schema": "https://schema.management.azure.com/  schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscroptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "East US"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "eastus"
       }
     }
    }
    ```

4. **aksResourceId** と、**aksResourceLocation** の値を、AKS クラスターの **[AKS Overview]\(AKS 概要\)** ページに表示されている値に編集します。  **workspaceResourceId** の値は、ワークスペース名を含む Log Analytics ワークスペースの完全なリソース ID です。  また、**workspaceRegion** 内の ワークスペースの場所を指定します。    
5. このファイルを **existingClusterParam.json** としてローカル フォルダーに保存します。
6. これでこのテンプレートをデプロイする準備が整いました。 

    * テンプレートが保存されているフォルダーから、次の PowerShell コマンドを使用します。

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが表示されます。

        ```powershell
        provisioningState       : Succeeded
        ```

    * Linux で Azure CLI を使用して次のコマンドを実行するには、次のコマンドを実行します。
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが表示されます。

        ```azurecli
        provisioningState       : Succeeded
        ```
監視を有効にした後、クラスターの運用データが表示されるまで、15 分ほどかかります。  

## <a name="verify-agent-deployed-successfully"></a>エージェントが正常に展開されていることを確認する
OMS エージェントが適切に展開されていることを確認するには、次のコマンドを実行します: `kubectl get ds omsagent --namespace=kube-system`。

適切に展開されている場合は、次のような出力になります。

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>パフォーマンス使用率の表示
コンテナーの正常性ページを開くと、すぐにクラスター ノードのパフォーマンスの使用率が表示されます。  AKS クラスターに関する情報は 3 つの観点から確認できます。

- Nodes 
- コントローラー  
- Containers

行階層では、クラスター内のノードから始まり、Kubernetes オブジェクト モデルに従います。  ノードを展開すると、ノードで実行されている 1 つまたは複数のポッドが表示され、ポッドに複数のコンテナーがグループ化されている場合は、階層の最後の行に表示されます。<br><br> ![パフォーマンス ビューの Kubernetes ノード階層の例](./media/monitoring-container-health/container-performance-and-health-view-03.png)

ページの上部からコントローラーまたはコンテナーを選択し、それらのオブジェクトの状態やリソース使用率を確認できます。  画面上部のドロップダウン ボックスを使用すると、名前空間、サービス、またはノードごとにフィルター処理できます。 また、**[メトリック]** ドロップダウン リストから **[Memory RSS]\(使用メモリ (RSS)\)** または **[メモリ ワーキング セット]** を選択すると、メモリ使用率を確認することもできます。  **[Memory RSS]\(使用メモリ (RSS)\)** は、Kubernetes 1.8 以降でのみサポートされています。 それ以外のバージョンでは、**AVG %** の値に、未定義、または表示できない値を示す数値データ型である *NaN%* が表示されます。 

![コンテナー パフォーマンス ノード パフォーマンス ビュー](./media/monitoring-container-health/container-performance-and-health-view-04.png)

既定では、パフォーマンス データは、過去 6 時間のものですが、ページの右上隅にある **[時間範囲]** ドロップダウン リストから時間枠を変更できます。 現時点では、ページは自動更新されないため、手動で更新する必要があります。 

次の例では、*aks agentpool 3402399 0* ノードの **[Containers]** の値は 10 で、これは展開されているコンテナーの合計数のロールアップです。<br><br> ![ノードあたりのコンテナーのロールアップの例](./media/monitoring-container-health/container-performance-and-health-view-07.png)<br><br> これにより、クラスター内のノード間のコンテナーのバランスが適切でないことがすばやく認識できます。  

次の表は、ノードを表示した場合に表示される情報を説明しています。

| 分割 | 説明 | 
|--------|-------------|
| Name | ホストの名前。 |
| 状態 | ノードの状態の Kubernetes ビュー |
| AVG % | 選択した期間中の、選択したメトリックに基づいたノードの平均割合。 |
| AVERAGE | 選択した期間中の、選択したメトリックに基づいたノードの実際の平均値。  平均値は、ノードに設定されている CPU、メモリの制限から測定されます。ポッドおよびコンテナーについては、ホストによって報告された平均値です。 |
| Containers | コンテナーの数。 |
| Uptime | ノードが起動または再起動されてから経過した時間を示します。 |
| Pod | コンテナー限定。 どのポッドが存在するかを示しています。 |
| コントローラー | コンテナーとポッド限定。 これには存在しているコントローラーが表示されます。 コントローラーにないポッドもあるため、一部は「該当なし」 (N/A) として表示されます。 | 
| Trend AVG% | コンテナーとノードの平均メトリック % に基づいた棒グラフ トレンドです。 |


セレクターから **[コントローラー]** を選択します。<br><br> ![コントローラーの選択ビュー](./media/monitoring-container-health/container-performance-and-health-view-08.png)

ここで、コントローラーのパフォーマンスの正常性を表示できます。<br><br> ![<名前> コントローラーのパフォーマンス ビュー](./media/monitoring-container-health/container-performance-and-health-view-05.png)

行階層はコントローラーで始まり、コントローラーを展開すると、1 つまたは複数のポッド、または 1 つまたは複数のコンテナーを表示できます。  ポッドを展開すると、ポッドにグループ化されているコンテナーが最後の行に表示されます。  

次の表は、コントローラーを表示した場合に表示される情報を示しています。

| 分割 | 説明 | 
|--------|-------------|
| Name | コントローラーの名前|
| 状態 | *[終了]*、*[失敗]*、*[停止]*、または *[一時停止]* などの、実行停止時のコンテナーの状態です。 コンテナーが実行されているのに状態が正しく表示されない、またはエージェントが状態を認識せず、コンテナーが 30 分以上応答していない場合は、*[不明]* 状態になります。 |
| AVG % | 各エンティティの選択されたメトリックの平均 % のロールアップ平均。 |
| AVERAGE | コンテナーの平均 CPU ミリコアまたはメモリ パフォーマンスのロールアップ。  平均値は、ポッドの CPU およびメモリ制限の設定から測定されます。 |
| Containers | コントローラーまたはポッドのコンテナーの合計数。 |
| Restarts | コンテナーの再起動数のロールアップ。 |
| Uptime | コンテナーが起動されてからの経過時間を表します。 |
| Pod | コンテナー限定。 どのポッドが存在するかを示しています。 |
| ノード | コンテナーとポッド限定。 これには存在しているコントローラーが表示されます。 | 
| Trend AVG% | コンテナーの平均メトリック % を表す棒グラフ トレンドです。 |

セレクターから **[コンテナー]** を選択します。<br><br> ![コンテナーの選択ビュー](./media/monitoring-container-health/container-performance-and-health-view-09.png)

ここには、コントローラーのパフォーマンスの正常性が表示されます。<br><br> ![<名前> コントローラーのパフォーマンス ビュー](./media/monitoring-container-health/container-performance-and-health-view-06.png)

次の表は、コンテナーを表示した場合に表示される情報を示しています。

| 分割 | 説明 | 
|--------|-------------|
| Name | コントローラーの名前|
| 状態 | 存在する場合、コンテナーのロールアップ状態です。 |
| AVG % | 各エンティティの選択されたメトリックの平均 % のロールアップ平均。 |
| AVERAGE | コンテナーの平均 CPU ミリコアまたはメモリ パフォーマンスのロールアップ。 平均値は、ポッドの CPU およびメモリ制限の設定から測定されます。 |
| Containers | コントローラーにあるコンテナーの合計数。|
| Restarts | コンテナーが起動されてからの経過時間を表します。 |
| Uptime | コンテナーが起動または再起動されてからの経過時間を表します。 |
| Pod | ポッドが配置されている場所のポッド情報です。 |
| ノード |  コンテナーが存在するノードです。  | 
| Trend AVG% | コンテナーの平均メトリック % を表す棒グラフ トレンドです。 |

## <a name="container-data-collection-details"></a>コンテナーのデータ収集の詳細
コンテナーの正常性の監視により、コンテナー ホストとコンテナーからさまざまなパフォーマンス メトリックとログ データが収集されます。 データは、3 分ごとに収集されます。

### <a name="container-records"></a>コンテナー レコード

次の表は、コンテナーの正常性の監視によって収集されるレコードと、ログ検索結果に表示されるデータ型の例を示します。

| データ型 | ログ検索のデータ型 | フィールド |
| --- | --- | --- |
| ホストとコンテナーのパフォーマンス | `Perf` | Computer、ObjectName、CounterName &#40;%Processor Time、Disk Reads MB、Disk Writes MB、Memory Usage MB、Network Receive Bytes、Network Send Bytes、Processor Usage sec、Network&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| コンテナー インベントリ | `ContainerInventory` | TimeGenerated、Computer、container name、ContainerHostname、Image、ImageTag、ContainerState、ExitCode、EnvironmentVar、Command、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| コンテナー イメージ インベントリ | `ContainerImageInventory` | TimeGenerated、Computer、Image、ImageTag、ImageSize、VirtualSize、Running、Paused、Stopped、Failed、SourceSystem、ImageID、TotalContainer |
| コンテナー ログ | `ContainerLog` | TimeGenerated、Computer、image ID、container name、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| コンテナー サービス ログ | `ContainerServiceLog`  | TimeGenerated、Computer、TimeOfCommand、Image、Command、SourceSystem、ContainerID |
| コンテナー ノード インベントリ | `ContainerNodeInventory_CL`| TimeGenerated、Computer、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| コンテナー プロセス | `ContainerProcess_CL` | TimeGenerated、Computer、Pod_s、Namespace_s、ClassName_s、InstanceID_s、Uid_s、PID_s、PPID_s、C_s、STIME_s、Tty_s、TIME_s、Cmd_s、Id_s、Name_s、SourceSystem |
| Kubernetes クラスター内のポッドのインベントリ | `KubePodInventory` | TimeGenerated、Computer、ClusterId、ContainerCreationTimeStamp、PodUid、PodCreationTimeStamp、ContainerRestartCount、PodRestartCount、PodStartTime、ContainerStartTime、ServiceName、ControllerKind、ControllerName、ContainerStatus、ContainerID、ContainerName、Name、PodLabel、Namespace、PodStatus、ClusterName、PodIp、SourceSystem |
| Kubernetes クラスター内のノード部分のインベントリ | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes イベント | `KubeEvents_CL` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Kubernetes クラスター内のサービス | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Kubernetes クラスターのノード部分のパフォーマンス メトリック | Perf &#124; where ObjectName == “K8SNode” | Computer、ObjectName、CounterName &#40;cpuUsageNanoCores、memoryWorkingSetBytes、memoryRssBytes、networkRxBytes、networkTxBytes、restartTimeEpoch、networkRxBytesPerSec、networkTxBytesPerSec、cpuAllocatableNanoCores、memoryAllocatableBytes、cpuCapacityNanoCores、memoryCapacityBytes&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem | 
| Kubernetes クラスターのコンテナー部分のパフォーマンス メトリック | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40;cpuUsageNanoCores、memoryWorkingSetBytes、memoryRssBytes、restartTimeEpoch、cpuRequestNanoCores、memoryRequestBytes、cpuLimitNanoCores、memoryLimitBytes&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>データを分析するためのログの検索
Log Analytics を使用することにより、傾向を特定し、ボトルネックを診断、予想したり、データを関連付けて現在のクラスター構成のパフォーマンスが最適化されているかを判断したりできます。  すぐに使用できる事前定義のログ検索が提供されています。また、検索結果として返される情報の表示方法をカスタマイズすることもできます。 

コンテナーを展開すると右端に表示される **[ログの表示]** オプションを選択することにより、ワークスペース内のデータを対話に従って解析できます。  **[ログ検索]** ページがポータル内のページの右上に表示されます。<br><br> ![Log Analytics でデータを解析する](./media/monitoring-container-health/container-performance-and-health-view-logs-01.png)   

Log Analytics に転送されるコンテナーのログ出力は STDOUT および STDERR です。 コンテナーの正常性の監視では、Azure が管理している Kubernetes (AKS) が監視され、大量のデータが生成されるため、現時点では Kube システムのデータは収集されません。     

### <a name="example-log-search-queries"></a>検索クエリの例
多くの場合、1、2 個の例を使ってクエリを作成し、その後、環境に合わせて変更するとうまくいきます。 次のサンプル クエリから始め、徐々に、より高度なクエリを作成することをお勧めします。

| クエリ | 説明 | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | すべてのコンテナーのライフ サイクル情報を一覧表示します。| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Kubernetes イベント|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | イメージ インベントリ | 
| **高度な分析では、折れ線グラフを選択します**:<br> Perf<br> &#124; where ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; summarize AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | コンテナー CPU | 
| **高度な分析では、折れ線グラフを選択します**:<br> Perf &#124; where ObjectName == "Container" and CounterName == "Memory Usage MB"<br> &#124; summarize AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | コンテナー メモリ |

## <a name="how-to-stop-monitoring-with-container-health"></a>コンテナーの正常性の監視を停止する方法
AKS コンテナーの監視を有効化した後、監視が必要なくなった場合は、提供されている Azure Resource Manager テンプレートの PowerShell コマンドレット **New-AzureRmResourceGroupDeployment** または Azure CLI を使用して*オプト アウト*できます。  ある JSON テンプレートで設定を*オプト アウト*し、他の JSON テンプレートではパラメーター値を含めるように設定するには、AKS クラスター リソース ID と、クラスターに展開されているリソース グループを指定します。  PowerShell でテンプレートを使用してリソースを展開する手順の詳細については、[Resource Manager テンプレートおよび Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)または [Resource Manager テンプレートおよび Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy-cli.md)に関する記事を参照してください。

Azure CLI を使用する場合は、まず CLI をインストールしローカルで使用する必要があります。  Azure CLI バージョン 2.0.27 以降を実行している必要があります。 `az --version` を実行してバージョンを確認します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページを参照してください。 

### <a name="create-and-execute-template"></a>テンプレートを作成して実行する

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": {
                "logAnalyticsWorkspaceResourceID": null
              }
            }
           }
         }
       }
      ]
    }
    ```

2. このファイルを **OptOutTemplate.json** という名前でローカル フォルダーに保存します。
3. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. **aksResourceId** と、**aksResourceLocation** の値を、選択したクラスターの **[プロパティ]** ページに表示されている AKS クラスターの値に編集します。<br><br> ![コンテナーのプロパティ ページ](./media/monitoring-container-health/container-properties-page.png)<br>

    **[プロパティ]** ページの **[ワークスペース リソース ID]** もコピーします。  この値は、後で Log Analytics ワークスペースを削除する場合に必要になります。この手順ではワークスペースの削除は行いません。  

5. このファイルを **OptOutParam.json** という名前でローカル フォルダーに保存します。
6. これでこのテンプレートをデプロイする準備が整いました。 

    * テンプレートが保存されているフォルダーから、次の PowerShell コマンドを使用するには:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが返されます。

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Linux で Azure CLI を使用して次のコマンドを実行するには、次のコマンドを実行します。

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが返されます。

        ```azurecli
        ProvisioningState       : Succeeded
        ```

クラスターの監視をサポートするためだけにワークスペースが作成され、不要になった場合、手動で削除する必要があります。 ワークスペースを削除する方法の詳細については、「[Azure Portal で Azure Log Analytics ワークスペースを削除する](../log-analytics/log-analytics-manage-del-workspace.md)」を参照してください。  上記の手順 4 でコピーした、**ワークスペース リソース ID** は、この後の手順で必要になります。  

## <a name="troubleshooting"></a>トラブルシューティング
このセクションでは、コンテナーの正常性の監視のトラブルシューティングに役立つ情報を提供します。

コンテナーの正常性監視が正常に有効化され設定されたのに、ログ検索を行っても Log Analytics に状態情報や結果が表示されない場合は、次の手順を実行して問題を診断できます。   

1. 次のコマンドを実行してエージェントの状態を確認します: `kubectl get ds omsagent --namespace=kube-system`

    クラスター内のすべてのノードで、エージェントが実行されていることを示す、次のような出力が表示される必要があります。  たとえば、このクラスターには 2 つのノードが含まれており、同じ数のノードの値が表示されるはずです。  

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. 次のコマンドを実行して、ポッドの状態を調べ、実行中であるかどうかを確認します: `kubectl get pods --namespace=kube-system`

    omsagent の状態として、次の *[実行中]* のような状態が出力される必要があります。

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

3. エージェントのログを確認します。 コンテナー化されたエージェントが配置された時点で、エージェントは、OMI コマンドを実行してエージェントと Docker プロバイダーのバージョンを簡単に確認し、表示します。 エージェントが正常に使用されていることを確認するには、次のコマンドを実行します: `kubectl logs omsagent-484hw --namespace=kube-system`

    状態は次のように出力されます。

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>次の手順

コンテナーの正常性の監視の詳細とアプリケーションのパフォーマンス情報を確認するために[ログを検索](../log-analytics/log-analytics-log-search.md)します。  