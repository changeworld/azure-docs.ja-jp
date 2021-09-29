---
title: マネージド オンライン エンドポイントとしてカスタム コンテナーをデプロイする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でカスタム コンテナーを使用してオープンソース サーバーを使用する方法を説明します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 06/16/2021
ms.topic: how-to
ms.custom: deploy, devplatv2
ms.openlocfilehash: b5438ccfcd84a9574e7e65eb9d0342f947f4d03b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599470"
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

```azurecli-interactive
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples/cli
```

## <a name="initialize-environment-variables"></a>環境変数を初期化する

環境変数を定義します。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="initialize_variables":::

## <a name="download-a-tensorflow-model"></a>TensorFlow モデルをダウンロードする

入力を 2 で除算して結果に 2 を加算するモデルをダウンロードして解凍します。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="download_and_unzip_model":::

## <a name="run-a-tf-serving-image-locally-to-test-that-it-works"></a>TF Serving イメージをローカル環境で実行して動作することをテストする

docker を使用して、テストのためにローカル環境でイメージを実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="run_image_locally_for_testing":::

### <a name="check-that-you-can-send-liveness-and-scoring-requests-to-the-image"></a>イメージに liveness とスコアリングの要求を送信できることを調べる

最初に、コンテナーが "アライブ" であること、つまりコンテナー内部のプロセスがまだ実行されていることを確認します。 200 (OK) 応答を受け取る必要があります。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="check_liveness_locally":::

次に、ラベル付けされていないデータに関する予測を取得できることを確認します。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="check_scoring_locally":::

### <a name="stop-the-image"></a>イメージを停止する

ローカル環境でのテストが済んだので、イメージを停止します。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="stop_image":::

## <a name="create-a-yaml-file-for-your-endpoint"></a>エンドポイント用の YAML ファイルを作成する

YAML を使用してクラウド デプロイを構成できます。 このエンドポイント用の YAML の例をご覧ください。

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/custom-container/tfserving-endpoint.yml":::

この YAML には、注意すべき重要な概念がいくつかあります。

### <a name="readiness-route-vs-liveness-route"></a>readiness ルートと liveness ルート

HTTP サーバーでは、必要に応じて、_liveness_ と _readiness_ 両方のパスを定義できます。 liveness ルートは、サーバーが実行されているかどうかを調べるために使用されます。 readiness ルートは、サーバーが何らかの作業を行う準備ができているかどうかを調べるために使用されます。 機械学習の推論では、サーバーはモデルを読み込む前に、liveness 要求に対して 200 OK を応答できます。 サーバーは、モデルがメモリに読み込まれた後でのみ、readiness 要求に対して 200 OK を応答できます。

liveness probe と readiness probe の詳細については、[Kubernetes のドキュメント](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)を参照してください。

このデプロイでは、TF Serving で liveness ルートだけが定義されているため、liveness と readiness の両方に同じパスを使用することにご注意ください。

### <a name="locating-the-mounted-model"></a>マウントされたモデルの配置

モデルをリアルタイム エンドポイントとしてデプロイすると、Azure Machine Learning によってモデルがエンドポイントに "_マウント_" されます。 モデルがマウントされると、新しい Docker イメージを作成することなく、モデルの新しいバージョンをデプロイできます。 既定では、名前 *foo* およびバージョン *1* で登録されたモデルは、デプロイされたコンテナーの内部の次のパスに配置されます: `/var/azureml-app/azureml-models/foo/1`

たとえば、次のようなディレクトリ構造のローカル コンピューターがあるとします。

```
azureml-examples
  cli
    endpoints
      online
        custom-container
          half_plus_two
          tfserving-endpoint.yml    
```     

そして、`tfserving-endpoint.yml` には以下が含まれます。

```
model:
    name: tfserving-mounted
    version: 1
    local_path: ./half_plus_two
```

この場合、モデルはエンドポイント内の次の場所にあります。

```
var 
  azureml-app
    azureml-models
      tfserving-endpoint
        1
          half_plus_two
```

### <a name="create-the-endpoint"></a>エンドポイントを作成する

YAML の構築方法がわかったので、自分でエンドポイントを作成してください。 このコマンドが完了するまで数分かかることがあります。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="create_endpoint":::

### <a name="invoke-the-endpoint"></a>エンドポイントを呼び出す

デプロイが完了したら、デプロイされたエンドポイントにスコアリング要求を行うことができるかどうかを確認します。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="invoke_endpoint":::

### <a name="delete-endpoint-and-model"></a>エンドポイントとモデルを削除する

エンドポイントで正常にスコアリングできたので、それを削除してかまいません。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="delete_endpoint_and_model":::

## <a name="next-steps"></a>次の手順

- [オンライン エンドポイントの安全なロールアウト (プレビュー)](how-to-safely-rollout-managed-endpoints.md)
- [マネージド オンライン エンドポイントのデプロイのトラブルシューティング](how-to-troubleshoot-managed-online-endpoints.md)
- [Torch serve サンプル](https://github.com/Azure/azureml-examples/blob/main/cli/deploy-torchserve.sh)