---
title: Triton を使用した高パフォーマンス モデルのサービス (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で NVIDIA Triton 推論サーバーを使用してモデルを展開する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 05/17/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: b8ccc8eb55031f583eba24368fca66e0943124b1
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110064848"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Triton 推論サーバーを使用した高パフォーマンスのサービス (プレビュー) 

[NVIDIA Triton 推論サーバー](https://aka.ms/nvidia-triton-docs)を使用して、モデルの推論に使用する Web サービスのパフォーマンスを向上させる方法について説明します。

推論用のモデルを展開する方法の 1 つは、Web サービスとして展開することです。 たとえば、Azure Kubernetes Service または Azure Container Instances への展開です。 既定では、Azure Machine Learning はシングルスレッドの "*汎用*" Web フレームワークを使用して Web サービスを展開します。

Triton は、"*推論用に最適化された*" フレームワークです。 GPU の使用率が高く、コスト効率に優れた推論を実現します。 サーバー側では、受信した要求をバッチ処理し、推論のためにこれらのバッチを送信します。 バッチ処理は GPU リソースのより適切な利用を可能にする、Triton のパフォーマンスにおける重要な部分です。

> [!IMPORTANT]
> Azure Machine Learning からの展開に対する Triton の使用は、現在 __プレビュー__ 段階です。 プレビューの機能は、カスタマー サポートの対象になっていない場合があります。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

> [!TIP]
> このドキュメントに記載されているコード スニペットは説明を目的としたものであり、完全なソリューションとなっていない場合があります。 動作するコード例については、[Azure Machine Learning での Triton のエンドツーエンドのサンプル](https://aka.ms/triton-aml-sample)を参照してください。

> [!NOTE]
> [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) は、Azure Machine Learning に統合されたオープンソースのサードパーティ製ソフトウェアです。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 お持ちでない場合は、[無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。
* Azure Machine Learning で[モデルを展開する方法と場所](how-to-deploy-and-where.md)について理解していること。
* [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/) **または** [Azure CLI](/cli/azure/) と [機械学習拡張機能](reference-azure-machine-learning-cli.md)。
* ローカル テスト用の Docker の動作するインストール。 Docker のインストールと検証の詳細については、[オリエンテーションとセットアップ](https://docs.docker.com/get-started/)に関する Docker ドキュメントを参照してください。

## <a name="architectural-overview"></a>アーキテクチャの概要

独自のモデルに Triton を使用する前に、Triton がどのように Azure Machine Learning と連携して動作し、既定の展開と比較してどう違うかを理解することが重要です。

**Triton を使用しない既定の展開**

* 受信した要求を同時に処理するために、複数の [Gunicorn](https://gunicorn.org/) ワーカーが開始されています。
* これらのワーカーでは、前処理、モデルの呼び出し、および後処理が行われます。 
* クライアントでは、__Azure ML スコアリング URI__ が使用されます。 たとえば、「 `https://myservice.azureml.net/score` 」のように入力します。

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="標準の、Triton を使用しない展開アーキテクチャの図":::

**Triton を使用した直接デプロイ**

* 要求は、Triton サーバーに直接送られます。
* Triton によって、GPU 使用率を最大化するために要求がバッチ処理されます。
* クライアントは、__Triton URI__ を使用して要求を行います。 たとえば、「 `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer` 」のように入力します。

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Python ミドルウェアを使用せずに、Triton のみを使用した Inferenceconfig のデプロイ":::


## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Python の前および後処理を使用せずに Triton をデプロイする

まず、下の手順に従って、Triton 推論サーバーがモデルに使用できることを確認します。

### <a name="optional-define-a-model-config-file"></a>(省略可能) モデル構成ファイルの定義

モデル構成ファイルでは、想定される入力の数と、それらの入力の次元を Triton に対して指定します。 構成ファイルの作成の詳細については、[モデルの構成](https://aka.ms/nvidia-triton-docs)に関する NVIDIA ドキュメントを参照してください。

> [!TIP]
> Triton 推論サーバーの起動時に `--strict-model-config=false` オプションを使用します。これは、ONNX または TensorFlow モデルに `config.pbtxt` ファイルを指定する必要がないことを意味します。
> 
> このオプションの詳細については、[生成されたモデルの構成](https://aka.ms/nvidia-triton-docs)に関する NVIDIA ドキュメントを参照してください。

### <a name="use-the-correct-directory-structure"></a>正しいディレクトリ構造を使用する

Azure Machine Learning にモデルを登録する場合、個々のファイルまたはディレクトリ構造を登録できます。 Triton を使用するには、`triton` という名前のディレクトリを含むディレクトリ構造に対してモデルの登録を行う必要があります。 このディレクトリの一般的な構造は次のとおりです。

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
            - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> このディレクトリ構造は Triton モデル リポジトリであり、モデルを Triton で動作させるために必要です。 詳細については、[Triton モデル リポジトリ](https://aka.ms/nvidia-triton-docs)に関する NVIDIA ドキュメントを参照してください。

### <a name="register-your-triton-model"></a>Triton モデルを登録する

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

`az ml model register` に関する詳細については、[リファレンス ドキュメント](/cli/azure/ml/model)を参照してください。

Azure Machine Learning にモデルを登録するときは、`--model-path  -p` パラメーターの値を、Triton Model Repository の親フォルダーの名前にする必要があります。
上記の例では `--model-path` は 'models' です。

`--name  -n` パラメーターの値は、Azure Machine Learning Workspace で認識しているモデルの名前にします。例では `my_triton_models` となっています。 

# <a name="python"></a>[Python](#tab/python)


[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=register-model)]

詳細については、[Model クラス](/python/api/azureml-core/azureml.core.model.model)のドキュメントを参照してください。

---

### <a name="deploy-your-model"></a>モデルをデプロイする

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Azure Machine Learning によって作成された "aks-gpu" という GPU 対応の Azure Kubernetes Service クラスターがある場合は、次のコマンドを使用してモデルをデプロイできます。

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=deploy-webservice)]

---

[モデルのデプロイの詳細については、こちらのドキュメントを](how-to-deploy-and-where.md)参照してください。

### <a name="call-into-your-deployed-model"></a>デプロイしたモデルを呼び出す

最初に、スコアリング URI とベアラー トークンを取得します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=get-keys)]

---

サービスが実行されていることを確認するために、次を実行します。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

このコマンドでは、次のような情報が返されます。 `200 OK` に注目してください。この状態は、Web サーバーが実行中であることを示します。

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=query-service)]

---


正常性チェックを実行したら、推論のために Triton にデータを送信するクライアントを作成できます。 クライアントの作成の詳細については、[クライアントの例](https://aka.ms/nvidia-client-examples)に関する NVIDIA ドキュメントを参照してください。 [Triton GitHub に Python のサンプル](https://aka.ms/nvidia-triton-docs)もあります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Machine Learning ワークスペースの使用を継続する予定で、デプロイされたサービスを削除する場合は、次のオプションのいずれかを使用します。


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=delete-service)]

---
## <a name="troubleshoot"></a>トラブルシューティング

* [失敗したデプロイのトラブルシューティング](how-to-troubleshoot-deployment.md): モデルのデプロイ時に発生する可能性のある一般的なエラーのトラブルシューティングと解決、回避方法について説明します。

* デプロイしたログに、**TritonServer が起動に失敗した** ことが表示される場合は、[Nvidia のオープン ソース ドキュメント](https://github.com/triton-inference-server/server)を参考にしてください。

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning での Triton のエンドツーエンドのサンプルを参照する](https://aka.ms/aml-triton-sample)
* [Triton クライアントの例](https://aka.ms/nvidia-client-examples)を確認する
* [Triton 推論サーバーのドキュメント](https://aka.ms/nvidia-triton-docs)を読む
* [Azure Kubernetes Service にデプロイする](how-to-deploy-azure-kubernetes-service.md)
* [Web サービスを更新する](how-to-deploy-update-web-service.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
