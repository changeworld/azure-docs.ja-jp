---
title: CLI (v2) のインストールと設定
titleSuffix: Azure Machine Learning
description: Machine Learning 用の Azure CLI 拡張機能をインストールして設定する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: lostmygithubaccount
ms.author: copeters
ms.date: 05/25/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 8034b9e6027f85d9dd5385fda18f81523830944f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772710"
---
# <a name="install-and-set-up-the-cli-v2"></a>CLI (v2) のインストールと設定

[Azure CLI](/cli/azure/) に対する `ml` 拡張機能 (プレビュー) は、Azure Machine Learning の拡張インターフェイスです。 これにより、コマンド ラインからモデルをトレーニングおよびデプロイできます。また、モデルのライフサイクルを追跡しながらデータ サイエンスのスケールアップとスケールアウトを加速する機能もあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件

- CLI を使用するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) を今すぐお試しください。
- ご使用の **ローカル環境** からこのドキュメントの CLI コマンドを使用するには、[Azure CLI](/cli/azure/install-azure-cli) が必要です。

## <a name="installation"></a>インストール

新しい Machine Learning 拡張機能には、**Azure CLI バージョン `>=2.15.0` が必要です**。 この要件が満たされていることを確認します。

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_version":::

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

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_login":::

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

## <a name="next-steps"></a>次の手順

- [Machine Learning CLI 拡張機能 (プレビュー) を使用してモデルをトレーニングする](how-to-train-cli.md)
- [Visual Studio Code Azure Machine Learning 拡張機能をセットアップする](how-to-setup-vs-code.md)
- [Azure Machine Learning Visual Studio Code 拡張機能を使用して画像分類 TensorFlow モデルをトレーニングする](tutorial-train-deploy-image-classification-model-vscode.md)
