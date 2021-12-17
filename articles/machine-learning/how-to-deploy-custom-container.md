---
title: マネージド オンライン エンドポイントとしてカスタム コンテナーをデプロイする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でカスタム コンテナーを使用してオープンソース サーバーを使用する方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: ssambare
author: shivanissambare
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: deploy, devplatv2
ms.openlocfilehash: 9b36a5799444b6da011693693b58f6574b135799
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553635"
---
# <a name="deploy-a-tensorflow-model-served-with-tf-serving-using-a-custom-container-in-a-managed-online-endpoint-preview"></a>マネージド オンライン エンドポイントのカスタム コンテナーを使用して TF Serving で提供される TensorFlow モデルをデプロイする (プレビュー)

Azure Machine Learning でマネージド オンライン エンドポイントとしてカスタム コンテナーをデプロイする方法を説明します。

カスタム コンテナーのデプロイでは、Azure Machine Learning によって使用される既定の Python Flask サーバー以外の Web サーバーを使用できます。 これらのデプロイのユーザーは、Azure Machine Learning の組み込みの監視、スケーリング、アラート、認証を引き続き利用できます。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!WARNING]
> Microsoft では、カスタム イメージが原因で発生した問題をトラブルシューティングできない場合があります。 問題が発生した場合は、問題がイメージに固有のものであるかどうかを確認するために、既定のイメージ、または Microsoft が提供するイメージのいずれかを使用するように求められることがあります。

## <a name="prerequisites"></a>前提条件

* Azure CLI と ML 拡張機能をインストールして構成します。 詳細については、[CLI (v2) (プレビュー) のインストール、セットアップ、使用](how-to-configure-cli.md)に関するページを参照してください。 

* Azure リソース グループがあること、また、そのリソース グループにおける `Contributor` アクセス権が自分 (または使用するサービス プリンシパル) に割り当てられていることが必要です。 上記の記事に従って ML 拡張機能を構成していれば、そのようなリソース グループが得られます。 

* Azure Machine Learning ワークスペースが必要です。 上記の記事に従って ML 拡張機能を構成していれば、そのようなワークスペースが得られます。

* まだ Azure CLI の既定値を設定していない場合は、既定の設定を保存する必要があります。 何度も値を渡さずに済むよう、次を実行します。

   ```azurecli
   az account set --subscription <subscription id>
   az configure --defaults workspace=<azureml workspace name> group=<resource group>

* To deploy locally, you must have [Docker engine](https://docs.docker.com/engine/install/) running locally. This step is **highly recommended**. It will help you debug issues.

## Download source code

To follow along with this tutorial, download the source code below.

```azurecli
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples/cli
```

## <a name="initialize-environment-variables"></a>環境変数を初期化する

環境変数を定義します。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="initialize_variables":::

## <a name="download-a-tensorflow-model"></a>TensorFlow モデルをダウンロードする

入力を 2 で除算して結果に 2 を加算するモデルをダウンロードして解凍します。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="download_and_unzip_model":::

## <a name="run-a-tf-serving-image-locally-to-test-that-it-works"></a>TF Serving イメージをローカル環境で実行して動作することをテストする

docker を使用して、テストのためにローカル環境でイメージを実行します。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="run_image_locally_for_testing":::

### <a name="check-that-you-can-send-liveness-and-scoring-requests-to-the-image"></a>イメージに liveness とスコアリングの要求を送信できることを調べる

最初に、コンテナーが "アライブ" であること、つまりコンテナー内部のプロセスがまだ実行されていることを確認します。 200 (OK) 応答を受け取る必要があります。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="check_liveness_locally":::

次に、ラベル付けされていないデータに関する予測を取得できることを確認します。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="check_scoring_locally":::

### <a name="stop-the-image"></a>イメージを停止する

ローカル環境でのテストが済んだので、イメージを停止します。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="stop_image":::

## <a name="create-a-yaml-file-for-your-endpoint-and-deployment"></a>エンドポイントおよびデプロイ用の YAML ファイルを作成する

YAML を使用してクラウド デプロイを構成できます。 この例で使用する YAML の例をご覧ください。

__tfserving-endpoint.yml__

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/custom-container/tfserving-endpoint.yml":::

__tfserving-deployment.yml__

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/custom-container/tfserving-deployment.yml":::

この YAML には、注意すべき重要な概念がいくつかあります。

### <a name="readiness-route-vs-liveness-route"></a>readiness ルートと liveness ルート

HTTP サーバーでは、_liveness_ と _readiness_ 両方のパスを定義できます。 liveness ルートは、サーバーが実行されているかどうかを調べるために使用されます。 readiness ルートは、サーバーが作業を行う準備ができているかどうかを調べるために使用されます。 機械学習の推論では、サーバーはモデルを読み込む前に、liveness 要求に対して 200 OK を応答できます。 サーバーは、モデルがメモリに読み込まれた後でのみ、readiness 要求に対して 200 OK を応答できます。

liveness probe と readiness probe の詳細については、[Kubernetes のドキュメント](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)を参照してください。

このデプロイでは、TF Serving で liveness ルートだけが定義されているため、liveness と readiness の両方に同じパスを使用することにご注意ください。

### <a name="locating-the-mounted-model"></a>マウントされたモデルの配置

モデルをリアルタイム エンドポイントとしてデプロイすると、Azure Machine Learning によってモデルがエンドポイントに "_マウント_" されます。 モデルがマウントされると、新しい Docker イメージを作成することなく、モデルの新しいバージョンをデプロイできます。 既定では、名前 *foo* およびバージョン *1* で登録されたモデルは、デプロイされたコンテナーの内部の次のパスに配置されます: `/var/azureml-app/azureml-models/foo/1`

たとえば、モデルが `half_plus_two` と名付けられている `/azureml-examples/cli/endpoints/online/custom-container` のようなディレクトリ構造のローカル コンピューターがあるとします。

:::image type="content" source="./media/how-to-deploy-custom-container/local-directory-structure.png" alt-text="ローカル ディレクトリ構造のツリー ビューを示す図。":::

そして、`tfserving-deployment.yml` には以下が含まれます。

```yaml
model:
    name: tfserving-mounted
    version: 1
    local_path: ./half_plus_two
```

この場合、モデルは次のように、デプロイの `/var/azureml-app/azureml-models/tfserving-deployment/1` の下に配置されます。

:::image type="content" source="./media/how-to-deploy-custom-container/deployment-location.png" alt-text="配置ディレクトリ構造のツリー ビューを示す図。":::

必要に応じて、`model_mount_path` を構成することもできます。 これにより、モデルがマウントされているパスを変更できます。 たとえば、以下のように _tfserving-deployment.yml_ に `model_mount_path` パラメーターを含めることができます。

> [!IMPORTANT]
> `model_mount_path` には、Linux (コンテナー イメージの OS) で有効な絶対パスを指定してください。

```YAML
name: tfserving-deployment
endpoint_name: tfserving-endpoint
model:
  name: tfserving-mounted
  version: 1
  local_path: ./half_plus_two
model_mount_path: /var/tfserving-model-mount
.....
```

この場合、モデルは次のように、デプロイの `/var/tfserving-model-mount/tfserving-deployment/1` に配置されます。 `azureml-app/azureml-models` の下ではなく、指定したマウント パスの配下にあることに注意してください。

:::image type="content" source="./media/how-to-deploy-custom-container/mount-path-deployment-location.png" alt-text="mount_model_path を使用した場合の配置ディレクトリ構造のツリー ビューを示す図":::

### <a name="create-your-endpoint-and-deployment"></a>エンドポイントとデプロイを作成する

YAML の構築方法がわかったので、自分でエンドポイントを作成してください。

```azurecli
az ml online-endpoint create --name tfserving-endpoint -f endpoints/online/custom-container/tfserving-endpoint.yml
```

デプロイの作成には数分かかる場合があります。



```azurecli
az ml online-deployment create --name tfserving-deployment -f endpoints/online/custom-container/tfserving-deployment.yml
```

### <a name="invoke-the-endpoint"></a>エンドポイントを呼び出す

デプロイが完了したら、デプロイされたエンドポイントにスコアリング要求を行うことができるかどうかを確認します。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="invoke_endpoint":::

### <a name="delete-endpoint-and-model"></a>エンドポイントとモデルを削除する

エンドポイントで正常にスコアリングできたので、それを削除してかまいません。

```azurecli
az ml online-endpoint delete --name tfserving-endpoint
```

```azurecli
az ml model delete -n tfserving-mounted --version 1
```

## <a name="next-steps"></a>次の手順

- [オンライン エンドポイントの安全なロールアウト (プレビュー)](how-to-safely-rollout-managed-endpoints.md)
- [マネージド オンライン エンドポイントのデプロイのトラブルシューティング](./how-to-troubleshoot-online-endpoints.md)
- [Torch serve サンプル](https://github.com/Azure/azureml-examples/blob/main/cli/deploy-torchserve.sh)