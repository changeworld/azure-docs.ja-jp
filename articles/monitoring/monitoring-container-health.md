---
title: Azure Kubernetes Service (AKS) の正常性を監視する (プレビュー) | Microsoft Docs
description: この記事では、AKS コンテナーのパフォーマンスを簡単に調査して、ホストしている Kubernetes 環境の使用率をすばやく把握する方法について説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2018
ms.author: magoedte
ms.openlocfilehash: 2ae61d672083508d49e72afd5a015191082c23e9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521933"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Azure Kubernetes Service (AKS) コンテナーの正常性を監視する (プレビュー)

この記事では、Kubernetes 環境にデプロイされ、Azure Kubernetes Service (AKS) 上でホストされているワークロードのパフォーマンスを監視するために、Azure Monitor コンテナーの正常性を設定して使用する方法について説明します。 Kubernetes クラスターとコンテナーの監視は、複数のアプリケーションを含む大規模な運用クラスターを実行するときは特に重要です。

コンテナーの正常性では、Kubernetes で使用可能なコントローラー、ノード、およびコンテナーから Metrics API 経由でメモリやプロセッサ メトリックを収集することにより、パフォーマンス監視機能が提供されます。 コンテナーの正常性を有効にすると、コンテナー化されたバージョンの OMS (Operations Management Suite) エージェント for Linux を使用してこれらのメトリックが自動的に収集され、[Log Analytics](../log-analytics/log-analytics-overview.md) ワークスペースに保存されます。 存在するコンテナーのワークロードと、Kubernetes クラスターのパフォーマンスの正常性に影響を与えている原因が、含まれている定義済みのビューに表示されるため、以下を実行できます。  

* ノードで実行されているコンテナーと、そのプロセッサおよびメモリの平均使用率を特定します。 この知識は、リソースのボトルネックを特定するのに役立ちます。
* コントローラーまたはポッドのどこにコンテナーが存在するかを特定します。 この知識は、コントローラーまたはポッド全体のパフォーマンスを表示するのに役立ちます。 
* ポッドをサポートする標準プロセスと関連のない、ホスト上で実行されているワークロードのリソース使用率を確認します。
* 平均的な、および最大の負荷がかかったときのクラスターの動作を理解します。 この知識は、容量ニーズを特定し、クラスターが維持できる最大負荷を判断するのに役立ちます。 

構成、監査、リソースの使用率を確認するために Docker および Windows コンテナー ホストを監視および管理する必要がある場合は、[コンテナー監視ソリューション](../log-analytics/log-analytics-containers.md)を参照してください。

## <a name="prerequisites"></a>前提条件 
始める前に、必ず以下のものを用意してください。

- 新規または既存の AKS クラスター。
- microsoft/oms:ciprod04202018 バージョン以上の、コンテナー化された OMS エージェント for Linux。 バージョン番号は、*mmddyyyy* のような日付の形式で表されます。 エージェントは、コンテナーの正常性のオンボード時に自動的にインストールされます。 
- Log Analytics ワークスペース。 新しい AKS クラスターの監視を有効にするときにワークスペースを作成すること、またはオンボード エクスペリエンスを使用して AKS クラスター サブスクリプションの既定のリソース グループに既定のワークスペースを作成することができます。 自分でワークスペースを作成する場合は、[Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)、[PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json)、[Azure portal](../log-analytics/log-analytics-quick-create-workspace.md) のいずれかを使用して作成できます。
- コンテナーの監視を有効にするための Log Analytics 共同作成者ロール。 Log Analytics ワークスペースへのアクセスを制御する方法の詳細については、「[ワークスペースを管理する](../log-analytics/log-analytics-manage-access.md)」を参照してください。

## <a name="components"></a>コンポーネント 

パフォーマンスの監視機能では、クラスター内のすべてのノードからパフォーマンス データおよびイベント データを収集する、コンテナー化された OMS エージェント for Linux を利用します。 コンテナーの監視を有効にすると、エージェントは自動的に展開され、指定した Log Analytics ワークスペースに登録されます。 

>[!NOTE] 
>AKS クラスターが既にデプロイされている場合は、この記事の後半で説明されているように、Azure CLI または提供されている Azure Resource Manager テンプレートを使用して、監視を有効にします。 `kubectl` を使用してアップグレード、エージェントを削除、再展開、または展開することはできません。 
>

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。
[Azure Portal](https://portal.azure.com) にサインインします。 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>新しいクラスターのコンテナーの正常性の監視を有効にする
デプロイ時に、Azure portal または Azure CLI で新しい AKS クラスターの監視を有効にすることができます。 ポータルから有効にする場合は、クイック スタートの記事「[Azure Kubernetes Service (AKS) クラスターのデプロイ](../aks/kubernetes-walkthrough-portal.md)」の手順に従ってください。 **[監視]** ページの **[監視を有効にする]** オプションで **[はい]** を選択して、既存の Log Analytics ワークスペースを選ぶか、新しく作成します。 

Azure CLI で作成した新しい AKS クラスターの監視を有効にするには、クイック スタート記事の「[AKS クラスターの作成](../aks/kubernetes-walkthrough.md#create-aks-cluster)」セクションの手順に従ってください。  

>[!NOTE]
>Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.27 以降を実行する必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページを参照してください。 
>

監視を有効にし、すべての構成タスクが正常に完了すると、次の 2 つの方法のいずれかを使用して、クラスターのパフォーマンスを監視することができます。

* 左側のウィンドウで **[正常性]** を選択し、AKS クラスターで直接監視します。
* 選択したクラスターの AKS クラスター ページで、**[Monitor コンテナーの正常性]** タイルを選択します。 Azure Monitor の左側のウィンドウにある **[正常性]** を選択します。 

  ![AKS でコンテナーの正常性を選択するためのオプション](./media/monitoring-container-health/container-performance-and-health-select-01.png)

監視を有効にした後、クラスターのオペレーショナル データが表示されるまで、約 15 分かかる場合があります。 

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>既存のマネージド クラスターのコンテナーの正常性の監視を有効にする
既にデプロイされている AKS クラスターの監視を有効にするには、Azure CLI、ポータル、または提供されている Azure Resource Manager テンプレートで PowerShell コマンドレット `New-AzureRmResourceGroupDeployment` を使用します。 

### <a name="enable-monitoring-using-azure-cli"></a>Azure CLI を使用して監視を有効にする
Azure CLI を使用して AKS クラスターの監視を有効にするには、次の手順のようにします。 この例では、ワークスペースを事前に作成したり、既存のワークスペースを指定したりする必要はありません。 このコマンドでは、リージョンにワークスペースがまだ存在しない場合、AKS クラスター サブスクリプションの既定のリソース グループに既定のワークスペースが作成されるので、プロセスが簡単になります。  作成される既定のワークスペースは、*DefaultWorkspace-<GUID>-<Region>* のような形式になります。  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

出力は次のようになります。

```azurecli
provisioningState       : Succeeded
```

### <a name="enable-monitoring-in-the-azure-portal"></a>Azure Portal で監視を有効にする
Azure Portal で AKS コンテナーの監視を有効にするには、次のようにします。

1. Azure Portal で **[すべてのサービス]** を選択します。 
2. リソースの一覧で、「**Containers**」と入力を開始します。  
    入力内容に基づいて、一覧がフィルター処理されます。 
3. **[Kubernetes サービス]** を選択します。  

    ![Kubernetes サービスのリンク](./media/monitoring-container-health/azure-portal-01.png)

4. コンテナーの一覧で、コンテナーを選択します。
5. コンテナーの概要ページで、**[Monitor コンテナーの正常性]** を選択します。  
6. **[コンテナー正常性およびログのオンボード]** ページにクラスターと同じサブスクリプションの既存の Log Analytics ワークスペースが存在する場合は、ドロップダウン リストでそれを選択します。  
    このリストでは、サブスクリプションで AKS コンテナーがデプロイされている既定のワークスペースと場所が事前に選択されています。 

    ![AKS コンテナーの正常性の監視を有効にする](./media/monitoring-container-health/container-health-enable-brownfield-02.png)

>[!NOTE]
>クラスターからの監視データを格納するための新しい Log Analytics ワークスペースを作成する場合は、「[Log Analytics ワークスペースの作成](../log-analytics/log-analytics-quick-create-workspace.md)」の手順に従います。 必ず、AKS コンテナーがデプロイされるのと同じサブスクリプションでワークスペースを作成してください。 
 
監視を有効にした後、クラスターのオペレーショナル データが表示されるまで、約 15 分かかる場合があります。 

### <a name="enable-monitoring-by-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して監視を有効にする
この方法には、2 つの JSON テンプレートが含まれます。 1 つのテンプレートでは監視を有効にする構成が指定され、もう 1 つのテンプレートには、次を指定するために構成するパラメーター値が含まれています。

* AKS コンテナー リソース ID。 
* クラスターがデプロイされるリソース グループ。
* ワークスペースを作成する Log Analytics ワークスペースとリージョン。 

Log Analytics ワークスペースは、手動で作成する必要があります。 ワークスペースを作成するには、[Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)、[PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json)、[Azure portal](../log-analytics/log-analytics-quick-create-workspace.md) のいずれかを使用して設定できます。

テンプレートを使用するリソースのデプロイの概念がよくわからない場合は、以下を参照してください。
* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.27 以降を実行する必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページを参照してください。 

#### <a name="create-and-execute-a-template"></a>テンプレートを作成して実行する

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
3. 以下の JSON 構文をファイルに貼り付けます。

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

4. **aksResourceId** および **aksResourceLocation** の値を、AKS クラスターの **[AKS Overview]\(AKS 概要\)** ページの値を使用して編集します。 **workspaceResourceId** の値は、ワークスペース名を含む Log Analytics ワークスペースの完全なリソース ID です。 また、**workspaceRegion** にワークスペースの場所を指定します。 
5. このファイルを **existingClusterParam.json** としてローカル フォルダーに保存します。
6. これでこのテンプレートをデプロイする準備が整いました。 

    * テンプレートが含まれているフォルダーで、次の PowerShell コマンドを使用します。

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが表示されます。

        ```powershell
        provisioningState       : Succeeded
        ```

    * Linux 上で Azure CLI を使用して、次のコマンドを実行する場合:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが表示されます。

        ```azurecli
        provisioningState       : Succeeded
        ```
監視を有効にした後、クラスターのオペレーショナル データが表示されるまで、約 15 分かかる場合があります。 

## <a name="verify-agent-and-solution-deployment"></a>エージェントとソリューションのデプロイを確認する
バージョン *06072018* 以降のエージェントでは、エージェントとソリューションの両方が正常にデプロイされていることを確認することができます。 これより前のバージョンのエージェントでは、エージェントのデプロイしか確認できません。

### <a name="agent-version-06072018-or-later"></a>エージェント バージョン 06072018 以降
エージェントが正常にデプロイされていることを確認するには、次のコマンドを実行します。 

```
kubectl get ds omsagent --namespace=kube-system
```

出力は次のようになり、適切にデプロイされたことが示されます。

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

ソリューションのデプロイを確認するには、次のコマンドを実行します。

```
kubectl get deployment omsagent-rs -n=kube-system
```

出力は次のようになり、適切にデプロイされたことが示されます。

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>06072018 より前のバージョンのエージェント

*06072018* より前にリリースされたバージョンの OMS エージェントが適切にデプロイされていることを確認するには、次のコマンドを実行します。  

```
kubectl get ds omsagent --namespace=kube-system
```

出力は次のようになり、適切にデプロイされたことが示されます。  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>CLI で構成を表示する
ソリューションが有効かどうか、Log Analytics ワークスペースのリソース ID、クラスターに関するサマリー詳細などの詳細を取得するには、`aks show` コマンドを使用します。  

```azurecli
az aks show -g <resoourceGroupofAKSCluster> -n <nameofAksCluster>
```

数分してコマンドが完了すると、ソリューションに関する情報が JSON 形式で表示されます。  コマンドの結果では監視アドオン プロファイルが表示され、次の出力例のようになります。

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="view-performance-utilization"></a>パフォーマンス使用率の表示
コンテナーの正常性ページを開くと、すぐにクラスター全体のパフォーマンスの使用率が表示されます。 AKS クラスターに関する情報は 4 つの観点から確認できます。

- クラスター
- Nodes 
- コントローラー  
- Containers

**[クラスター]** タブの 4 つのパフォーマンス折れ線グラフに、クラスターの主要なパフォーマンス メトリックが表示されます。 

![[クラスター] タブのパフォーマンス グラフの例](./media/monitoring-container-health/container-health-cluster-perfview.png)

パフォーマンス グラフには、次の 4 つのパフォーマンス メトリックが表示されます。

- **ノードの CPU 使用率&nbsp;%**: クラスター全体の CPU 使用率の集計されたパースペクティブ。 時間範囲ごとに結果をフィルター処理するには、グラフの上のパーセンタイル セレクターで "**Avg**"、"**Min**"、"**Max**"、"**50th**"、"**90th**"、"**95th**" を個別にまたは組み合わせて選択します。 
- **ノードのメモリ使用率&nbsp;%**: クラスター全体のメモリ使用率の集計されたパースペクティブ。 時間範囲ごとに結果をフィルター処理するには、グラフの上のパーセンタイル セレクターで "**Avg**"、"**Min**"、"**Max**"、"**50th**"、"**90th**"、"**95th**" を個別にまたは組み合わせて選択します。 
- **ノード数**: Kubernetes からのノードの数と状態。 表されるクラスター ノードの状態には、*[すべて]*、*[準備完了]*、*[準備未完了]* があり、グラフの上のセレクターで個別にまたは組み合わせてフィルター処理できます。 
- **アクティビティ ポッド数**: Kubernetes からのポッドの数と状態。 表されるポッドの状態には、*[すべて]*、*[保留中]*、*[実行中]*、*[不明]* があり、グラフの上のセレクターで個別にまたは組み合わせてフィルター処理できます。 

**[ノード]**、**[コントローラー]**、および **[コンテナー]** タブに切り替えると、ページの右側に自動的にプロパティ ウィンドウが表示されます。  ここには選択された項目のプロパティが示され、Kubernetes オブジェクトを整理するために定義するラベルが含まれます。  ウィンドウの表示と非表示を切り替えるには、ウィンドウの **>>** リンクをクリックします。  

![Kubernetes パースペクティブ プロパティ ウィンドウの例](./media/monitoring-container-health/perspectives-preview-pane-01.png)

階層内のオブジェクトを展開すると、選択されたオブジェクトに基づいて、プロパティ ウィンドウが更新されます。 ウィンドウから、定義済みのログ検索を使用して Kubernetes イベントを表示することもできます。その場合は、ウィンドウの上部にある **[View Kubernetes event logs]\(Kubernetes イベント ログの表示\)** リンクをクリックします。 Kubernetes ログ データの表示の詳細については、「[データを分析するためのログの検索](#search-logs-to-analyze-data)」を参照してください。

**[ノード]** タブに切り替えます。行階層では、クラスター内のノードから始まり、Kubernetes オブジェクト モデルに従います。 ノードを展開し、ノードで実行されている 1 つまたは複数のポッドを表示することができます。 複数のコンテナーがポッドにグループ化されている場合、階層内の最後の行として表示されます。 ホストでプロセッサまたはメモリが不足している場合、ホスト上でどのくらいの数のポッドに関連しないワークロードが実行されているかを確認することもできます。

![パフォーマンス ビューの Kubernetes ノード階層の例](./media/monitoring-container-health/container-health-nodes-view.png)

ページ上部のコントローラーまたはコンテナーを選択し、それらのオブジェクトの状態やリソース使用率を確認できます。 上部のドロップダウン ボックスを使用すると、名前空間、サービス、ノードごとにフィルター処理できます。 また、**[メトリック]** ドロップダウン リストで **[Memory RSS]\(使用メモリ (RSS)\)** または **[メモリ ワーキング セット]** を選択すると、メモリ使用率を確認することもできます。 **[Memory RSS]\(使用メモリ (RSS)\)** は、Kubernetes 1.8 以降でのみサポートされています。 それ以外のバージョンでは、**Min&nbsp;%** の値が、未定義または表示できない値を示す数値データ型である、*NaN&nbsp;%* として示されます。 

![コンテナー ノード パフォーマンス ビュー](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

既定では、パフォーマンス データは、過去 6 時間のものですが、右上にある **[時間範囲]** ドロップダウン リストを使用して時間枠を変更できます。 現時点では、ページは自動更新されないため、手動で更新する必要があります。 パーセンタイル セレクターで "**Avg**"、"**Min**"、"**Max**"、"**50th**"、"**90th**"、"**95th**" を選択して、時間範囲内の結果をフィルター処理することもできます。 

![パーセンタイル選択によるデータのフィルター処理](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

次の例では、*aks-nodepool-3977305* ノードの **[Containers]** の値は 5 で、これはデプロイされているコンテナーの合計数のロールアップです。

![ノードあたりのコンテナーのロールアップの例](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

これにより、クラスター内のノード間のコンテナーのバランスが適切かどうかをすばやく識別できます。 

次の表では、ノードを表示した場合に示される情報について説明します。

| 分割 | 説明 | 
|--------|-------------|
| Name | ホストの名前。 |
| Status | ノードの状態の Kubernetes ビュー。 |
| Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;% | 選択した期間中の、パーセンタイルに基づいたノードの平均率。 |
| Avg、Min、Max、50th、90th | 選択した期間中の、パーセンタイルに基づいたノードの実際の平均値。 平均値は、ノードに設定されている CPU/メモリの制限から測定されます。ポッドおよびコンテナーについては、ホストによって報告された平均値です。 |
| Containers | コンテナーの数。 |
| Uptime | ノードが起動または再起動されてから経過した時間を示します。 |
| コントローラー | コンテナーとポッド限定。 どのコントローラーに存在しているかが示されます。 コントローラーにないポッドもあるため、一部は **N/A** と表示される可能性があります。 | 
| Trend Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;% | コントローラーのパーセンタイル メトリック率を表す棒グラフ トレンド。 |


セレクターで、**[コントローラー]** を選択します。

![コントローラーの選択ビュー](./media/monitoring-container-health/container-health-controllers-tab.png)

ここで、コントローラーのパフォーマンスの正常性を表示できます。

![<名前> コントローラーのパフォーマンス ビュー](./media/monitoring-container-health/container-health-controllers-view.png)

行階層はコントローラーで始まり、コントローラーを展開します。 1 つまたは複数のコンテナーを表示できます。 ポッドを展開すると、ポッドにグループ化されているコンテナーが最後の行に表示されます。 

次の表では、コントローラーを表示した場合に示される情報について説明します。

| 分割 | 説明 | 
|--------|-------------|
| Name | コントローラーの名前。|
| Status | *[OK]*、*[終了]*、*[失敗]*、*[停止]*、*[一時停止]* などの、実行完了時のコンテナーのロールアップ状態です。 コンテナーが実行されているのに状態が正しく表示されない、またはエージェントが状態を認識せず、コンテナーが 30 分を超えても応答しない場合は、*[不明]* 状態になります。 状態アイコンのその他の詳細については、以下の表を参照してください。|
| Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;% | 各エンティティの選択されたメトリックとパーセンタイルの平均率のロールアップ平均。 |
| Avg、Min、Max、50th、90th  | 選択されたパーセンタイルのコンテナーの平均 CPU ミリコアまたはメモリ パフォーマンスのロールアップ。 平均値は、ポッドに設定されている CPU/メモリ制限から測定されます。 |
| Containers | コントローラーまたはポッドのコンテナーの合計数。 |
| Restarts | コンテナーの再起動数のロールアップ。 |
| Uptime | コンテナーが起動されてからの経過時間を表します。 |
| ノード | コンテナーとポッド限定。 これには存在しているコントローラーが表示されます。 | 
| Trend Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;%| コントローラーのパーセンタイル メトリックを表す棒グラフ トレンドです。 |

状態フィールドのアイコンは、コンテナーのオンライン状態を示します。
 
| アイコン | Status | 
|--------|-------------|
| ![実行中 (準備完了) 状態アイコン](./media/monitoring-container-health/container-health-ready-icon.png) | 実行中 (準備完了)|
| ![待機中または一時停止状態アイコン](./media/monitoring-container-health/container-health-waiting-icon.png) | 待機中または一時停止|
| ![最後に報告された実行中の状態アイコン](./media/monitoring-container-health/container-health-grey-icon.png) | 最後に報告された実行中。ただし、30 分以上に応答していない|
| ![正常な状態アイコン](./media/monitoring-container-health/container-health-green-icon.png) | 正常に停止したか、停止に失敗した|

状態アイコンには、ポッドでの指定内容に基づいた数が示されます。 最も良くない 2 つの状態を示し、状態の上にマウス ポインターを移動すると、コンテナー内のすべてのポッドのロールアップ状態を示します。 準備完了の状態がない場合、状態の値は **(0)** と表示されます。 

セレクターで、**[コンテナー]** を選択します。

![コンテナーの選択ビュー](./media/monitoring-container-health/container-health-containers-tab.png)

ここで、コンテナーのパフォーマンスの正常性を表示できます。

![<名前> コントローラーのパフォーマンス ビュー](./media/monitoring-container-health/container-health-containers-view.png)

次の表では、コンテナーを表示した場合に示される情報について説明します。

| 分割 | 説明 | 
|--------|-------------|
| Name | コントローラーの名前。|
| Status | 存在する場合、コンテナーの状態です。 状態アイコンのその他の詳細については、次の表を参照してください。|
| Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;% | 各エンティティの選択されたメトリックとパーセンタイルの平均率のロールアップ。 |
| Avg、Min、Max、50th、90th  | 選択されたパーセンタイルのコンテナーの平均 CPU ミリコアまたはメモリ パフォーマンスのロールアップ。 平均値は、ポッドに設定されている CPU/メモリ制限から測定されます。 |
| Pod | ポッドが存在するコンテナーです。| 
| ノード |  コンテナーが存在するノードです。 | 
| Restarts | コンテナーが起動されてからの経過時間を表します。 |
| Uptime | コンテナーが起動または再起動されてからの経過時間を表します。 |
| Trend Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;% | コンテナーのメトリックの平均率を表す棒グラフ トレンド。 |

状態フィールドのアイコンは、次の表の説明とのとおり、ポッドのオンライン状態を示します。
 
| アイコン | Status | 
|--------|-------------|
| ![実行中 (準備完了) 状態アイコン](./media/monitoring-container-health/container-health-ready-icon.png) | 実行中 (準備完了)|
| ![待機中または一時停止状態アイコン](./media/monitoring-container-health/container-health-waiting-icon.png) | 待機中または一時停止|
| ![最後に報告された実行中の状態アイコン](./media/monitoring-container-health/container-health-grey-icon.png) | 最後に報告された実行中。ただし、30 分を超えても応答しない|
| ![終了状態アイコン](./media/monitoring-container-health/container-health-terminated-icon.png) | 正常に停止したか、停止に失敗した|
| ![失敗状態アイコン](./media/monitoring-container-health/container-health-failed-icon.png) | 失敗の状態 |

## <a name="container-data-collection-details"></a>コンテナーのデータ収集の詳細
コンテナーの正常性の監視により、コンテナー ホストとコンテナーからさまざまなパフォーマンス メトリックとログ データが収集されます。 データは、3 分ごとに収集されます。

### <a name="container-records"></a>コンテナー レコード

次の表には、コンテナーの正常性によって収集されるレコードと、ログ検索結果に表示されるデータ型の例を示します。

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
Log Analytics を使用することにより、傾向を特定し、ボトルネックを診断、予想したり、データを関連付けて現在のクラスター構成のパフォーマンスが最適化されているかを判断したりできます。 すぐに使用できる事前定義のログ検索が提供されています。また、検索結果として返される情報の表示方法をカスタマイズすることもできます。 

プレビュー ウィンドウで **[View Kubernetes event logs]\(Kubernetes イベント ログの表示\)** または **[コンテナー ログの表示]** オプションを選択することにより、ワークスペース内のデータを対話式に分析できます。 **[ログ検索]** ページは、元の Azure portal ページの右側に表示されます。

![Log Analytics でデータを解析する](./media/monitoring-container-health/container-health-log-search-example.png)   

Log Analytics に転送されるコンテナーのログ出力は STDOUT および STDERR です。 コンテナーの正常性では、Azure マネージド Kubernetes (AKS) が監視され、大量のデータが生成されるため、現時点では Kube システムのデータは収集されません。 

### <a name="example-log-search-queries"></a>検索クエリの例
多くの場合、1、2 個の例を使ってクエリを作成し、その後、要件に合わせて変更するとうまくいきます。 より高度なクエリを作成できるように、次のサンプル クエリを試すことができます。

| Query | 説明 | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | コンテナーのライフ サイクル情報をすべて一覧表示します| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Kubernetes イベント|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | イメージ インベントリ | 
| **高度な分析では、折れ線グラフを選択します**:<br> Perf<br> &#124; where ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; summarize AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | コンテナー CPU | 
| **高度な分析では、折れ線グラフを選択します**:<br> Perf &#124; where ObjectName == "Container" and CounterName == "Memory Usage MB"<br> &#124; summarize AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | コンテナー メモリ |

## <a name="how-to-stop-monitoring-with-container-health"></a>コンテナーの正常性の監視を停止する方法
AKS コンテナーの監視を有効にした後、監視が必要なくなった場合は、提供されている Azure Resource Manager テンプレートと PowerShell コマンドレット **New-AzureRmResourceGroupDeployment** または Azure CLI を使用して*オプト アウト* できます。 1 つの JSON テンプレートでは、*オプト アウト*する構成が指定されます。もう 1 つのテンプレートには、AKS クラスター リソース ID およびリソース グループ (クラスターがデプロイされる) を指定するために構成するパラメーター値が含まれています。 

テンプレートを使用するリソースのデプロイの概念がよくわからない場合は、以下を参照してください。
* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.27 以降を実行する必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページを参照してください。 

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
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. このファイルを **OptOutTemplate.json** という名前でローカル フォルダーに保存します。
3. 以下の JSON 構文をファイルに貼り付けます。

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

4. **aksResourceId** と **aksResourceLocation** の値を、選択したクラスターの **[プロパティ]** ページに表示されている AKS クラスターの値を使用して編集します。

    ![コンテナーのプロパティ ページ](./media/monitoring-container-health/container-properties-page.png)

    **[プロパティ]** ページの **[ワークスペース リソース ID]** もコピーします。 この値は、後で Log Analytics ワークスペースを削除する場合に必要になります。 このプロセスでは、Log Analytics ワークスペースの削除は行いません。 

5. このファイルを **OptOutParam.json** という名前でローカル フォルダーに保存します。
6. これでこのテンプレートをデプロイする準備が整いました。 

    * テンプレートが含まれているフォルダーで、次の PowerShell コマンドを使用する場合:

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

クラスターの監視をサポートするためだけにワークスペースが作成され、不要になった場合、手動で削除する必要があります。 ワークスペースを削除する方法の詳細については、「[Azure Portal で Azure Log Analytics ワークスペースを削除する](../log-analytics/log-analytics-manage-del-workspace.md)」を参照してください。 上記の手順 4 でコピーした、**ワークスペース リソース ID** は、この後の手順で必要になります。 

## <a name="troubleshooting"></a>トラブルシューティング
このセクションでは、コンテナーの正常性の監視のトラブルシューティングに役立つ情報を提供します。

コンテナーの正常性が正しく有効化され、構成されたのに、ログ検索を行っても Log Analytics で状態情報や結果を表示できない場合は、次の手順を行って問題を診断できます。 

1. 次のコマンドを実行してエージェントの状態を確認します。 

    `kubectl get ds omsagent --namespace=kube-system`

    出力は次のようになり、適切にデプロイされたことが示されます。

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. エージェント バージョン *06072018* 以降のソリューションのデプロイ状態を確認するには、次のコマンドを実行します。

    `kubectl get deployment omsagent-rs -n=kube-system`

    出力は次のようになり、適切にデプロイされたことが示されます。

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. 次のコマンドを実行して、ポッドの状態を調べ、実行中であることを確認します。`kubectl get pods --namespace=kube-system`

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

4. エージェントのログを確認します。 コンテナー化されたエージェントは、デプロイされた時点で、OMI コマンドを実行して簡単なチェックを行い、エージェントとプロバイダーのバージョンを表示します。 

5. エージェントが正常にオンボードされていることを確認するには、次のコマンドを実行します。`kubectl logs omsagent-484hw --namespace=kube-system`

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

詳細なコンテナーの正常性およびアプリケーションのパフォーマンス情報を確認する場合は、[ログの検索](../log-analytics/log-analytics-log-search.md)に関するページを参照してください。 
