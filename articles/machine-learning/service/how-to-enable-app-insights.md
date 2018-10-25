---
title: 運用環境で Azure Machine Learning サービスのために Application Insights を有効にする
description: Azure Kubernetes Service にデプロイされた Azure Machine Learning サービスのために Application Insights を設定する方法について説明します
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: 45871ab515c7ffd9520b1d77d3fd1e77abcc29ef
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114568"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>Application Insights を使用して運用環境の Azure Machine Learning モデルを監視する

この記事では、**Azure Machine Learning** サービスのために **Application Insights** を設定する方法について説明します。 Application Insights を有効にすると、以下を監視できます。
* 要求率、応答時間、失敗率
* 依存率、応答時間、失敗率
* 例外

Application Insights の詳細については、[こちら](../../application-insights/app-insights-overview.md)を参照してください。 

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
* Azure Machine Learning ワークスペース、スクリプトを保存するローカル ディレクトリ、Azure Machine Learning SDK for Python のインストール。 これらの前提条件を満たす方法については、[開発環境の構成方法](how-to-configure-environment.md)に関するドキュメントを参照してください。
* Azure Kubernetes Service (AKS) にデプロイするトレーニング済みの機械学習モデル。 ない場合は、[画像分類モデルのトレーニング](tutorial-train-models-with-aml.md)に関するチュートリアルを参照してください。
* [AKS クラスター](how-to-deploy-to-aks.md)。

## <a name="enable--disable-in-the-portal"></a>ポータルでの有効化と無効化

Azure portal では Application Insights を有効または無効にすることができます。

### <a name="enable"></a>有効化

1. [Azure portal](https://portal.azure.com) でワークスペースを開きます。

1. デプロイに移動し、Application Insights を有効にするサービスを選択します。

   [![デプロイ](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. **[編集]** をクリックし、**[詳細設定]** に移動します。

   [![編集](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. **[詳細設定]** で **[AppInsights 診断を有効にする]** をオンにします。

   [![編集](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. 画面下部の **[更新]** を選択して変更を適用します。 

### <a name="disable"></a>Disable
Azure portal で Application Insights を無効にするには、次の手順を実行します。

1. https://portal.azure.com で Azure portal にサインインします。
1. ワークスペースに移動します。
1. **[デプロイ]**、**[サービスの選択]**、**[編集]** の順に選択します。

   [![編集](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. **[詳細設定]** で、**[AppInsights 診断を有効にする]** オプションをオフにします。 

   [![オフ](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. 画面下部の **[更新]** を選択して変更を適用します。 

## <a name="enable--disable-from-the-sdk"></a>SDK からの有効化と無効化

### <a name="update-a-deployed-service"></a>デプロイされたサービスを更新する
1. ワークスペース内のサービスを特定する (ws = ワークスペースの名前)

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. サービスを更新し、Application Insights を有効にします。 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>サービスのカスタム トレースをログに記録する
カスタム トレースをログに記録する場合は、[AKS の標準的なデプロイ プロセス](how-to-deploy-to-aks.md)の手順に従い、以下を実行します。

1. print ステートメントを追加してスコアリング ファイルを更新します。
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. aks の構成を更新します。
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [イメージをビルドしてデプロイします。](how-to-deploy-to-aks.md)  

### <a name="disable-tracking-in-python"></a>Python で追跡を無効にする

Application Insights を無効にするには、次のコードを使用します。

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

## <a name="evaluate-data"></a>データを評価する
サービスのデータは、Azure Machine Learning サービス ワークスペースと同じリソース グループ内の Application Insights アカウントに保存されます。
表示するには:
1. [Azure portal](https://portal.azure.com) のリソース グループに移動し、Application Insights リソースをクリックします。 
2. **[概要]** タブには、サービスの基本的なメトリック セットが表示されます。

   [![概要](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. カスタム トレースを確認するには、**[分析]** をクリックします。
4. スキーマ セクション内の **traces** をクリックしてから、クエリを**実行**します。 データは以下の表形式で表示され、スコアリング ファイルのカスタムの呼び出しにマップされます。 

   [![カスタム トレース](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Application Insights の使用方法の詳細については、[こちら](../../application-insights/app-insights-overview.md)を参照してください。
    

## <a name="example-notebook"></a>ノートブックの例

[00.Getting Started/13.enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) ノートブックは、この記事の概念を示しています。  このノートブックの入手:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順
運用環境でモデルに関するデータを収集することもできます。 記事「[実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)」を参照してください。 
