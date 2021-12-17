---
title: 事前構築済み Docker イメージを拡張する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の事前構築済み Docker イメージを拡張する
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 10/21/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt
ms.openlocfilehash: a25416951d7cc124397d4701992ec9a04968d443
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562411"
---
# <a name="extend-a-prebuilt-docker-image"></a>事前構築済み Docker イメージを拡張する

Azure Machine Learning の[拡張](./how-to-prebuilt-docker-images-inference-python-extensibility.md)ソリューションと[モデル推論用に事前構築済みの Docker イメージ](concept-prebuilt-docker-images-inference.md)では、実際の推論サービスのニーズを満たせない場合もあります。

その場合は、Dockerfile を使用すると、あらかじめ構築されたいずれかのイメージを出発点として新しいイメージを作成できます。 既存の事前構築済み Docker イメージを拡張することで、Azure Machine Learning のネットワーク スタックとライブラリを利用でき、イメージをゼロから作成する必要がありません。

**メリットとトレードオフ**

Dockerfile を使用すると、デプロイ前にイメージをフルにカスタマイズできます。 特に、コンテナーで設定する依存関係や環境変数を最大限制御することができます。

この方法の主なトレードオフは、デプロイ中に別途イメージのビルドが実行されるために、デプロイ プロセスに時間がかかることです。 [Python パッケージ拡張](./how-to-prebuilt-docker-images-inference-python-extensibility.md)手法を使用した方が、デプロイは短時間で済みます。
## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 ワークスペースの作成に関するチュートリアルについては、[Azure Machine Learning の利用開始](quickstart-create-resources.md)に関するページを参照してください。
* [Dockerfile](https://docs.docker.com/engine/reference/builder/) の作成について熟知していること。
* [Docker](https://www.docker.com/) のローカル作業環境 (`docker` CLI を含む)、**または** Azure Machine Learning ワークスペースに関連付けられた Azure Container Registry (ACR)。

    > [!WARNING]
    > ワークスペースの Azure Container Registry は、ワークスペースを使用して モデルを初めてトレーニングまたはデプロイするときに作成されます。 新しいワークスペースを作成し、モデルのトレーニングも作成も行っていない場合、そのワークスペースの Azure Container Registry は存在しません。
## <a name="create-and-build-dockerfile"></a>Dockerfile を作成してビルドする

次に示すのは、Azure Machine Learning の事前構築済み Docker イメージを基本イメージとして使用するサンプル Dockerfile です。

```Dockerfile
FROM mcr.microsoft.com/azureml/<image_name>:<tag>

COPY requirements.txt /tmp/requirements.txt

RUN pip install –r /tmp/requirements.txt
```

次に、必要なすべてのファイルと共に上記の Dockerfile をディレクトリに格納し、次のコマンドを実行してイメージをビルドします。

```bash
docker build -f <above dockerfile> -t <image_name>:<tag> .
```

> [!TIP]
> `docker build` の詳細については、[Docker のドキュメント](https://docs.docker.com/engine/reference/commandline/build/)を参照してください。

ローカルから `docker build` コマンドが利用できない場合は、Azure Machine Learning ワークスペースの Azure Container Registry ACR を使用して、Docker イメージをクラウドにビルドします。 詳細については、[Azure Container Registry でコンテナー イメージをビルド、デプロイする方法に関するチュートリアル](../container-registry/container-registry-tutorial-quick-task.md)を参照してください。

> [!IMPORTANT]
> Microsoft では、Azure Container Registry でカスタム基本イメージを作成しようとする前に、Dockerfile がローカルで正しく動作するか確認することを推奨しています。

以降のセクションでは、Dockerfile についてさらに詳しく説明していきます。

## <a name="install-extra-packages"></a>追加のパッケージをインストールする

Ubuntu コンテナーにインストールすべき `apt` パッケージが他にもあれば、それらを Dockerfile に追加できます。 次の例は、Dockerfile から `apt-get` コマンドを使用する方法を示しています。

```Dockerfile
FROM <prebuilt docker image from MCR>

# Switch to root to install apt packages
USER root:root

RUN apt-get update && \
    apt-get install -y \
    <package-1> \
    ... 
    <package-n> && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/*

# Switch back to non-root user
USER dockeruser
```

Dockerfile から追加の pip パッケージをインストールすることもできます。 `pip install` の使用例を次に示します。

```Dockerfile
RUN pip install <library>
```

<a id="buildmodel"></a>

## <a name="build-model-and-code-into-images"></a>モデルとコードをイメージに組み込む

モデルとコードをイメージに組み込む必要がある場合、次の環境変数を Dockerfile で設定する必要があります。

* `AZUREML_ENTRY_SCRIPT`: コードのエントリ スクリプト。 このファイルに、`init()` メソッドと `run()` メソッドが記述されます。
* `AZUREML_MODEL_DIR`: モデル ファイルが格納されるディレクトリ。 エントリ スクリプトは、このディレクトリをモデルのルート ディレクトリとして使用する必要があります。

次の例は、Dockerfile でこれらの環境変数を設定する方法を示しています。

```Dockerfile
FROM <prebuilt docker image from MCR>

# Code
COPY <local_code_directory> /var/azureml-app
ENV AZUREML_ENTRY_SCRIPT=<entryscript_file_name>

# Model
COPY <model_directory> /var/azureml-app/azureml-models
ENV AZUREML_MODEL_DIR=/var/azureml-app/azureml-models
```

## <a name="example-dockerfile"></a>Dockerfile の例

次の例は、`apt` パッケージをインストールし、環境変数を設定して、Dockerfile にコードとモデルを組み込む方法を示しています。

```Dockerfile
FROM mcr.microsoft.com/azureml/pytorch-1.6-ubuntu18.04-py37-cpu-inference:latest 

USER root:root

# Install libpng-tools and opencv
RUN apt-get update && \
    apt-get install -y \
    libpng-tools \
    python3-opencv && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/*

# Switch back to non-root user
USER dockeruser

# Code
COPY code /var/azureml-app
ENV AZUREML_ENTRY_SCRIPT=score.py

# Model
COPY model /var/azureml-app/azureml-models
ENV AZUREML_MODEL_DIR=/var/azureml-app/azureml-models
```

## <a name="next-steps"></a>次の手順

Azure Machine Learning Python SDK で Dockerfile を使用する方法については、次のドキュメントを参照してください。

* [独自のローカル Dockerfile を使用する](how-to-use-environments.md#use-your-own-dockerfile)
* [事前構築済み Docker イメージを使用してカスタム基本イメージを作成する](how-to-use-environments.md#use-a-prebuilt-docker-image)

モデルのデプロイについて詳しくは、[モデルのデプロイ方法](how-to-deploy-and-where.md)に関するページを参照してください。

事前構築済み Docker イメージのデプロイをトラブルシューティングする方法については、[事前構築済み Docker イメージのデプロイをトラブルシューティングする方法](how-to-troubleshoot-prebuilt-docker-image-inference.md)に関するページを参照してください。