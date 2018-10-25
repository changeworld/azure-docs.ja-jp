---
title: 実稼働環境でモデルのデータ収集を有効にする - Azure Machine Learning
description: Azure Blob ストレージで Azure Machine Learning の入力モデル データを収集する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 09/24/2018
ms.openlocfilehash: 70c023fc8fe996060d3eff3d5a700b5f910097b4
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2018
ms.locfileid: "49113633"
---
# <a name="collect-data-for-models-in-production"></a>実稼働環境でモデルのデータを収集する

この記事では、Azure Kubernetes Cluster (AKS) にデプロイした Azure Machine Learning サービスの入力モデル データを収集し、Azure Blob ストレージに格納する方法について説明します。 

この方法を習得すると、収集したデータで次のことができるようになります。
* 実稼働データがモデルに追加されたときのデータの変動を監視する

* モデルの再トレーニングや最適化の時期をより適切に判断する

* 収集したデータを使用してモデルを再トレーニングする

## <a name="what-is-collected-and-where-does-it-go"></a>収集される情報とその行き先

次のデータを収集できます。
* Azure Kubernetes Cluster (AKS) にデプロイされた Web サービスのモデル**入力**データ (音声、画像、およびビデオは**収集されません**) 
  
* 実稼働環境入力データを使用したモデル予測

> [!Note]
> このデータの事前集計または事前計算は、現時点ではサービスに含まれていません。   

出力は Azure Blob に保存されます。 データは Azure Blob に追加されるので、分析の実行には任意のツールを選択できます。 

出力データは、BLOB 内で次のような構文のパスに保存されます。

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

- Azure Machine Learning service ワークスペース、スクリプトを保存するローカル ディレクトリ、Azure Machine Learning SDK for Python のインストール。 これらの前提条件を満たす方法については、[開発環境を構成する方法](how-to-configure-environment.md)に関するドキュメントを参照してください。

- Azure Kubernetes Service (AKS) にデプロイするトレーニング済みの機械学習モデル。 ない場合は、[画像分類モデルのトレーニング](tutorial-train-models-with-aml.md)に関するチュートリアルを参照してください。

- [AKS クラスター](how-to-deploy-to-aks.md)。

- [使用している環境](how-to-configure-environment.md)に次の依存関係とモジュールがインストールされていること。
  + Linux の場合:
    ```shell
    sudo apt-get install libxml++2.6-2v5
    pip install azureml-monitoring
    ```

  + Windows の場合:
    ```shell
    pip install azureml-monitoring
    ```

## <a name="enable-data-collection"></a>データ収集を有効にする
モデルのデプロイに使用されているツールが Azure Machine Learning Service かどうかにかかわらず、データ収集を有効にすることができます。 

有効にするには、次の手順を実行します。

1. スコア付けファイルを開きます。 

1. ファイルの先頭に次のコードを追加します。

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. `init()` 関数でデータ収集変数を宣言します。

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* は省略可能なパラメーターです。モデルに不要な場合、設定する必要はありません。 correlationId を指定すると、他のデータとのマッピングが簡単になります (例: LoanNumber、CustomerId など)。
    
    *Identifier* は BLOB のフォルダー構造を構築するために後で使用します。"生" データと "処理済み" データの区別に使用できます。

3.  `run(input_df)` 関数に次のコード行を追加します。

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. AKS にサービスをデプロイしてもデータ収集は自動的に **true** に**設定されない**ので、次のように構成ファイルを更新する必要があります。 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    また、この構成を変更して、サービス監視の AppInsights を有効にすることもできます。
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. [新しいイメージを作成してサービスをデプロイします。](how-to-deploy-to-aks.md) 


依存関係がインストールされたサービスが**環境ファイル**と**スコア付けファイル**に既に存在する場合は、次の手順でデータ収集を有効にします。

1. [Azure portal](https://portal.azure.com) に移動します。

1. ワークスペースを開きます。

1. **[デプロイ]** -> **[サービスの選択]** -> **[編集]** の順に選択します。

   ![サービスの編集](media/how-to-enable-data-collection/EditService.png)

1. **[詳細設定]** で、**[モデルのデータ コレクションを有効にする]** をオフにします。 

   ![データ コレクションをオフにする](media/how-to-enable-data-collection/CheckDataCollection.png)

   このウィンドウでは、[AppInsights 診断を有効にする] をオンにしてサービスの正常性を追跡することもできます。  

1. **[更新]** をクリックして変更を適用します。


## <a name="disable-data-collection"></a>データ収集を無効にする
データ収集はいつでも停止できます。 データ収集を無効にするには、Python コードまたは Azure portal を使用します。

+ オプション 1 - Azure portal で無効にする: 
  1. [Azure ポータル](https://portal.azure.com)にサインインします。

  1. ワークスペースを開きます。

  1. **[デプロイ]** -> **[サービスの選択]** -> **[編集]** の順に選択します。

     ![サービスの編集](media/how-to-enable-data-collection/EditService.png)

  1. **[詳細設定]** で、**[モデルのデータ コレクションを有効にする]** をオフにします。 

     ![データ コレクションをオフにする](media/how-to-enable-data-collection/UncheckDataCollection.png) 

  1. **[更新]** をクリックして変更を適用します。

* オプション 2 - Python を使用してデータ収集を無効にする:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="example-notebook"></a>ノートブックの例

[00.Getting Started/12.enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/12.enable-data-collection-for-models-in-aks) ノートブックは、この記事の概念を示しています。  

このノートブックの入手:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]