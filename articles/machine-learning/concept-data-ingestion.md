---
title: データ インジェスト オプション
titleSuffix: Azure Machine Learning
description: 機械学習モデルをトレーニングするためのデータ インジェスト オプションについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 71a02e47db288890d1392f5423da0ef817ecd690
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303122"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Azure Machine Learning のデータ インジェスト

この記事では、Azure Machine Learning で使用できる次のデータ インジェスト オプションの長所と短所について説明します。 

1. [Azure Data Factory](#use-azure-data-factory) のパイプライン
2. [Azure Machine Learning Python SDK](#use-the-python-sdk)

データ インジェストは、構造化されていないデータを 1 つまたは複数のソースから抽出し、機械学習モデルのトレーニング用に準備するプロセスです。 また、特に手動で行う場合や、複数のソースに大量のデータがある場合は、時間がかかります。 この作業を自動化することで、リソースが解放され、お使いのモデルで最新の適用可能なデータが確実に使用されるようになります。

最初に、データの抽出、読み込み、変換を目的として構築されている Azure Data Factory (ADF) を使用して評価することをお勧めします。 ADF を使用して要件を満たすことができない場合は、Python SDK を使用してカスタム コード ソリューションを開発するか、ADF と Python SDK を一緒に使用して、ニーズを満たすデータ インジェスト ワークフロー全体を作成できます。

## <a name="use-azure-data-factory"></a>Azure Data Factory を使用する

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) では、データ ソース監視のネイティブ サポートとデータ インジェスト パイプラインのトリガーが提供されています。  

次の表は、データ インジェスト ワークフローで Azure Data Factory を使用する場合の長所と短所をまとめたものです。

|長所|短所
---|---
データの抽出、読み込み、変換を目的として構築されています。|現時点では、Azure Data Factory パイプライン タスクの限定セットが提供されています 
データ移動とデータ変換を大規模に調整するためのデータ駆動型ワークフローを作成できます。|構築と保守にコストがかかります。 詳細については、Azure Data Factory の [価格に関するページ](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)を参照してください。
[Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) や [Azure Functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) など、さまざまな Azure ツールと統合 | スクリプトをネイティブに実行せず、代わりにスクリプトの実行を個別のコンピューティングに依存します 
データ ソースによってトリガーされるデータ インジェストをネイティブにサポートします| 
データ準備とモデル トレーニングのプロセスが異なります。|
Azure Data Factory データフローの埋め込みデータ系列機能|
コードの作成経験が少ない方向けに、スクリプトを使用しない方法として[ユーザー インターフェイス](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal)が提供されています |

次の手順と図は、Azure Data Factory のデータ インジェスト ワークフローを示しています。

1. データをソースからプルします
1. データを変換して出力 BLOB コンテナーに保存します。コンテナーは、Azure Machine Learning のデータ ストレージとして機能します
1. 準備したデータを保存すると、Azure Data Factory パイプラインにより、Machine Learning のトレーニング パイプラインが呼び出され、モデル トレーニング用に準備したデータを受け取ります


    ![ADF データ インジェスト](media/concept-data-ingestion/data-ingest-option-one.svg)

## <a name="use-the-python-sdk"></a>Python SDK の使用 

[Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml) を使用して、[Azure Machine Learning パイプライン](how-to-create-your-first-pipeline.md)の手順にデータ インジェスト タスクを組み込むことができます。

次の表は、データ インジェスト タスクに SDK と ML パイプラインの手順を使用する場合の長所と短所をまとめたものです。

長所| 短所
---|---
独自の Python スクリプトを構成します | データ ソース変更のトリガーをネイティブでサポートしていません。 Logic App または Azure 関数の実装が必要です
データ準備が、すべてのモデル トレーニング実行に含まれます|データ インジェスト スクリプトを作成するための開発スキルが必要です
[Azure Machine Learning コンピューティング](concept-compute-target.md#azure-machine-learning-compute-managed)など、さまざまなコンピューティング先のデータ準備スクリプトをサポートしています |インジェスト メカニズム作成のユーザー インターフェイスが提供されていません

次の図の Azure Machine Learning パイプラインは、データ インジェストとモデル トレーニングの 2 つの手順で構成されています。 データ インジェストの手順には、Python ライブラリと Python SDK を使用して実行できるタスクが含まれます。たとえば、ローカル/Web ソースからデータを抽出する、欠損値を補完するなどの基本的なデータ変換を行います。 次に、トレーニングの手順で、準備したデータをトレーニング スクリプトへの入力として使用して、機械学習モデルをトレーニングします。 

![Azure パイプライン + SDK データ インジェスト](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>次のステップ

* [Azure Data Factory](how-to-data-ingest-adf.md) を使用して、機械学習のデータ インジェスト パイプラインを作成する方法について説明します。
* [Azure Pipelines](how-to-cicd-data-ingestion.md) を使用して、データ インジェスト パイプラインの開発ライフ サイクルを自動化および管理する方法について説明します。