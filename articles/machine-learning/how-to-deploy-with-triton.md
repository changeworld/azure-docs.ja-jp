---
title: Triton を使用した高パフォーマンス モデルのサービス (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で NVIDIA Triton 推論サーバーを使用してモデルを展開する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 11/03/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.author: ssambare
author: shivanissambare
ms.custom: deploy, devplatv2
ms.openlocfilehash: 017ddd81ea07f3febc4d708024d448f3b09c5344
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723584"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Triton 推論サーバーを使用した高パフォーマンスのサービス (プレビュー) 

Azure Machine Learning で[マネージド オンライン エンドポイント](concept-endpoints.md#managed-online-endpoints)と共に [NVIDIA Triton 推論サーバー](https://aka.ms/nvidia-triton-docs)を使用する方法について説明します。

Triton は、推論用に最適化されたマルチフレームワークのオープンソース ソフトウェアです。 TensorFlow、ONNX Runtime、PyTorch、NVIDIA TensorRT などの一般的な機械学習フレームワークをサポートしています。 CPU または GPU のワークロードに使用できます。

この記事では、Triton とモデルをマネージド オンライン エンドポイントにデプロイする方法について説明します。 CLI (コマンド ライン) と Azure Machine Learning スタジオの両方の使用に関する情報を提供します。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) は、Azure Machine Learning に統合されたオープンソースのサードパーティ製ソフトウェアです。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [basic prereqs](../../includes/machine-learning-cli-prereqs.md)]

* 動作する Python 3.8 (以上) の環境。

* Azure サブスクリプションの NCv3 シリーズ VM へのアクセス。

    > [!IMPORTANT]
    > このシリーズ VM を使用するには、事前にサブスクリプションのクォータの引き上げを要求しなければならない場合があります。 詳細については、「[NCv3 シリーズ](../virtual-machines/ncv3-series.md)」を参照してください。

[!INCLUDE [clone repo & set defaults](../../includes/machine-learning-cli-prepare.md)]

NVIDIA Triton 推論サーバーには特定のモデル リポジトリ構造が必要です。この構造には、モデルごとのディレクトリとモデル バージョンのサブディレクトリが含まれています。 各モデル バージョンのサブディレクトリの内容は、モデルの種類とモデルをサポートするバックエンドの要件によって決まります。 すべてのモデル リポジトリ構造を表示する場合: [https://github.com/triton-inference-server/server/blob/main/docs/model_repository.md#model-files](https://github.com/triton-inference-server/server/blob/main/docs/model_repository.md#model-files)

このドキュメントの情報は、ONNX 形式で格納されたモデルの使用に基づいているため、モデル リポジトリのディレクトリ構造は `<model-repository>/<model-name>/1/model.onnx` です。 具体的には、このモデルで画像の識別を行います。

## <a name="deploy-using-cli-v2"></a>CLI (v2) を使用してデプロイする

このセクションでは、Machine Learning 拡張機能 (v2) を備えた Azure CLI を使用して Triton をマネージド オンライン エンドポイントにデプロイする方法を示します。

> [!IMPORTANT]
> Triton のコードなしのデプロイの場合、 **[ローカル エンドポイントを使用したテスト](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints)** は現在サポートされていません。

1. 複数のコマンドでパスを入力せずにすむように、次のコマンドを使用して `BASE_PATH` 環境変数を設定します。 この変数は、モデルとそれに関連する YAML 構成ファイルが格納されているディレクトリを示します。

    ```azurecli
    BASE_PATH=endpoints/online/triton/single-model
    ```

1. 次のコマンドを使用して、作成するエンドポイントの名前を設定します。 この例では、エンドポイントにランダムな名前を作成します。

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="set_endpoint_name":::

1. 次のコマンドを使用して Python の要件をインストールします。

    ```azurecli
    pip install numpy
    pip install tritonclient[http]
    pip install pillow
    pip install gevent
    ```

1. エンドポイントの YAML 構成ファイルを作成します。 次の例では、エンドポイントの名前と認証モードを構成します。 次のコマンドで使用されているものは、前に複製した azureml-examples リポジトリ内の `/cli/endpoints/online/triton/single-model/create-managed-endpoint.yml` にあります。

    __create-managed-endpoint.yaml__

    :::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/triton/single-model/create-managed-endpoint.yaml":::

1. YAML 構成を使用して新しいエンドポイントを作成するには、次のコマンドを使用します。

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="create_endpoint":::

1. デプロイの YAML 構成ファイルを作成します。 次の例では、前の手順で作成したエンドポイントに対して __blue__ という名前のデプロイを構成します。 次のコマンドで使用されているものは、前に複製した azureml-examples リポジトリ内の `/cli/endpoints/online/triton/single-model/create-managed-deployment.yml` にあります。

    > [!IMPORTANT]
    > Triton のコードなしのデプロイ (NCD) が機能するには、 **`model_format`** を **`Triton`** に設定する必要があります。 詳細については、[「CLI (v2) モデル YAML スキーマ」を確認](reference-yaml-model.md)してください。
    >
    > このデプロイでは、Standard_NC6s_v3 VM を使用します。 この VM を使用するには、事前にサブスクリプションのクォータの引き上げを要求しなければならない場合があります。 詳細については、「[NCv3 シリーズ](../virtual-machines/ncv3-series.md)」を参照してください。

    :::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/triton/single-model/create-managed-deployment.yaml":::

1. YAML 構成を使用してデプロイを作成するには、次のコマンドを使用します。

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="create_deployment":::

### <a name="invoke-your-endpoint"></a>エンドポイントを呼び出す

デプロイが完了したら、次のコマンドを使用して、デプロイされたエンドポイントにスコアリング要求を行います。 

> [!TIP]
> スコアリングには、azureml-examples リポジトリのファイル `/cli/endpoints/online/triton/single-model/triton_densenet_scoring.py` が使用されます。 エンドポイントに渡される画像には、サイズ、種類、形式の要件を満たすための前処理と、予測ラベルを示すための後処理が必要です。 `triton_densenet_scoring.py` では、`tritonclient.http` ライブラリを使用して Triton 推論サーバーと通信します。

1. エンドポイントのスコアリング URI を取得するには、次のコマンドを使用します。

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="get_scoring_uri":::

1. 認証トークンを取得するには、次のコマンドを使用します。

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="get_token":::

1. エンドポイントを使用してデータをスコア付けするには、次のコマンドを使用します。 これは、クジャクの画像 (https://aka.ms/peacock-pic) ) をエンドポイントに送信します。

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="check_scoring_of_model":::

    スクリプトからの応答は次のテキストのようになります。

    ```
    Is server ready - True
    Is model ready - True
    /azureml-examples/cli/endpoints/online/triton/single-model/densenet_labels.txt
    84 : PEACOCK
    ```

### <a name="delete-your-endpoint-and-model"></a>エンドポイントとモデルを削除する

エンドポイントを使い終えたら、次のコマンドを使用して削除します。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="delete_endpoint":::

モデルを削除するには次のコマンドを使用します。

```azurecli
az ml model delete --name $MODEL_NAME --version $MODEL_VERSION
```

## <a name="deploy-using-azure-machine-learning-studio"></a>Azure Machine Learning スタジオを使用してデプロイする

このセクションでは、[Azure Machine Learning スタジオ](https://ml.azure.com)を使用して Triton をマネージド オンライン エンドポイントにデプロイする方法を示します。

1. 次の YAML および CLI コマンドを使用して、モデルを Triton 形式で登録します。 YAML では、[https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/triton/single-model](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/triton/single-model) の densenet-onnx モデルを使用します

    __create-triton-model.yaml__

    ```yml
    name: densenet-onnx-model
    version: 1
    local_path: ./models
    model_format: Triton
    description: Registering my Triton format model.
    ```

    ```azurecli
    az ml model create -f create-triton-model.yaml
    ```

    次のスクリーンショットは、Azure Machine Learning スタジオの __[Models]\(モデル\) ページ__ で登録済みモデルがどのように表示されるかを示しています。

    :::image type="content" source="media/how-to-deploy-with-triton/triton-model-format.png" lightbox="media/how-to-deploy-with-triton/triton-model-format.png" alt-text="[Models]\(モデル\) ページの Triton モデル形式を示すスクリーンショット。":::


1. [スタジオ](https://ml.azure.com)から、ワークスペースを選択し、 __[エンドポイント]__ または __[Models]\(モデル\)__ ページを使用してエンドポイントのデプロイを作成します。

    # <a name="endpoints-page"></a>[[エンドポイント] ページ](#tab/endpoint)

    1. __[エンドポイント]__ ページで、 **[+ 作成 (プレビュー)]** を選択します。

        :::image type="content" source="media/how-to-deploy-with-triton/create-option-from-endpoints-page.png" lightbox="media/how-to-deploy-with-triton/create-option-from-endpoints-page.png" alt-text="[エンドポイント] UI ページの [作成] オプションを示すスクリーンショット。":::

    1. エンドポイントの名前と認証の種類を指定し、 __[次へ]__ を選択します。
    1. モデルを選択する場合は、前に登録した Triton モデルを選択します。 __[次へ]__ をクリックして続行します。

    1. Triton 形式で登録されたモデルを選択すると、ウィザードの [環境] ステップではスコアリング スクリプトと環境は必要ありません。

        :::image type="content" source="media/how-to-deploy-with-triton/ncd-triton.png" lightbox="media/how-to-deploy-with-triton/ncd-triton.png" alt-text="Triton モデルに必要なコードと環境がないことを示すスクリーンショット":::

    1. ウィザードを完了して、モデルをエンドポイントにデプロイします。

        :::image type="content" source="media/how-to-deploy-with-triton/review-screen-triton.png" lightbox="media/how-to-deploy-with-triton/review-screen-triton.png" alt-text="NCD レビュー画面を示すスクリーンショット":::

    # <a name="models-page"></a>[[Models]\(モデル\) ページ](#tab/models)

    1. Triton モデルを選択し、 __[展開する]__ を選択します。 プロンプトが表示されたら、 __[リアルタイム エンドポイントへのデプロイ (プレビュー)]__ を選択します。

        :::image type="content" source="media/how-to-deploy-with-triton/deploy-from-models-page.png" lightbox="media/how-to-deploy-with-triton/deploy-from-models-page.png" alt-text="[Models]\(モデル\) UI からモデルをデプロイする方法を示すスクリーンショット":::

    1. ウィザードを完了して、モデルをエンドポイントにデプロイします。

    ---

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。

- [REST を使用してモデルをデプロイする (プレビュー)](how-to-deploy-with-rest.md)
- [スタジオでマネージド オンライン エンドポイント (プレビュー) を作成および使用する](how-to-use-managed-online-endpoint-studio.md)
- [オンライン エンドポイントの安全なロールアウト (プレビュー)](how-to-safely-rollout-managed-endpoints.md)
- [マネージド オンライン エンドポイントを自動スケーリングする方法](how-to-autoscale-endpoints.md)
- [Azure Machine Learning のマネージド オンライン エンドポイント (プレビュー) のコストを表示する](how-to-view-online-endpoints-costs.md)
- [マネージド オンライン エンドポイントとマネージド ID (プレビュー) を使用して Azure リソースにアクセスする](how-to-access-resources-from-endpoints-managed-identities.md)
- [マネージド オンライン エンドポイントのデプロイトをラブルシューティングする](how-to-troubleshoot-managed-online-endpoints.md)