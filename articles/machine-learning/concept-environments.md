---
title: Azure Machine Learning 環境について
titleSuffix: Azure Machine Learning
description: さまざまなコンピューティング先での再現可能、監査可能、移植可能な機械学習の依存関係定義を有効にする機械学習環境について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 09/23/2021
ms.openlocfilehash: e798893b28eaaa6aabb1d3f3623aea60df7bc2c6
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576151"
---
# <a name="what-are-azure-machine-learning-environments"></a>Azure Machine Learning 環境とは?

Azure Machine Learning 環境は、機械学習トレーニングが行われる環境をカプセル化したものです。 そこでは、トレーニングとスコアリングのスクリプトに関連する、Python パッケージ、環境変数、およびソフトウェア設定を指定します。 また、ランタイム (Python、Spark、または Docker) も指定します。 環境は、Machine Learning ワークスペース内で管理およびバージョン管理されるエンティティであり、さまざまなコンピューティング先で再現、監査、移植が可能な機械学習ワークフローを実現します。

ローカル コンピューティングで `Environment` オブジェクトを使用して、次のことを行うことができます。
* トレーニング スクリプトを開発します。
* 大規模なモデルのトレーニング用に Azure Machine Learning コンピューティングで同じ環境を再利用します。
* その同じ環境を使ってモデルをデプロイします。
* 既存のモデルがトレーニングされた環境を見直します。

次の図は、実行構成 (トレーニング用) と推論およびデプロイ構成 (Web サービス デプロイ用) の両方で、どのように 1 つの `Environment` オブジェクトを使用できるかを示しています。

![機械学習ワークフローでの環境の図](./media/concept-environments/ml-environment.png)

環境、コンピューティング ターゲット、およびトレーニング スクリプトが一体となって、トレーニング実行の完全な仕様である実行構成を形成します。

## <a name="types-of-environments"></a>環境の種類

環境は、*キュレーション*、*ユーザー管理*、*システム管理* の 3 つのカテゴリに大別できます。

キュレートされた環境は Azure Machine Learning から提供され、既定でお使いのワークスペースで利用できます。 これらには、現状のまま使用する目的で、Python のパッケージと設定のコレクションが含まれていて、さまざまな機械学習フレームワークの使用を開始する助けとなります。 これらの事前に作成された環境を利用すると、デプロイ時間の短縮も可能です。 完全な一覧については、[キュレーションされた環境に関する記事](resource-curated-environments.md)を参照してください。

ユーザー管理環境では、ユーザーが環境を設定し、トレーニング スクリプトで必要なすべてのパッケージをコンピューティング先にインストールする必要があります。 また、モデル デプロイに必要な依存関係を必ず含めるようにしてください。

Python 環境を [Conda](https://conda.io/docs/) で自動的に管理したいときは、システム管理環境を使用します。 新しい Conda 環境は、Docker のベース イメージ上の Conda 仕様から具体化されています。

## <a name="create-and-manage-environments"></a>環境を作成および管理する

AzureML Python SDK、Azure Machine Learning CLI、Azure Machine Learning studio の環境ページ、[VS Code 拡張機能](how-to-manage-resources-vscode.md#create-environment)などのクライアントから環境を作成できます。 各クライアントでは、必要に応じて、ベース イメージ、Dockerfile、および Python レイヤーをカスタマイズできます。

特定のコード サンプルについては、[環境の使用方法](how-to-use-environments.md#create-an-environment)に関するページの「環境の作成」セクションを参照してください。 

環境はワークスペースから簡単に管理することができ、次の操作を実行できます。

* 環境を登録する。
* ワークスペースから環境をフェッチして、トレーニングまたはデプロイに使用する。
* 既存のインスタンスを編集して、環境の新しいインスタンスを作成する。
* 環境へのこれまでの変更を確認し、再現性を確保する。
* 環境から Docker イメージを自動的に構築する。

実験を送信すると、「匿名」環境が自動的にワークスペースに登録されます。 これらは表示されませんが、バージョンごとに取得される可能性があります。

コード例については、[環境の使用方法](how-to-use-environments.md#manage-environments)に関するページの「環境の管理」セクションを参照してください。

## <a name="environment-building-caching-and-reuse"></a>環境のビルド、キャッシュ、再利用

Azure Machine Learning では、Docker イメージと Conda 環境に環境定義が組み込まれます。 また、後続のトレーニング実行とサービス エンドポイントのデプロイで再利用できるように、環境はキャッシュされます。 トレーニング スクリプトをリモートで実行するには、Docker イメージを作成する必要があります。しかし、ローカル実行では、Conda 環境を直接使用できます。 

### <a name="submitting-a-run-using-an-environment"></a>環境を使用して実行を送信する

環境を使用して初めてリモート実行を送信するときには、Azure Machine Learning service によって、ワークスペースに関連付けられている Azure Container Registry (ACR) で [ACR ビルド タスク](../container-registry/container-registry-tasks-overview.md)が呼び出されます。 ビルドされた Docker イメージは、ワークスペースの ACR にキャッシュされます。 キュレーションされた環境は、グローバル ACR にキャッシュされた Docker イメージによって支援されます。 イメージは、実行の開始時に、コンピューティング先によって関連する ACR から取得されます。

ローカルでの実行の場合、環境の定義に基づいて、Docker または Conda 環境が作成されます。 スクリプトはその後、ターゲット コンピューティング (ローカルのランタイム環境またはローカルの Docker エンジン) で実行されます。

### <a name="building-environments-as-docker-images"></a>Docker イメージとしての環境のビルド

ワークスペースの ACR にまだ特定の環境定義のイメージが存在しない場合は、新しいイメージが構築されます。 イメージのビルドは、次の 2 つの手順で構成されます。

 1. 基本イメージのダウンロードと、Docker 手順の実行
 2. 環境定義で指定されている Conda の依存関係に従った Conda 環境の構築

[ユーザーが管理する依存関係](/python/api/azureml-core/azureml.core.environment.pythonsection)が指定された場合、2 番目の手順は省略されます。 この場合、ユーザーがすべての Python パッケージをインストールする必要があります。それには、Python パッケージを基本イメージに含めるか、最初の手順でカスタム Docker 手順を指定します。 また、ユーザーは Python 実行可能ファイルの正しい場所を指定する必要もあります。 [カスタム Docker 基本イメージ](./how-to-deploy-custom-container.md)を使用することも可能です。

### <a name="image-caching-and-reuse"></a>イメージのキャッシュと再利用

別の実行に同じ環境定義を使用する場合、Azure Machine Learning では、時間を節約するためにワークスペースの ACR にキャッシュされているイメージが再利用されます。

キャッシュされたイメージの詳細を表示するには、Azure Machine Learning studio の環境ページを確認するか、[`Environment.get_image_details`](/python/api/azureml-core/azureml.core.environment.environment#get-image-details-workspace-) メソッドを使用します。

キャッシュされたイメージを再利用するか、新しいイメージを作成するかを決定するため、AzureML では、環境定義から[ハッシュ値](https://en.wikipedia.org/wiki/Hash_table)が計算されて、それが既存の環境のハッシュと比較されます。 ハッシュは、環境定義に基づいています。
 
 * TestVM
 * カスタム Docker イメージ
 * Python パッケージ
 * Spark パッケージ

ハッシュは、環境名またはバージョンの影響を受けません。 環境の名前を変更したり、別の環境と同じ設定とパッケージで新しい環境を作成したりしても、ハッシュ値は同じままです。 ただし、Python パッケージの追加や削除、パッケージ バージョンの変更などの環境定義の変更を行うと、結果のハッシュ値が変更されます。 環境内の依存関係またはチャネルの順序を変更すると、ハッシュも変更され、新しいイメージのビルドが必要になります。 同様に、キュレーションされた環境に変更を加えると、新しい「キュレーションされていない」環境が作成されます。 

> [!NOTE]
> 環境の名前を変更せずに、キュレーションされた環境にローカルの変更を送信することはできません。 プレフィックス "AzureML-" と "Microsoft" はキュレーションされた環境専用に予約されており、名前がそれらのどちらかで始まる場合、ジョブの送信は失敗します。

環境の計算されたハッシュ値は、ワークスペースとグローバル ACR (またはローカル実行のみのコンピューティング ターゲット) のハッシュ値と比較されます。 一致するものがある場合は、キャッシュされたイメージがプルされて使用されます。その他の場合はイメージのビルドがトリガーされます。

次の図は、3 つの環境定義を示しています。 それらのうちの 2 つは、名前とバージョンが異なりますが、ベース イメージと Python パッケージが同じであるため、ハッシュと対応するキャッシュ イメージが同じになります。 3 番目の環境は Python パッケージとバージョンが異なるため、異なるハッシュとキャッシュされたイメージになります。

![環境のキャッシュと Docker イメージの図](./media/concept-environments/environment-caching.png)

ワークスペース ACR に実際にキャッシュされているイメージは、`azureml/azureml_e9607b2514b066c851012848913ba19f` のような名前になり、ハッシュは最後に表示されます。

>[!IMPORTANT]
> * `numpy` など、パッケージの依存関係を固定せずに環境を作成した場合、その環境では、"*その環境の作成時に使用可能だった*" パッケージのバージョンが使用されます。 定義が一致する場合、今後の環境でも元のバージョンが使用されます。 
>
>   パッケージを更新するには、バージョン番号を指定してイメージのリビルドを強制します。 `numpy` を `numpy==1.18.1` に変更することがこの例に該当します。 入れ子になった依存関係など、新しい依存関係がインストールされます。これによって、以前の動作シナリオが破壊されるおそれがあります。
>
> * ご自分の環境定義で、`mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04` などベース イメージを固定しないと、`latest` タグが更新されるたびにイメージが再構築されます。 これにより、最新のパッチとシステムの更新プログラムをイメージで受信できます。

> [!WARNING]
>  [`Environment.build`](/python/api/azureml-core/azureml.core.environment.environment#build-workspace--image-build-compute-none-) メソッドでは、キャッシュされたイメージがリビルドされます。それによる副作用として、固定されていないパッケージが更新され、そのキャッシュされたイメージに対応するすべての環境定義の再現性が損なわれる可能性があります。

### <a name="image-patching"></a>イメージの修正

Microsoft は、既知のセキュリティ脆弱性に対するパッチをベース イメージに適用します。 サポートされているイメージの更新プログラムは 2 週間ごとにリリースされます。最新バージョンのイメージでは 30 日以上パッチが適用されていない脆弱性がないことがコミットされます。 修正済みのイメージは、新しい不変タグ付きでリリースされ、`:latest` タグは、パッチが適用されたイメージの最新バージョンに更新されます。 

独自のイメージを提供する場合は、提供者がそれらを更新する必要があります。

ベース イメージの詳細については、次のリンクを参照してください。

* [Azure Machine Learning ベース イメージ ](https://github.com/Azure/AzureML-Containers)GitHub リポジトリです。
* [カスタム イメージを使用してモデルをトレーニングする](how-to-train-with-custom-image.md)
* [カスタム コンテナーを使用して、TensorFlow モデルをデプロイする](how-to-deploy-custom-container.md)

## <a name="next-steps"></a>次のステップ

* Azure Machine Learning で[環境を作成および使用する](how-to-use-environments.md)方法を確認します。
* [環境クラス](/python/api/azureml-core/azureml.core.environment%28class%29)については、Python SDK 参照ドキュメントを参照してください。
