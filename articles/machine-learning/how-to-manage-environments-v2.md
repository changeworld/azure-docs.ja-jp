---
title: CLI (v2) を使用した Azure Machine Learning 環境の管理
titleSuffix: Azure Machine Learning
description: Machine Learning に Azure CLI 拡張機能を使用して Azure ML 環境を管理する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 2c88c8954e334b5c6ffc80a60b2bd85cd98bda7a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055533"
---
# <a name="manage-azure-machine-learning-environments-with-the-cli-v2-preview"></a>CLI (v2) を使用した Azure Machine Learning 環境の管理 (プレビュー)

Azure Machine Learning 環境は、ジョブまたはデプロイの実行環境を定義し、コードの依存関係をカプセル化します。 Azure ML は、環境仕様を使用して、指定されたコンピューティング ターゲットでトレーニング コードまたはスコアリング コードを実行する Docker コンテナーを作成します。 Conda 仕様、Docker イメージ、または Docker ビルド コンテキストから環境を定義できます。

この記事では、CLI (v2) を使用して Azure ML 環境を作成し、管理する方法について説明します。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件

- CLI を使用するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) を今すぐお試しください。
- [Machine Learning 用の Azure CLI 拡張機能をインストールして設定する](how-to-configure-cli.md)

> [!TIP]
> 完全な機能を備えた開発環境の場合は、Visual Studio Code と [Azure Machine Learning 拡張機能](how-to-setup-vs-code.md)を使用して、[Azure Machine Learning リソースを管理](how-to-manage-resources-vscode.md)し、[機械学習モデルをトレーニング](tutorial-train-deploy-image-classification-model-vscode.md)します。

### <a name="clone-examples-repository"></a>examples リポジトリを複製する

トレーニング例を実行するには、最初に examples リポジトリを複製し、`cli` ディレクトリに変更します。

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="git_clone":::

なお、`--depth 1` では、リポジトリに対する最新のコミットだけが複製されるので、操作の完了にかかる時間を短縮できます。

## <a name="curated-environments"></a>キュレーションされた環境

Azure ML には、キュレーションされた環境とカスタム環境という 2 種類の環境があります。 キュレーションされた環境は、一般的な ML フレームワークとツールが含まれた、定義済みの環境です。 カスタム環境は、ユーザー定義の環境であり、`az ml environment create` を使用して作成できます。

キュレーションされた環境は Azure ML から提供され、既定でお使いのワークスペースで利用できます。 Azure ML は、これらの環境を最新のフレームワーク バージョンのリリースで定期的に更新し、バグの修正とセキュリティ修正プログラムを行うために保持します。 また、キャッシュされた Docker イメージによってサポートされるため、ジョブの準備に要するコストとモデルのデプロイに要する時間が短縮されます。

キュレーションされたこれらの環境は、`azureml:<curated-environment-name>:<version>` 構文を使用する特定の環境を参照することで、トレーニングやデプロイにすぐに使用できます。 また、キュレーションされたこれらの環境をサポートする Dockerfile を変更することで、独自のカスタム環境のリファレンスとして使用することもできます。

使用可能な一連のキュレーションされた環境は、Azure ML スタジオの UI で確認することも、`az ml environments list` で CLI (v2) を使用して確認することもできます。

## <a name="create-an-environment"></a>環境の作成

Conda 仕様、Docker イメージ、または Docker ビルド コンテキストから環境を定義できます。 YAML 使用ファイルを使用して環境を構成し、次の CLI コマンドを使用して環境を作成します。

```cli
az ml environment create --file my_environment.yml
```

Azure ML 環境の YAML リファレンス ドキュメントについては、[CLI (v2) 環境 YAML スキーマ](reference-yaml-environment.md)を参照してください。

### <a name="create-an-environment-from-a-docker-image"></a>Docker イメージから環境を作成する

Docker イメージから環境を定義するには、Docker Hub や Azure Container Registry などのレジストリでホストされているイメージのイメージ URI を指定します。 

Docker イメージから定義された環境の YAML 仕様ファイルの例を以下に示します。 Docker Hub のオフィシャル PyTorch リポジトリにあるイメージは、YAML ファイルの `image` プロパティを使用して指定されます。

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image.yml":::

環境を作成するには:

```cli
az ml environment create --file assets/environment/docker-image.yml
```

> [!TIP]
> Azure ML は、共通のシステム依存関係を使用して、CPU と GPU Ubuntu Linux ベースの基本イメージを保持します。 たとえば、GPU イメージには Miniconda、OpenMPI、CUDA、cuDNN、NCCL が含まれています。 ご使用の環境に合わせてこれらのイメージを使用することも、独自のカスタム イメージをビルドするときに、対応する Dockerfile を参考として使用することもできます。
>  
> 一連の基本イメージと、それに対応する Dockerfile については、[AzureML コンテナー リポジトリ](https://github.com/Azure/AzureML-Containers)を参照してください。

### <a name="create-an-environment-from-a-docker-build-context"></a>Docker ビルド コンテキストから環境を作成する

ビルド済みイメージから環境を定義する代わりに、 Docker [ビルド コンテキスト](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#understand-build-context)から環境を定義することもできます。 これを行うには、ビルド コンテキストとして機能するディレクトリを指定します。 このディレクトリには、Dockerfile と、イメージをビルドするために必要なその他のファイルが含まれている必要があります。

ビルド コンテキストから定義された環境の YAML 仕様ファイルの例を以下に示します。 ビルド コンテキスト フォルダーへのローカル パスが `build.local_path` フィールドに指定され、そのビルド コンテキスト フォルダー内の Dockerfile への相対パスが `build.dockerfile_path` フィールドに指定されます。 YAML ファイルで `build.dockerfile_path` が省略された場合、Azure ML はビルド コンテキストのルートで `Dockerfile` という名前の Dockerfile を検索します。

この例では、ビルド コンテキストに `Dockerfile` という名前の Dockerfile と、Python パッケージをインストールするために Dockerfile 内で参照される `requirements.txt` ファイルが含まれています。

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-context.yml":::

環境を作成するには:

```cli
az ml environment create --file assets/environment/docker-context.yml
```

Azure ML は、環境が作成されるときに、ビルド コンテキストからイメージのビルドを開始します。 studio UI でビルドの状態を監視し、ビルド ログを確認できます。

### <a name="create-an-environment-from-a-conda-specification"></a>Conda 仕様から環境を作成する

Conda 環境の依存関係を含む標準の conda YAML 構成ファイルを使用して環境を定義できます。 この標準形式の詳細については、[環境の手動作成](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually)に関するセクションを参照してください。

この環境では、Docker 基本イメージも指定する必要があります。 Azure ML は、提供されている Docker イメージを基盤にして conda 環境をビルドします。

conda 仕様から定義された環境の YAML 仕様ファイルの例を以下に示します。 ここでは、Azure ML environment yaml ファイルから conda ファイルへの相対パスが、`conda_file` プロパティを使用して指定されています。 また、別のファイルで定義するのではなく、`conda_file` プロパティを使用して conda 仕様をインラインで定義することもできます。

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image-plus-conda.yml":::

環境を作成するには:

```cli
az ml environment create --file assets/environment/docker-image-plus-conda.yml
```

環境がジョブまたはデプロイで使用されている場合、Azure ML はこの環境仕様から最終的な Docker イメージをビルドします。 また、スタジオ UI で環境のビルドを手動でトリガーすることもできます。

## <a name="manage-environments"></a>環境を管理する

CLI (v2) では、Azure ML 環境アセットのライフサイクルを管理するための一連のコマンドが、`az ml environment` に用意されています。

### <a name="list"></a>List

ワークスペース内のすべての環境を一覧表示します。

```cli
az ml environment list
```

指定された名前のすべての環境バージョンを一覧表示します。

```cli
az ml environment list --name docker-image-example
```

### <a name="show"></a>表示

特定の環境の詳細を取得します。

```cli
az ml environment list --name docker-image-example --version 1
```

### <a name="update"></a>更新

特定の環境の変更可能なプロパティを更新します。

```cli
az ml environment update --name docker-image-example --version 1 --set description="This is an updated description."
```

> [!IMPORTANT]
> 環境については、`description` と `tags` のみを更新できます。 その他のすべてのプロパティは変更できません。これらのプロパティを変更する必要がある場合は、新しいバージョンの環境を作成する必要があります。

### <a name="delete"></a>削除

特定の環境を削除します。

```cli
az ml environment delete --name docker-image-example --version 1
```

## <a name="use-environments-for-training"></a>トレーニングに環境を使用する

トレーニング ジョブに環境を使用するには、ジョブ YAML 構成の `environment` フィールドを指定します。 既存の登録済みの Azure ML 環境を `environment: azureml:<environment-name>:<environment-version>` 経由で参照するか、環境仕様をインラインで定義できます。 環境をインラインで定義する場合は、`name` フィールドと `version` フィールドを指定しないでください。これらの環境は「匿名」環境として扱われ、環境アセット レジストリでは追跡されません。

トレーニング ジョブを送信するときに、新しい環境のビルドに数分かかる場合があります。 その期間は、必要な依存関係のサイズによって異なります。 環境はサービスによってキャッシュされます。 そのため、環境定義が変更されていない限り、完全なセットアップの時間が発生するのは一度だけです。

ジョブでの環境の使用方法の詳細については、[CLI を使用してモデルをトレーニングする (v2)](how-to-train-cli.md)を参照してください。

## <a name="use-environments-for-model-deployments"></a>モデル デプロイに環境を使用する

オンラインとバッチの両方のスコアリング用に、モデルのデプロイに環境を使用することもできます。 これを行うには、デプロイ YAML 構成で `environment` フィールドを指定します。

デプロイでの環境の使用方法の詳細については、[管理されたオンライン エンドポイントを使用した機械学習モデルのデプロイとスコア付け](how-to-deploy-managed-online-endpoints.md)を参照してください。

## <a name="next-steps"></a>次の手順

- [CLI (v2) を使用してモデルをトレーニングする (ジョブを作成する)](how-to-train-cli.md)
- [マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする](how-to-deploy-managed-online-endpoints.md)
- [環境 YAML スキーマ リファレンス](reference-yaml-environment.md)
