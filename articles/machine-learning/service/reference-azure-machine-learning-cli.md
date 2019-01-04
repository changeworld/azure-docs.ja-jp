---
title: Machine Learning CLI 拡張機能
titleSuffix: Azure Machine Learning service
description: Azure CLI 用の Azure Machine Learning CLI 拡張機能について説明します。 Azure CLI は、Azure クラウド上のリソースを操作できるようにするための、クロスプラットフォームのコマンド ライン ユーティリティです。 Machine Learning 拡張機能を使用すると、Azure Machine Learning サービスを操作することができます。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: e16506773e38f1732a55161cdd58ffb7523602d4
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53277286"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Azure Machine Learning service 用 CLI 拡張機能の使用

Azure Machine Learning CLI は [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) への拡張機能であり、Azure プラットフォーム向けのクロスプラット フォームのコマンド ライン インターフェイスです。 この拡張機能では、Azure Machine Learning サービスをコマンドラインから操作するためのコマンドが提供されます。 これにより、機械学習のワークフローを自動化するスクリプトを作成することができます。 たとえば、次のアクションを実行するスクリプトを作成することもできます。

+ 実験を実行して機械学習モデルを作成します

+ 顧客が使用できるように機械学習モデルを登録します

+ 機械学習モデルのライフサイクルをパッケージ化、デプロイ、追跡します

CLI は、Azure Machine Learning SDK に取って代わるものではありません。 高度にパラメーター化された下記のようなタスクを処理するために最適化された、補完的なツールです。

* コンピューティング リソースの作成

* パラメーター化された実験の送信

* モデルの登録

* イメージの作成

* サービスのデプロイ

## <a name="prerequisites"></a>前提条件


* CLI を使用するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](http://aka.ms/AMLFree) を今日からお試しいただけます。

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

## <a name="install-the-extension"></a>拡張機能のインストール

Machine Learning CLI 拡張機能をインストールするには、次のコマンドを使用します。

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-1.0.2-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

メッセージが表示されたら、`y` を選択して拡張機能をインストールします。

拡張機能がインストールされたことを確認するには、次のコマンドを使用して、ML 固有のサブコマンドの一覧を表示します。

```azurecli-interactive
az ml -h
```

> [!TIP]
> 拡張機能を更新するには、拡張機能を__削除__し、その後__インストール__する必要があります。 これにより、最新バージョンがインストールされます。

## <a name="remove-the-extension"></a>拡張機能を削除する

CLI 拡張機能を削除するには、次のコマンドを使用します。

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>リソース管理

次のコマンドは、CLI を使用して、Azure Machine Learning によって使用されるリソースを管理する方法を示したものです。


+ Azure Machine Learning サービスのワークスペースを作成する:

    ```azurecli-interactive
    az ml workspace create -n myworkspace -g myresourcegroup
    ```

+ 既定のワークスペースを作成する:

    ```azurecli-interactive
    az configure --defaults aml_workspace=myworkspace group=myresourcegroup
    ```

+ 分散トレーニング用のマネージド コンピューティング ターゲットを作成する:

    ```azurecli-interactive
    az ml computetarget create amlcompute -n mycompute --max_nodes 4 --size Standard_NC6
    ```

* マネージド コンピューティング ターゲットを更新する:

    ```azurecli-interactive
    az ml computetarget update --name mycompute --workspace –-group --max_nodes 4 --min_nodes 2 --idle_time 300
    ```

* トレーニングまたはデプロイ用のアンマネージド コンピューティング ターゲットを接続する:

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a name="experiments"></a>実験

次のコマンドは、CLI を使用して実験を操作する方法を示したものです。

* 実験を送信する前に、プロジェクト (実行構成) をアタッチする:

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* 実験の実行を開始する。 このコマンドを使用する際には、コンピューティング ターゲットを指定します。 この例では、`local` でローカル コンピューターを使用し、`train.py` スクリプトを使用してモデルをトレーニングしています。

    ```azurecli-interactive
    az ml run submit -c local train.py
    ```

* 送信された実験の一覧を表示する:

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>モデルの登録、イメージの作成およびデプロイ

次のコマンドは、トレーニング済みモデルを登録し、それを運用サービスとしてデプロイする方法を示したものです。

+ Azure Machine Learning にモデルを登録する:

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ 機械学習モデルと依存関係を格納したイメージを作成する: 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ コンピューティング ターゲットにイメージをデプロイする:

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
