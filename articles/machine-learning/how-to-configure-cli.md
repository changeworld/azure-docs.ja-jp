---
title: 2\.0 CLI をインストール、設定、および使用する
titleSuffix: Azure Machine Learning
description: Machine Learning 用 Azure CLI 拡張機能をインストール、設定、および使用する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: lostmygithubaccount
ms.author: copeters
ms.date: 05/25/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 53d7b42426919ec5b91821ac67fbde940972494a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745229"
---
# <a name="install-set-up-and-use-the-20-cli-preview"></a>2\.0 CLI (プレビュー) をインストール、設定、および使用する

[Azure CLI](/cli/azure/) に対する `ml` 拡張機能 (プレビュー) は、Azure Machine Learning の拡張インターフェイスです。 これにより、コマンド ラインからモデルをトレーニングおよびデプロイできます。また、モデルのライフサイクルを追跡しながらデータ サイエンスのスケールアップとスケールアウトを加速する機能もあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件

- CLI を使用するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) を今すぐお試しください。
- ご使用の **ローカル環境** からこのドキュメントの CLI コマンドを使用するには、[Azure CLI](/cli/azure/install-azure-cli) が必要です。

    > [!TIP]
    > [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) を使用する場合は、ブラウザーを使用してクラウド内に存在する CLI にアクセスします。

## <a name="installation"></a>インストール

新しい Machine Learning 拡張機能には、**Azure CLI バージョン `>=2.15.0` が必要です**。 この要件が満たされていることを確認します。

```azurecli
az version
```

そうでない場合は、[Azure CLI をアップグレード](/cli/azure/update-azure-cli)します。

インストールされた Azure CLI 拡張機能を確認します。

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_list":::

`azure-cli-ml` 拡張機能など、`ml` 名前空間を使用する競合する拡張機能がインストールされていないことを確認します。

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_remove":::

ここで、`ml` 拡張機能をインストールします。

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_ml_install":::

help コマンドを実行してインストールを確認し、使用可能なサブコマンドを調べます。

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_ml_verify":::

拡張機能を最新バージョンにアップグレードすることができます。

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_ml_update":::

### <a name="installation-on-linux"></a>Linux へのインストール

Linux を使用している場合、必要な CLI バージョンと Machine Learning 拡張機能をインストールする最も簡単な方法は次のとおりです。

:::code language="bash" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_install_linux":::

詳しくは、[Linux 向け Azure CLI のインストールに関するページ](/cli/azure/install-azure-cli-linux)を参照してください。

## <a name="set-up"></a>設定

ログイン: 

```azurecli
az login
```

複数の Azure サブスクリプションにアクセスできる場合は、アクティブなサブスクリプションを設定できます。

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_account_set":::

まだ存在していない場合は、Azure リソース グループを作成できます。

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_group_create":::

機械学習サブコマンドには、`--workspace/-w` および `--resource-group/-g` パラメーターが必要です。 これらを繰り返し入力しなくて済むようにするには、既定値を構成します。

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_configure_defaults":::

> [!TIP]
> ほとんどのコード例では、既定のワークスペースとリソース グループが設定されていることを前提としています。 これらは、コマンド ラインでオーバーライドできます。

次に機械学習ワークスペースを作成します。

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_ml_workspace_create":::

## <a name="hello-world"></a>Hello world

続いて、サンプル リポジトリを複製し、`cli` サブディレクトリに移動します。

```azurecli-interactive
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples/cli
```

Python を使用してローカルで hello world を実行するには、`jobs` サブディレクトリの例を参照してください。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/hello-world.yml":::

> [!IMPORTANT]
> [Docker](https://docker.io) をローカルにインストールして実行する必要があります。

ジョブを送信して、ログをコンソール出力にストリーミングし、Azure Machine Learning スタジオで実行を開きます。

:::code language="azurecli" source="~/azureml-examples-main/cli/hello-world.sh" id="hello_world":::

> [!IMPORTANT]
> Docker イメージがローカルにプルされ、Azure ML ジョブが実行されるため、初回の実行には数分かかる場合があります。 後続の実行では、イメージがローカルにキャッシュされ、より迅速に完了します。

## <a name="next-steps"></a>次の手順

- [Machine Learning CLI 拡張機能 (プレビュー) を使用してモデルをトレーニングする](how-to-train-cli.md)
- [Visual Studio Code Azure Machine Learning 拡張機能をセットアップする](how-to-setup-vs-code.md)
- [Azure Machine Learning Visual Studio Code 拡張機能を使用して画像分類 TensorFlow モデルをトレーニングする](tutorial-train-deploy-image-classification-model-vscode.md)
