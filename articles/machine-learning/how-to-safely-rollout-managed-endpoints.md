---
title: オンライン エンドポイントの安全なロールアウト
titleSuffix: Azure Machine Learning
description: より新しいバージョンの ML モデルを中断なしでロールアウトします。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 7f82c65a2aba8057ab3f7cbc6729b83ed597e12b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564805"
---
# <a name="safe-rollout-for-online-endpoints-preview"></a>オンライン エンドポイントの安全なロールアウト (プレビュー)

運用環境に既存のモデルがデプロイされているとき、新しいバージョンのモデルをデプロイしたいとします。 中断なく新しい ML モデルをロールアウトするにはどうすればよいのでしょうか。 そのようなニーズにちょうど適合するのがブルーグリーン デプロイ、つまり新しいバージョンの Web サービスを運用環境に導入する際に、最初は変更をユーザー (または要求) のごく一部にロールアウトしてから全体にロールアウトする手法です。 この記事では、オンライン エンドポイントを使用していることを前提としています。詳細については、「[Azure Machine Learning エンドポイント (プレビュー) とは](concept-endpoints.md)」を参照してください。

この記事では、次の内容について説明します。

> [!div class="checklist"]
> * バージョン 1 のモデルを提供する "ブルー" と呼ばれる新しいオンライン エンドポイントをデプロイする
> * より多くの要求を処理できるようこのデプロイをスケーリングする
> * ライブ トラフィックを受け付けない "グリーン" と呼ばれるエンドポイントにバージョン 2 のモデルをデプロイする
> * グリーン デプロイを別個にテストする 
> * 10% のライブ トラフィックをグリーン デプロイに送る
> * すべてのライブ トラフィックをグリーン デプロイに完全に切り替える
> * 使用されなくなった v1 ブルー デプロイを削除する

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning を使用するためには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) を今すぐお試しください。

* Azure CLI と ML 拡張機能をインストールして構成する必要があります。 詳細については、[CLI (v2) (プレビュー) のインストール、セットアップ、使用](how-to-configure-cli.md)に関するページを参照してください。 

* Azure リソース グループがあること、また、そのリソース グループにおける `Contributor` アクセス権がご自身 (またはご使用のサービス プリンシパル) に割り当てられていることが必要です。 上記の記事に従って ML 拡張機能を構成していれば、そのようなリソース グループが得られます。 

* Azure Machine Learning ワークスペースが必要です。 上記の記事に従って ML 拡張機能を構成していれば、そのようなワークスペースが得られます。

* まだ Azure CLI の既定値を設定していない場合は、既定の設定を保存する必要があります。 何度も値を渡さずに済むよう、次を実行します。

   ```azurecli
   az account set --subscription <subscription id>
   az configure --defaults workspace=<azureml workspace name> group=<resource group>
   ```

* 既存のオンライン エンドポイントとデプロイ。 この記事では、「[マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md)」の説明に従ってデプロイが作成されていることを前提としています。

* まだ環境変数 $ENDPOINT_NAME の設定が済んでいない場合は、ここで設定してください。

   :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="set_endpoint_name":::

* (推奨) サンプル リポジトリをクローンしてリポジトリの `cli/` ディレクトリに切り替えます。 

   ```azurecli
   git clone https://github.com/Azure/azureml-examples
   cd azureml-examples/cli
   ```

このチュートリアルのコマンドは、`deploy-safe-rollout-online-endpoints.sh` ファイルに存在します。また、YAML 構成ファイルは `endpoints/online/managed/sample/` サブディレクトリに存在します。

## <a name="confirm-your-existing-deployment-is-created"></a>既存のデプロイが作成されていることを確認する

既存のエンドポイントとデプロイの状態は、次を実行して確認できます。 

```azurecli
az ml online-endpoint show --name $ENDPOINT_NAME 

az ml online-deployment show --name blue --endpoint $ENDPOINT_NAME 
```

`$ENDPOINT_NAME` によって識別されるエンドポイントと、`blue` というデプロイが確認できます。 

## <a name="scale-your-existing-deployment-to-handle-more-traffic"></a>より多くのトラフィックを処理できるように既存のデプロイをスケーリングする

「[マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md)」で説明されているデプロイでは、デプロイ yaml ファイルで `instance_count` の値を `1` に設定しました。 `update` コマンドを使用してスケール アウトできます。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="scale_blue" :::

> [!Note]
> 上記のコマンドでは、`--set` を使用してデプロイ構成をオーバーライドします。 または、yaml ファイルを更新し、`--file` 入力を使用して `update` コマンドに入力 として渡します。

## <a name="deploy-a-new-model-but-send-it-no-traffic-yet"></a>新しいモデルをデプロイする (ただし、まだトラフィックは送らない)

`green` という名前の新しいデプロイを作成します。 

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="create_green" :::

green には、どのトラフィックも明示的に割り当てていないので、割り当てられたトラフィックはありません。 次のコマンドを使用して確認できます。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="get_traffic" :::

### <a name="test-the-new-deployment"></a>新しいデプロイをテストする

`green` に割り当てられたトラフィックは 0% ですが、`--deployment` の名前を指定して直接呼び出すことができます。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="test_green" :::

トラフィック ルールを介さずに、REST クライアントを使用して直接デプロイを呼び出したい場合、`azureml-model-deployment: <deployment-name>` という HTTP ヘッダーを設定します。 次のコード スニペットでは、`curl` を使用してデプロイを直接呼び出します。 コード スニペットは、Unix/WSL 環境で動作する必要があります。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="test_green_using_curl" :::

## <a name="test-the-new-deployment-with-a-small-percentage-of-live-traffic"></a>ライブ トラフィックの割合を小さく抑えて新しいデプロイをテストする

`green` デプロイをテストしたら、それにトラフィックのごく一部を割り当てます。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="green_10pct_traffic" :::

これで、`green` デプロイが要求の 10% を受け取るようになります。 

## <a name="send-all-traffic-to-your-new-deployment"></a>すべてのトラフィックを新しいデプロイに送る

`green` デプロイに問題がなければ、すべてのトラフィックをそのデプロイに切り替えます。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="green_100pct_traffic" :::

## <a name="remove-the-old-deployment"></a>以前のデプロイを削除する

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="delete_blue" :::

## <a name="delete-the-endpoint-and-deployment"></a>エンドポイントとデプロイを削除する

デプロイを使用する予定がなければ、次のようにして削除してください。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="delete_endpoint" :::


## <a name="next-steps"></a>次の手順
- [REST を使用してモデルをデプロイする (プレビュー)](how-to-deploy-with-rest.md)
- [スタジオでマネージド オンライン エンドポイント (プレビュー) を作成および使用する](how-to-use-managed-online-endpoint-studio.md)
- [マネージド オンライン エンドポイントとマネージド ID (プレビュー) を使用して Azure リソースにアクセスする](how-to-access-resources-from-endpoints-managed-identities.md)
- [マネージド オンライン エンドポイント (プレビュー) を監視する](how-to-monitor-online-endpoints.md)
- [Azure Machine Learning を使用するリソースのクォータの管理と引き上げ](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)
- [Azure Machine Learning のマネージド オンライン エンドポイント (プレビュー) のコストを表示する](how-to-view-online-endpoints-costs.md)
- [マネージド オンライン エンドポイント SKU の一覧 (プレビュー)](reference-managed-online-endpoints-vm-sku-list.md)
- [マネージド オンライン エンドポイントのデプロイとスコアリングのトラブルシューティング (プレビュー)](how-to-troubleshoot-managed-online-endpoints.md)
- [マネージド オンライン エンドポイント (プレビュー) YAML リファレンス](reference-yaml-endpoint-managed-online.md)
