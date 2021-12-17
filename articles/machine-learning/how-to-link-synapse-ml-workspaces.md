---
title: Synapse と Azure Machine Learning のワークスペースを使用してリンク サービスを作成する (プレビュー)
titleSuffix: Azure Machine Learning
description: 統一されたデータ ラングリング エクスペリエンスを実現するために、Azure Synapse と Azure Machine Learning のワークスペースをリンクして Apache Spark プールをアタッチする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.custom: devx-track-python, data4ml, synapse-azureml, contperf-fy21q4
ms.openlocfilehash: e4efd0564be556704f0a4ac2c6ae4b378b19fb2b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564786"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-and-attach-apache-spark-poolspreview"></a>Azure Synapse Analytics と Azure Machine Learning ワークスペースをリンクして Apache Spark プールをアタッチする (プレビュー)

この記事では、[Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) ワークスペースと [Azure Machine Learning ワークスペース](concept-workspace.md)をリンクするリンク サービスを作成する方法について説明します。 

Azure Machine Learning ワークスペースを Azure Synapse ワークスペースとリンクすることで、Azure Synapse Analytics で動作する Apache Spark プールを大規模なデータ ラングリング専用コンピューティングとしてアタッチしたり、モデルのトレーニングを実施したりする作業を、すべて同じ Python ノートブックから行うことができます。

ML ワークスペースと Synapse ワークスペースのリンクは、[Python SDK](#link-sdk) または [Azure Machine Learning スタジオ](#link-studio)から行うことができます。

また、複数のワークスペースをリンクし、1 つの [Azure Resource Manager (ARM) テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-linkedservice-create/azuredeploy.json)を使用して Synapse Spark プールをアタッチすることもできます。

>[!IMPORTANT]
> Azure Machine Learning と Azure Synapse の統合はパブリック プレビュー段階です。 `azureml-synapse` パッケージから提供される機能は[試験段階](/python/api/overview/azure/ml/#stable-vs-experimental)のプレビュー機能であり、随時変更される可能性があります。

## <a name="prerequisites"></a>前提条件

* [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md?tabs=python)します。

* [Azure portal で Synapse ワークスペースを作成](../synapse-analytics/quickstart-create-workspace.md)します。

* [Azure portal、Web ツール、Synapse Studio のいずれかを使用して Apache Spark プールを作成](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)します

* [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) をインストールします。

* [Azure Machine Learning スタジオ](https://ml.azure.com/)にアクセスします。

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Python SDK にワークスペースをリンクさせる

> [!IMPORTANT]
> Synapse ワークスペースを正常にリンクさせるには、Synapse ワークスペースの **所有者** ロールが付与されている必要があります。 [Azure portal](https://ms.portal.azure.com/) でご自身のアクセス権を確認してください。
>
> Synapse ワークスペースの **所有者** ではなく、単に **共同作成者** である場合は、既存のリンク サービスのみを使用できます。 「[既にあるリンクされたサービスを取得して使用する](#get-an-existing-linked-service)」方法を参照してください。

以下のコードでは、次の目的に [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) クラスと [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) クラスを使用しています。

* Machine Learning ワークスペース (`ws`) を Azure Synapse ワークスペースにリンクさせる。
* リンクされたサービスとして Azure Machine Learning に Synapse ワークスペースを登録する。

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> リンクされたサービスごとにマネージド ID (`system_assigned_identity_principal_id`) が作成されます。 このマネージド ID には、Synapse セッションを開始する前に、Synapse ワークスペースの **Synapse Apache Spark 管理者** ロールが付与されている必要があります。 [Synapse Studio のマネージド ID に Synapse Apache Spark 管理者ロールを割り当てます](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)。
>
> 特定のリンクされたサービスの `system_assigned_identity_principal_id` を見つけるには、`LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` を使用します。

### <a name="manage-linked-services"></a>リンクされたサービスの管理

Machine Learning ワークスペースに関連付けられているリンクされたサービスをすべて表示します。

```python
LinkedService.list(ws)
```

ワークスペースのリンクを解除するには、`unregister()` メソッドを使用します

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>スタジオを使用してワークスペースをリンクさせる

Azure Machine Learning スタジオを使用して Machine Learning ワークスペースと Synapse ワークスペースをリンクさせるには、次の手順に従います。 

1. [Azure Machine Learning スタジオ](https://ml.azure.com/)にサインインします。
1. 左ペインの **[管理]** セクションにある **[リンクされたサービス]** を選択します。
1. **[Add integration]\(統合の追加\)** を選択します。
1. **[Link workspace]\(ワークスペースのリンク\)** フォームの各フィールドの内容を入力します。

    |フィールド| 説明    
    |---|---
    |名前| リンクされたサービスの名前を入力します。 この特定のリンクされたサービスを参照する際に、この名前を使用します。
    |サブスクリプション名 | Machine Learning ワークスペースに関連付けられているサブスクリプションの名前を選択します。 
    |Synapse ワークスペース | リンク先の Synapse ワークスペースを選択します。
    
1. **[次へ]** を選択して **[Select Spark pools (optional)]\(Spark プールの選択 (省略可)\)** フォームを開きます。 このフォームで、ワークスペースにアタッチする Synapse Spark プールを選択します。

1. **[次へ]** を選択して **[Review]\(レビュー\)** フォームを開き、選択内容を確認します。
1. **[作成]** を選択して、リンクされたサービスの作成プロセスを完了します。

## <a name="get-an-existing-linked-service"></a>既にあるリンクされたサービスを取得する

データ ラングリング専用のコンピューティングをアタッチする前に、Azure Synapse Analytics ワークスペースにリンクされている ML ワークスペースが必要です。これは、リンクされたサービスと呼ばれます。 

既にあるリンクされたサービスを取得して使用するためには、**Azure Synapse Analytics ワークスペース** に対する **ユーザーまたは共同作成者** のアクセス許可が必要です。

この例では、[`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) メソッドを使用して、既にあるリンクされたサービス (`synapselink1`) をワークスペース (`ws`) から取得します。
```python
from azureml.core import LinkedService

linked_service = LinkedService.get(ws, 'synapselink1')
```

## <a name="attach-synapse-spark-pool-as-a-compute"></a>Synapse Spark プールをコンピューティングとしてアタッチする

リンクされたサービスを取得したら、データ ラングリング タスク専用のコンピューティング リソースとして Synapse Apache Spark プールをアタッチします。 

Apache Spark プールは、次の手段でアタッチできます。
* Azure Machine Learning Studio
* [Azure Resource Manager (ARM) テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-linkedservice-create/azuredeploy.json)
* Azure Machine Learning Python SDK 

### <a name="attach-a-pool-via-the-studio"></a>スタジオを使用してプールをアタッチする
次の手順に従います。 

1. [Azure Machine Learning スタジオ](https://ml.azure.com/)にサインインします。
1. 左ペインの **[管理]** セクションにある **[リンクされたサービス]** を選択します。
1. Synapse ワークスペースを選択します。
1. 左上の **[Attached Spark pools]\(アタッチされた Spark プール\)** を選択します。 
1. **[接続]** を選択します。 
1. Apache Spark プールを一覧から選択し、名前を入力します。  
    1. コンピューティングにアタッチできる使用可能な Synapse Spark プールが、この一覧によって認識されます。 
    1. 新しい Synapse Spark プールを作成するには、[Synapse Studio を使用して Apache Spark プールを作成する](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)方法に関するページを参照してください。
1. **[Attach selected]\(選択されたアタッチ\)** を選択します。 

### <a name="attach-a-pool-with-the-python-sdk"></a>Python SDK を使用してプールをアタッチする

**Python SDK** を使用して Apache Spark プールをアタッチすることもできます。 

以下のコードは、次の処理を実行します。 
1. 以下を使用して [`SynapseCompute`](/python/api/azureml-core/azureml.core.compute.synapsecompute) を構成します。

   1. 前の手順で作成または取得した [`LinkedService`](/python/api/azureml-core/azureml.core.linkedservice)、`linked_service`。 
   1. アタッチするコンピューティング先の種類 (`SynapseSpark`)
   1. Apache Spark プールの名前。 これは、Azure Synapse Analytics ワークスペースにある既存の Apache Spark プールと一致している必要があります。
   
1. 次の情報を渡して、機械学習の [`ComputeTarget`](/python/api/azureml-core/azureml.core.computetarget) を作成します。 
   1. 使用する Machine Learning ワークスペース (`ws`)
   1. Azure Machine Learning ワークスペース内でコンピューティングを参照する場合の名前。 
   1. Synapse コンピューティングを構成するときに指定した attach_configuration。
       1. ComputeTarget.attach() の呼び出しは非同期であるため、このサンプルは、呼び出しが完了するまでブロック状態になります。

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name=synapse_spark_pool_name) #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name= synapse_compute_name, 
                                       attach_configuration= attach_config
                                      )

synapse_compute.wait_for_completion()
```

Apache Spark プールがアタッチされていることを確認します。

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="next-steps"></a>次のステップ

* [Azure Synapse を使用してデータをラングリングする方法 (プレビュー)](how-to-data-prep-synapse-spark-pool.md)。
* [機械学習パイプライン内の Apache Spark を Azure Synapse と一緒に使用する方法 (プレビュー)](how-to-use-synapsesparkstep.md)
* [モデルをトレーニングします](how-to-set-up-training-targets.md)。