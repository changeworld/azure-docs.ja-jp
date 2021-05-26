---
title: オンライン エンドポイントの安全なロールアウト
titleSuffix: Azure Machine Learning
description: より新しいバージョンの ML モデルを中断なしでロールアウトします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 05/25/2021
ms.topic: how-to
ms.custom: how-to
ms.openlocfilehash: 61754eec2c866a7bf5897b2faa2a2b2ae7b60d02
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382859"
---
# <a name="safe-rollout-for-online-endpoints-preview"></a>オンライン エンドポイントの安全なロールアウト (プレビュー)

運用環境に既存のモデルがデプロイされているとき、新しいバージョンのモデルをデプロイしたいとします。 中断なく新しい ML モデルをロールアウトするにはどうすればよいのでしょうか。 そのようなニーズにちょうど適合するのがブルーグリーン デプロイ、つまり新しいバージョンの Web サービスを運用環境に導入する際に、最初は変更をユーザー (または要求) のごく一部にロールアウトしてから全体にロールアウトする手法です。 

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

* Azure Machine Learning を使用するためには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* Azure CLI と ML 拡張機能をインストールして構成する必要があります。 詳細については、[2.0 CLI (プレビュー) のインストール、セットアップ、使用](how-to-configure-cli.md)に関するページを参照してください。 

* Azure リソース グループがあること、また、そのリソース グループにおける `Contributor` アクセス権がご自身 (またはご使用のサービス プリンシパル) に割り当てられていることが必要です。 上記の記事に従って ML 拡張機能を構成していれば、そのようなリソース グループが得られます。 

* Azure Machine Learning ワークスペースが必要です。 上記の記事に従って ML 拡張機能を構成していれば、そのようなワークスペースが得られます。

* まだ Azure CLI の既定値を設定していない場合は、既定の設定を保存する必要があります。 何度も値を渡さずに済むよう、次を実行します。

```azurecli
az account set --subscription <subscription id>
az configure --defaults workspace=<azureml workspace name> group=<resource group>
```

* 既存のマネージド エンドポイント。 この記事では、「[マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md)」の説明に従ってデプロイが作成されていることを前提としています。

* まだ環境変数 $ENDPOINT_NAME の設定が済んでいない場合は、ここで設定してください。

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="set_endpoint_name":::

* (推奨) サンプル リポジトリをクローンしてリポジトリの `cli/` ディレクトリに切り替えます。 

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples/cli
```

このチュートリアルのコマンドは、`how-to-deploy-declarative-safe-rollout-online-endpoints.sh` ファイルに存在します。また、YAML 構成ファイルは `endpoints/online/managed/canary-declarative-flow/` サブディレクトリに存在します。

## <a name="confirm-your-existing-deployment-is-created"></a>既存のデプロイが作成されていることを確認する

既存のデプロイの状態は、次を実行して確認できます。 

```azurecli
az ml endpoint show --name $ENDPOINT_NAME 
```

`$ENDPOINT_NAME` によって識別されるエンドポイントと、`blue` というデプロイが確認できます。 

## <a name="scale-your-existing-deployment-to-handle-more-traffic"></a>より多くのトラフィックを処理できるように既存のデプロイをスケーリングする

「[マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md)」で説明されているデプロイでは、`instance_count` の値を `1` に設定しました。 処理できるトラフィック量を増やすため、2 つ目のバージョンの YAML ファイル (`2-scale-blue.yml`) では、この値を `2` に設定します。

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/2-scale-blue.yml" range="29":::

デプロイを次の内容で更新します。

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="scale_blue" :::

> [!IMPORTANT]
> YAML を使用した更新は宣言型です。 つまり YAML の変更は、基になる Azure Resource Manager リソース (エンドポイントとデプロイ) に反映されます。 このアプローチによって [GitOps](https://www.atlassian.com/git/tutorials/gitops) が促進されます。つまり、エンドポイントまたはデプロイに対する "*すべて*" の変更は YAML を経由することになります (`instance_count` も含む)。 副作用として、YAML からデプロイを削除し、そのファイルを使用して `az ml endpoint update` を実行した場合、そのデプロイは削除されます。 

## <a name="deploy-a-new-model-but-send-it-no-traffic-yet"></a>新しいモデルをデプロイする (ただし、まだトラフィックは送らない)

新しいモデルをデプロイするために、構成ファイルの `deployments` セクションに新しいセクションを追加します。ただし `traffic` セクションには、受け取るトラフィック量を 0% として指定します。 `3-create-green.yml` ファイルには、この変更が含まれます。

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/3-create-green.yml" range="7,35-56":::

デプロイを更新します。 

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="create_green" :::

### <a name="test-the-new-deployment"></a>新しいデプロイをテストする

この構成では、先ほど作成した `green` デプロイに対するトラフィックを 0% として指定しました。 これは、`--deployment` の名前を指定して直接呼び出すことでテストできます。

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="test_green" :::

トラフィック ルールを介さずに、REST クライアントを使用して直接デプロイを呼び出したい場合、`azureml-model-deployment: <deployment-name>` という HTTP ヘッダーを設定します。

## <a name="test-the-new-deployment-with-a-small-percentage-of-live-traffic"></a>ライブ トラフィックの割合を小さく抑えて新しいデプロイをテストする

`4-flight-green.yml` ファイルは、`green` デプロイのテスト後、構成ファイルの `traffic` 構成を変更することで、何割かのトラフィックを処理する方法を示しています。

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/4-flight-green.yml" range="5-7":::

強調表示されている行を除いて、構成ファイルは変更されていません。 デプロイを次の内容で更新します。

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="green_10pct_traffic" :::

これで、`green` デプロイが要求の 10% を受け取るようになります。 

## <a name="send-all-traffic-to-your-new-deployment"></a>すべてのトラフィックを新しいデプロイに送る

`green` デプロイに問題がなければ、すべてのトラフィックをそのデプロイに切り替えます。 次のスニペットが示しているのは、構成ファイル内の該当するコードだけです。それ以外のコードは変更していません。

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/5-full-green.yml" range="5-7":::

さらに、デプロイを更新します。 

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="green_100pct_traffic" :::

## <a name="remove-the-old-deployment"></a>以前のデプロイを削除する

以前の `blue` デプロイを削除して、新しいモデルへの切り替えを完了します。 最終的な構成ファイルは次のようになります。

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/6-delete-blue.yml" :::

デプロイを次の内容で更新します。

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="delete_blue" :::

## <a name="delete-the-endpoint-and-deployment"></a>エンドポイントとデプロイを削除する

デプロイを使用する予定がなければ、次のようにして削除してください。

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="delete_endpoint" :::
