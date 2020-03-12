---
title: Azure Machine Learning 環境について
titleSuffix: Azure Machine Learning
description: この記事では、さまざまなコンピューティング先での再現可能、監査可能、移植可能な機械学習の依存関係定義を有効にする、機械学習環境の利点について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 036efa27fb8d22c32f2f6bce1efe9dea300a3972
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302774"
---
# <a name="what-are-azure-machine-learning-environments"></a>Azure Machine Learning 環境とは?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning 環境では、トレーニングとスコアリングのスクリプトに関連した Python パッケージ、環境変数、およびソフトウェア設定を指定します。 また、実行時間 (Python、Spark、または Docker) も指定します。 環境は、Machine Learning ワークスペース内で管理およびバージョン管理されるエンティティであり、さまざまなコンピューティング先で再現、監査、移植が可能な機械学習ワークフローを実現します。

ローカル コンピューティングで `Environment` オブジェクトを使用して、次のことを行うことができます。
* トレーニング スクリプトを開発します。
* 大規模なモデルのトレーニング用に Azure Machine Learning コンピューティングで同じ環境を再利用します。
* その同じ環境を使ってモデルをデプロイします。

次の図は、トレーニング用の実行構成と、Web サービス デプロイ用の推論およびデプロイ構成の両方で、1 つの `Environment` オブジェクトを使用できることを示しています。

![機械学習ワークフローでの環境の図](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>環境の種類

環境は、*キュレーション*、*ユーザー管理*、*システム管理*の 3 つのカテゴリに大別できます。

キュレートされた環境は Azure Machine Learning から提供され、既定でお使いのワークスペースで利用できます。 Python のパッケージと設定の集まりが含まれており、さまざまな機械学習フレームワークを開始するのに役立ちます。 

ユーザー管理環境では、ユーザーが環境を設定し、トレーニング スクリプトで必要なすべてのパッケージをコンピューティング先にインストールする必要があります。 Conda では環境やインストールの確認が自動的には行われません。 独自の環境を定義する場合、pip 依存関係としてバージョン `>= 1.0.45` の `azureml-defaults` を指定する必要があります。 このパッケージには、Web サービスとしてモデルをホストするために必要な機能が含まれています。

Python 環境とスクリプトの依存関係を [Conda](https://conda.io/docs/) で自動的に管理したいときは、システム管理環境を使用します。 サービスでは、手動で構成できないリモート コンピューティング ターゲットでの有用性を理由に、既定でこの種類の環境を想定しています。

## <a name="create-and-manage-environments"></a>環境を作成および管理する

環境は次の方法で作成できます。

* 選別された環境を使用するか、独自の依存関係を定義することにより、新しい `Environment` オブジェクトを定義する。
* 自分のワークスペースから既存の `Environment` オブジェクトを使用する。 この方法では、依存関係に関して一貫性と再現性を確保できます。
* 既存の Anaconda 環境定義からインポートする。
* Azure Machine Learning CLI を使用する

特定のコード サンプルについては、[トレーニングとデプロイのための環境の再利用](how-to-use-environments.md#create-an-environment)に関するページの」の「環境の作成」セクションを参照してください。 環境はワークスペースから簡単に管理することができます。 これらには次の機能が含まれます。

* 実験を送信すると、環境は自動的にワークスペースに登録されます。 また、手動で登録することもできます。
* ワークスペースから環境を取り込んで、トレーニングやデプロイに使用したり、環境定義を編集したりできます。
* バージョン管理を使用すると、環境へのこれまでの変更を確認することができるため、再現性を確保することができます。
* 環境から Docker イメージを自動的に構築できます。

コー ドサンプルについては、[トレーニングとデプロイのための環境の再利用](how-to-use-environments.md#manage-environments)に関するページの「環境を管理する」セクションを参照してください。

## <a name="environment-building-caching-and-reuse"></a>環境のビルド、キャッシュ、再利用

Azure Machine Learning service では、Docker イメージと Conda 環境に環境定義が組み込まれます。 また、後続のトレーニング実行とサービス エンドポイントのデプロイで再利用できるように、環境はキャッシュされます。

### <a name="building-environments-as-docker-images"></a>Docker イメージとしての環境のビルド

通常、環境を使用して初めて実行を送信するときに、Azure Machine Learning service によって、ワークスペースに関連付けられている Azure Container Registry (ACR) で [ACR ビルド タスク](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)が呼び出されます。 ビルドされた Docker イメージは、ワークスペースの ACR にキャッシュされます。 実行の開始時に、イメージはコンピューティング先によって取得されます。

イメージのビルドは、次の 2 つの手順で構成されます。

 1. 基本イメージのダウンロードと、Docker 手順の実行
 2. 環境定義で指定されている Conda の依存関係に従った Conda 環境の構築

[ユーザーが管理する依存関係](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py)が指定された場合、2 番目の手順は省略されます。 この場合、ユーザーがすべての Python パッケージをインストールする必要があります。それには、Python パッケージを基本イメージに含めるか、最初の手順でカスタム Docker 手順を指定します。 また、ユーザーは Python 実行可能ファイルの正しい場所を指定する必要もあります。

### <a name="image-caching-and-reuse"></a>イメージのキャッシュと再利用

別の実行に同じ環境定義を使用する場合、Azure Machine Learning service では、ワークスペースの ACR にキャッシュされているイメージが再利用されます。 

キャッシュされているイメージの詳細を表示するには、[Environment.get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-) メソッドを使用します。

キャッシュされたイメージを再利用するか、新しいイメージを作成するかを決定するため、サービスでは、環境定義から[ハッシュ値](https://en.wikipedia.org/wiki/Hash_table)が計算されて、それが既存の環境のハッシュと比較されます。 ハッシュは以下のものが基になっています。
 
 * 基本イメージのプロパティ値
 * カスタム Docker 手順のプロパティ値
 * Conda 定義内の Python パッケージの一覧
 * Spark 定義内のパッケージの一覧 

ハッシュは環境名またはバージョンには依存しません。 Python パッケージの追加や削除や、パッケージのバージョンの変更など、環境定義が変更されると、ハッシュ値が変化し、イメージのリビルドがトリガーされます。 ただし、環境の名前を変更するだけ、または既存のものとまったく同じプロパティとパッケージで新しい環境を作成するだけの場合は、ハッシュ値は変化せず、キャッシュされたイメージが使用されます。

3 つの環境定義を示す次の図を参照してください。 それらの 2 つは名前とバージョンは異なりますが、基本イメージと Python パッケージは同じです。 同じハッシュを持つため、同じキャッシュされたイメージに対応します。 3 番目の環境は Python パッケージとバージョンが異なるため、別のキャッシュされたイメージに対応します。

![Docker イメージとしての環境のキャッシュの図](./media/concept-environments/environment-caching.png)

```numpy``` など、固定されていないパッケージの依存関係を持つ環境を作成すると、その環境では、環境の作成時にインストールされたパッケージのバージョンが引き続き使用されます。 また、一致した定義を持つ将来の環境では、古いバージョンが使用され続けます。 パッケージを更新するには、バージョン番号を指定してイメージのリビルドを強制します (```numpy==1.18.1``` など)。 新しい依存関係 (入れ子になったものなど) がインストールされ、以前に動作していたシナリオが壊れる可能性があることに注意してください

> [!WARNING]
>  [Environment.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace-) メソッドでは、キャッシュされたイメージがリビルドされます。それによる副作用として、固定されていないパッケージが更新され、そのキャッシュされたイメージに対応するすべての環境定義の再現性が損なわれる可能性があります。

## <a name="next-steps"></a>次のステップ

* Azure Machine Learning で[環境を作成および使用する](how-to-use-environments.md)方法を確認します。
* [環境クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)については、Python SDK 参照ドキュメントを参照してください。
* [環境](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)については、R SDK 参照ドキュメントを参照してください。
