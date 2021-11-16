---
title: マネージド オンライン エンドポイントを使用して AutoML モデルをデプロイする (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure によって自動的に管理される Web サービスとして AutoML モデルをデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
ms.reviewer: laobri
author: shivanissambare
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 2a2c04795b3cfcf38e3a2b84ccc36c0ee488f5f6
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059395"
---
# <a name="how-to-deploy-an-automl-model-to-an-online-endpoint-preview"></a>AutoML モデルをオンライン エンドポイントにデプロイする方法 (プレビュー)

この記事では、AutoML でトレーニングされた機械学習モデルをオンライン エンドポイントにデプロイする方法について説明します。 自動機械学習 (自動 ML または AutoML とも呼ばれます) は、機械学習モデルを開発する時間のかかる反復的なタスクを自動化するプロセスです。 詳細については、「[自動機械学習 (AutoML) とは](concept-automated-ml.md)」を参照してください。

この記事では、次に示すものを使用して、AutoML でトレーニングされた機械学習モデルをオンライン エンドポイントにデプロイする方法について説明します。 

- Azure Machine Learning Studio
- Azure Machine Learning CLI 2.0

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>必須コンポーネント

AutoML でトレーニングされた機械学習モデル。 詳細については、「[チュートリアル: Azure Machine Learning スタジオでコードなし AutoML を使用して分類モデルをトレーニングする](tutorial-first-experiment-automated-ml.md)」または「[チュートリアル: 自動機械学習を使用して需要を予測する](tutorial-automated-ml-forecast.md)」を参照してください。

## <a name="deploy-from-azure-machine-learning-studio-and-no-code"></a>Azure Machine Learning スタジオからのデプロイとコードなし

[自動 ML] ページからの AutoML でトレーニングされたモデルのデプロイは、コードなしの操作です。 つまり、スコアリング スクリプトと環境は、どちらも自動的に生成されるので、用意する必要はありません。 

1. スタジオの [自動 ML] ページにアクセスします
1. 実験を選択して実行します
1. [モデル] タブを選択します
1. デプロイするモデルを選択します 
1. モデルを選択すると、[デプロイ] ボタンがドロップダウン メニューに表示されます
1. *[リアルタイム エンドポイントへのデプロイ (プレビュー)]* オプションを選択します

   :::image type="content" source="media/how-to-deploy-automl-endpoint/deploy-button.png" lightbox="media/how-to-deploy-automl-endpoint/deploy-button.png" alt-text="[デプロイ] ボタンのドロップダウン メニューを示すスクリーンショット":::

   システムによって、デプロイに必要なモデルと環境が生成されます。 

   :::image type="content" source="media/how-to-deploy-automl-endpoint/model.png" lightbox="media/how-to-deploy-automl-endpoint/model.png" alt-text="生成されたモデルを示すスクリーンショット":::

   :::image type="content" source="media/how-to-deploy-automl-endpoint/environment.png" lightbox="media/how-to-deploy-automl-endpoint/environment.png" alt-text="生成された環境を示すスクリーンショット":::

5. ウィザードを完了して、モデルをリアルタイム エンドポイントにデプロイします

 :::image type="content" source="media/how-to-deploy-automl-endpoint/complete-wizard.png" lightbox="media/how-to-deploy-automl-endpoint/complete-wizard.png"  alt-text="[確認と作成] ページを示すスクリーンショット":::


## <a name="deploy-manually-from-the-studio-or-command-line"></a>スタジオまたはコマンド ラインから手動でデプロイする

デプロイをより細かくコントロールする場合は、トレーニング アーティファクトをダウンロードしてデプロイできます。 

デプロイに必要なコンポーネントをダウンロードするには、次のようにします。

1. 自動 ML 実験にアクセスして、Machine Learning ワークスペースで実行します
1. [モデル] タブを選択します
1. 使用するモデルを選択します。 モデルを選択すると、 *[ダウンロード]* ボタンが有効になります。
1. *[ダウンロード]* を選択します

:::image type="content" source="media/how-to-deploy-automl-endpoint/download-model.png" lightbox="media/how-to-deploy-automl-endpoint/download-model.png" alt-text="[モデル] と [ダウンロード] ボタンの選択を示すスクリーンショット":::

次のものを含む zip ファイルを受け取ります。
* `conda_env_<VERSION>.yml` という名前の Conda 環境仕様のファイル
* `scoring_file_<VERSION>.py` という名前の Python スコアリング ファイル
* `model.pkl` という名前の Python `.pkl` ファイルにあるモデル自体

これらのファイルを使用してデプロイする場合は、スタジオまたは Azure コマンド ライン インターフェイスを使用できます。

# <a name="studio"></a><bpt id="p1">[</bpt>スタジオ<ept id="p1">](#tab/azure-studio)</ept>

1. Azure Machine Learning スタジオの [モデル] ページにアクセスします

1. [+ モデルの登録] オプションをクリックします

1. 自動 ML 実行からダウンロードしたモデルを登録します

1. [環境] ページにアクセスし、[カスタム環境] を選択し、[+ 作成] オプションを選択してデプロイ用の環境を作成します。 ダウンロードした Conda yaml を使用してカスタム環境を作成します。

1. モデルを選択し、[デプロイ] ドロップダウン オプションから [リアルタイム エンドポイントへのデプロイ] を選択します

1. ウィザードのすべての手順を完了して、オンライン エンドポイントとデプロイを作成します

 
# <a name="cli"></a>[CLI](#tab/CLI)

## <a name="configure-the-cli"></a>CLI の構成 

CLI からデプロイを作成するには、ML v2 拡張機能を持つ Azure CLI が必要です。 次のコマンドを実行して、両方があることを確認します。

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_version":::

エラー メッセージが表示された場合、または応答に `Extensions: ml` が表示されない場合は、「[CLI のインストールと設定 (v2)](how-to-configure-cli.md)」の手順に従ってください。

ログイン: 

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_login":::

複数の Azure サブスクリプションにアクセスできる場合は、アクティブなサブスクリプションを設定できます。

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_account_set":::

デプロイを作成する場所に、既定のリソース グループとワークスペースをセットします。

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_configure_defaults":::

## <a name="put-the-scoring-file-in-its-own-directory"></a>スコアリング ファイルを独自のディレクトリに配置する

`src/` という名前のディレクトリを作成し、ダウンロードしたスコアリング ファイルを配置します。 このディレクトリは Azure にアップロードされ、推論を行うために必要なすべてのソース コードが含まれています。 AutoML モデルの場合、スコアリング ファイルは 1 つだけです。 

## <a name="create-the-endpoint-and-deployment-yaml-file"></a>エンドポイントおよびデプロイ yaml ファイルを作成する

コマンド ラインから管理対象のオンライン エンドポイントを作成するには、*endpoint.yml* と *deployment.yml* ファイルを作成する必要があります。 [Azure Machine Learning の例リポジトリ](https://github.com/Azure/azureml-examples)から取得した次のコードは、必要なすべての入力をキャプチャする、_エンドポイント/オンライン/マネージド/サンプル/_ を示しています。

__automl_endpoint.yml__

::: code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/endpoint.yml" :::

__automl_deployment.yml__

::: code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/blue-deployment.yml" :::

[AutoML モデル] ページからダウンロードしたファイルを使用するには、このファイルを変更する必要があります。

1. `automl_endpoint.yml` と `automl_deployment.yml` のファイルを作成し、上記の例のコンテンツを貼り付けます。

1. エンドポイントの `name` の値を変更します。 エンドポイント名は、Azure リージョン内で一意である必要があります。 エンドポイント名は、大文字または小文字で始まり、'-' と英数字のみで構成されている必要があります。

1. `automl_deployment` のファイルで、以下のパスにあるキーの値を変更します。

    | パス | 変更後の値 |
    | --- | --- |
    | `model:local_path` | ダウンロードした `model.pkl` ファイルへのパス。 |
    | `code_configuration:code:local_path` | スコアリング ファイルを配置したディレクトリ。 | 
    | `code_configuration:scoring_script` | Python スコアリング ファイル (`scoring_file_<VERSION>.py`) の名前。 |
    | `environment:conda_file` | ダウンロードされた Conda 環境ファイル (`conda_env_<VERSION>.yml`) のファイル URL。 |

    > [!NOTE]
    > YAML の完全な説明については、[マネージド オンライン エンドポイント (プレビュー) YAML リファレンス](reference-yaml-endpoint-managed-online.md)に関するページを参照してください。

3. コマンド ラインから次を実行します。 

    ```azurecli
    az ml online-endpoint create -f automl_endpoint.yml
    az ml online-deployment create -f automl_deployment.yml
    ```
    
---

デプロイを作成したら、「[モデルを使用してデータをスコアリングするエンドポイントの呼び出し](how-to-deploy-managed-online-endpoints.md#invoke-the-endpoint-to-score-data-by-using-your-model)」で説明されているようにスコアリングできます。

## <a name="next-steps"></a>次の手順

- [マネージド オンライン エンドポイントのデプロイのトラブルシューティング](how-to-troubleshoot-managed-online-endpoints.md)
- [オンライン エンドポイントの安全なロールアウト](how-to-safely-rollout-managed-endpoints.md)
