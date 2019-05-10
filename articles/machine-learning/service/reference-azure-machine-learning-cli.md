---
title: Machine Learning CLI 拡張機能
titleSuffix: Azure Machine Learning service
description: Azure CLI 用の Azure Machine Learning CLI 拡張機能について説明します。 Azure CLI は、Azure クラウド上のリソースを操作できるようにするための、クロスプラットフォームのコマンド ライン ユーティリティです。 Machine Learning 拡張機能を使用すると、Azure Machine Learning サービスを操作することができます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 9cc6ad4f7b33de4d132efe63ff11c34f10b614af
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023380"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Azure Machine Learning service 用 CLI 拡張機能の使用

Azure Machine Learning CLI は [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) への拡張機能であり、Azure プラットフォーム向けのクロスプラット フォームのコマンド ライン インターフェイスです。 この拡張機能では、Azure Machine Learning サービスをコマンドラインから操作するためのコマンドが提供されます。 それにより、機械学習のワークフローを自動化することができます。 たとえば、次のアクションを実行できます。

+ 実験を実行して機械学習モデルを作成します

+ 顧客が使用できるように機械学習モデルを登録します

+ 機械学習モデルのライフサイクルをパッケージ化、デプロイ、追跡します

CLI は、Azure Machine Learning SDK に取って代わるものではありません。 自動化に適合するために高度にパラメーター化されたタスクを処理するように最適化された補完的ツールです。

## <a name="prerequisites"></a>前提条件

* CLI を使用するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

## <a name="install-the-extension"></a>拡張機能のインストール

Machine Learning CLI 拡張機能をインストールするには、次のコマンドを使用します。

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 以下のコマンドで使用できるサンプル ファイルは、[こちら](http://aka.ms/azml-deploy-cloud)にあります。

メッセージが表示されたら、`y` を選択して拡張機能をインストールします。

拡張機能がインストールされたことを確認するには、次のコマンドを使用して、ML 固有のサブコマンドの一覧を表示します。

```azurecli-interactive
az ml -h
```

## <a name="remove-the-extension"></a>拡張機能を削除する

CLI 拡張機能を削除するには、次のコマンドを使用します。

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>リソース管理

次のコマンドは、CLI を使用して、Azure Machine Learning によって使用されるリソースを管理する方法を示したものです。

+ リソース グループがまだない場合は、次のように指定してリソース グループを作成します。

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Azure Machine Learning サービスのワークスペースを作成する:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

+ ワークスペース構成をフォルダーに接続して、CLI コンテキストの認識を有効にします。
    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

+ Azure BLOB コンテナーをデータストアとして接続します。

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```
    
+ AKS クラスターをコンピューティング先として接続します。

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a id="experiments"></a>実験の実行

* 実験の実行を開始する。 このコマンドを使用する場合は、-c パラメーターに対して runconfig ファイルの名前 (ファイル システムが表示されている場合、\*.runconfig の前のテキスト) を指定します。

    ```azurecli-interactive
    az ml run submit-script -c local -e testexperiment train.py
    ```

* 次のように指定して、実験の一覧を表示します。

    ```azurecli-interactive
    az ml experiment list
    ```

## <a name="model-registration-profiling--deployment"></a>モデルの登録、プロファイリング、デプロイ

次のコマンドは、トレーニング済みモデルを登録し、それを運用サービスとしてデプロイする方法を示したものです。

+ Azure Machine Learning にモデルを登録する:

  ```azurecli-interactive
  az ml model register -n mymodel -p sklearn_regression_model.pkl
  ```

+ モデルを AKS にデプロイします｡

  ```azurecli-interactive
  az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
  ```
