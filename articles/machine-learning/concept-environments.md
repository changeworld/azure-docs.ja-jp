---
title: ML 環境とは
titleSuffix: Azure Machine Learning
description: この記事では、さまざまなコンピューティング先での再現可能、監査可能、移植可能な機械学習の依存関係定義を有効にする、機械学習環境の利点について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: ad520c7e6503f28de0bd5538662c223575a078fa
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692627"
---
# <a name="what-are-azure-machine-learning-environments"></a>Azure Machine Learning 環境とは?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

環境では、トレーニングとスコアリングのスクリプトに関連した Python パッケージ、環境変数、およびソフトウェア設定を指定し、実行時間 (Python、Spark、または Docker) も指定します。 これらは、Azure Machine Learning ワークスペース内で管理およびバージョン管理されるエンティティであり、さまざまなコンピューティング ターゲット間で再現、監査、移植が可能な機械学習ワークフローを実現します。

ローカル コンピューティング上の環境オブジェクトを使用してトレーニング スクリプトを開発し、Azure Machine Learning コンピューティング上で同じ環境を再利用して大規模なモデル トレーニングを行うことができ、その同じ環境を使用してモデルをデプロイすることもできます。

次の図は、トレーニング用の実行構成と、Web サービス デプロイ用の推論およびデプロイ構成の両方で、同じ環境オブジェクトを使用できることを示しています。

![機械学習ワークフローでの環境の図](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>環境の種類

環境は、**キュレーション**、**ユーザー管理**、**システム管理**の 3 つのカテゴリに大別できます。

キュレートされた環境は Azure Machine Learning から提供され、既定でお使いのワークスペースで利用できます。 Python のパッケージと設定の集まりが含まれており、さまざまな機械学習フレームワークを開始するのに役立ちます。 

ユーザー管理環境の場合は、ユーザーが環境を設定し、トレーニング スクリプトで必要なすべてのパッケージをコンピューティング ターゲットにインストールする必要があります。 Conda で自動的に環境やインストールの確認が行われることはありません。 独自の環境を定義する場合、pip 依存関係としてバージョン `>= 1.0.45` の `azureml-defaults` を指定する必要があります。 このパッケージには、Web サービスとしてモデルをホストするために必要な機能が含まれています。

Python 環境とスクリプトの依存関係を [Conda](https://conda.io/docs/) で自動的に管理したいときは、システム管理環境が使用されます。 サービスでは、手動で構成できないリモート コンピューティング ターゲットでの有用性を理由に、既定でこの種類の環境を想定しています。

## <a name="creating-and-managing-environments"></a>環境の作成と管理

環境は、次のいずれかによって作成されます。

* 選別された環境を使用して、または独自の依存関係を定義することにより、新しい `Environment` オブジェクトを定義する
* 自分のワークスペースから既存の `Environment` オブジェクトを使用する。 これにより、依存関係に関して一貫性と再現性を確保できます。
* 既存の Anaconda 環境定義からインポートする。

具体的なコード例については、[使用方法](how-to-use-environments.md#create-an-environment)を参照してください。 また、環境はワークスペースから簡単に管理することができ、次の機能が含まれます。

* 実験を送信すると、環境は自動的にワークスペースに登録されます。 また、手動で登録することもできます。
* ワークスペースから環境を取り込み、トレーニング、デプロイにそれらを使用するか、環境定義を編集します。
* バージョン管理を使用すると、環境へのこれまでの変更を確認することができるため、再現性を確保することができます。
* 環境から Docker イメージを自動で構築します。

コード例については、使用方法の[環境の管理](how-to-use-environments.md#manage-environments)に関するセクションを参照してください。

## <a name="next-steps"></a>次のステップ

* Azure Machine Learning での[環境を作成および使用する](how-to-use-environments.md)方法を学ぶ
* [環境クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)については、Python SDK 参照ドキュメントを参照してください。
* [環境](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)については、R SDK 参照ドキュメントを参照してください。