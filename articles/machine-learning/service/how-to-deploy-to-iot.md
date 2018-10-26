---
title: Azure Machine Learning サービスから IoT Edge デバイスへのモデルのデプロイ |Microsoft Docs
description: Azure Machine Learning サービスから Azure IoT Edge デバイスにモデルをデプロイする方法について説明します。 Edge デバイスにモデルをデプロイすると、データをクラウドに送信して応答を待機するのではなく、デバイス上でデータをスコア付けすることができます。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shipatel
author: shivanipatel
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: 7d706cf71761496fd740c729224ee4331eeb2911
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091625"
---
# <a name="prepare-to-deploy-models-on-iot-edge"></a>IoT Edge にモデルをデプロイするための準備

このドキュメントでは、Azure Machine Learning サービスを使用して、Azure IoT Edge デバイスにデプロイするためにトレーニング済みのモデルを準備する方法を説明します。

Azure IoT Edge デバイスとは、Azure IoT Edge ランタイムを実行している Linux または Windows ベースのデバイスです。 機械学習モデルは、これらのデバイスに IoT Edge モジュールとしてデプロイできます。 IoT Edge デバイスにモデルをデプロイすると、データを処理するためにクラウドに送信する必要がなくなり、デバイスがモデルを直接使用できます。 応答時間が早くなり、データ転送量が少なくなります。

モデルを Edge デバイスにデプロイする前に、このドキュメントの手順に従ってモデルとデバイスを準備します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

* Azure Machine Learning ワークスペース。 これを作成するには、「[Azure Machine Learning サービスの概要](quickstart-get-started.md)」のドキュメントにある手順に従います。

* 開発環境。 詳細については、「[開発環境を構成する方法](how-to-configure-environment.md)」のドキュメントを参照してください。

* Azure サブスクリプション内の [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md)。 

* トレーニング済みのモデル。 モデルをトレーニングする方法の例は、「[Azure Machine Learning で画像分類モデルをトレーニングする方法](tutorial-train-models-with-aml.md)」のドキュメントを参照してください。 あらかじめトレーニングされたモデルは、[Azure IoT Edge 用 AI ツールキットの GitHub リポジトリ](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial)で入手できます。

## <a name="prepare-the-iot-device"></a>IoT デバイスの準備

デバイスを登録し、IoT ランタイムをインストールする方法については、「[クイック スタート: 初めての IoT Edge モジュールを Linux x64 デバイスに展開する](../../iot-edge/quickstart-linux.md)」というドキュメントの手順に従います。

## <a name="register-the-model"></a>モデルを登録する

Azure IoT Edge モジュールはコンテナー イメージに基づいています。 IoT Edge デバイスにモデルをデプロイするには、次の手順に従って Azure Machine Learning サービス ワークスペースにモデルを登録し、Docker イメージを作成します。 

1. ワークスペースを初期化し、config.json ファイルを読み込みます。

    ```python
    from azureml.core  import Workspace

    #Load existing workspace from the config file info.
    ws  = Workspace.from_config()
    ```    

1. モデルをワークスペースに登録します。 既定のテキストを、実際のモデルのパス、名前、タグ、説明に置き換えます。

    > [!IMPORTANT]
    > 以前に Azure Machine Learning を使用してモデルをトレーニングしたことがある場合は、モデルがワークスペースに既に登録されていることがあります。 その場合は、この手順をスキップしてください。 このワークスペースに登録されているモデルの一覧を表示するには、`Model.list(ws)` を使用します。

    ```python
    from azureml.core.model import Model
    model = Model.register(model_path = "model.pkl", # this path points to the local file
                        model_name = "best_model", # the model gets registered as this name
                        tags = {'attribute': "myattribute", 'classification': "myclassification"},
                        description = "My awesome model",
                        workspace = ws)
    ```    

1. 登録したモデルを取得します。 

    ```python
    from azureml.core.model import Model

    model_name = "best_model"
    model = Model(ws, model_name)                     
    ```    

## <a name="create-a-docker-image"></a>Docker イメージを作成します。

1. `score.py` という名前の**スコアリング スクリプト**を作成します。 このファイルは、イメージ内のモデルを実行するために使用されます。 これには次の関数を含める必要があります。

    * `init()` 関数。通常、グローバル オブジェクトにモデルを読み込みます。 この関数は、Docker コンテナーを開始するときに 1 回だけ実行されます。 

    * `run(input_data)` 関数。モデルを使用して、入力データに基づく値を予測します。 実行に対する入力と出力は、通常、JSON を使用してシリアル化およびシリアル化解除が実行されますが、その他の形式もサポートされています。

    例については、[画像分類のチュートリアル](tutorial-deploy-models-with-aml.md#make-script)をご覧ください。

1. `myenv.yml` という名前の**環境ファイル**を作成します。 このファイルは Conda 環境仕様で、スクリプトとモデルで必要なすべての依存関係の一覧が含まれています。 例については、[画像分類のチュートリアル](tutorial-deploy-models-with-aml.md#make-myenv)をご覧ください。

1. `score.py` および `myenv.yml` ファイルを使用して Docker イメージを構成します。
    
    ```python
    from azureml.core.image import Image, ContainerImage
    
    #Image configuration
    image_config = ContainerImage.image_configuration( runtime = "python", 
                           execution_script = "score.py",
                           conda_file = "myenv.yml", 
                           tags = {"attributes", "calssification"},
                           description = "Image that contains my model",
                           
                        )
    ```    

1. モデルとイメージ構成を使用してイメージを作成します。

    ```python
    image = ContainerImage.create (name = "myimage", 
                           models = [model], #this is the model object
                           image_config = image_config,
                           workspace = ws
                        )
    ```     

    イメージの作成には 5 分ほどかかります。

## <a name="get-the-container-registry-credentials"></a>コンテナー レジストリの資格情報の取得

Azure IoT では、Azure Machine Learning サービスが Docker イメージを格納するコンテナー レジストリの資格情報が必要です。 資格情報を取得するには次の手順に従います。

1. [Azure Portal](https://portal.azure.com/signin/index) にサインインします。

1. Azure Machine Learning サービス ワークスペースに移動し、__[概要]__ を選択します。 コンテナー レジストリの設定に移動するには、__[レジストリ]__ リンクを選択します。

    ![コンテナー レジストリ エントリの画像](./media/how-to-deploy-to-iot/findregisteredcontainer.png)

1. コンテナー レジストリに入ったら、**[アクセス キー]** を選択して、管理者ユーザーを有効にします。

    ![アクセス キー画面の画像](./media/how-to-deploy-to-iot/findaccesskey.png)

1. ログイン サーバー、ユーザー名、パスワードの値を保存します。 

   これらの資格情報は、プライベート コンテナー レジストリ内のイメージに IoT Edge デバイスからアクセスできるようにするために提供する必要があります。

## <a name="next-steps"></a>次の手順

これでデプロイの準備が完了しました。 次に、「[Azure Portal から Azure IoT Edge モジュールをデプロイする](../../iot-edge/how-to-deploy-modules-portal.md)」というドキュメントの手順に従って Edge デバイスにデプロイします。 デバイスの __レジストリ設定__ を構成するときに、前に構成した資格情報を使用します。
