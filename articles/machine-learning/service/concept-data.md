---
title: Azure Machine Learning のデータ
titleSuffix: Azure Machine Learning
description: Azure Machine Learning がどのようにデータとの間でやり取りを行い、それが機械学習の実験全体でどのように利用されるかについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 12/09/2019
ms.openlocfilehash: 4ea0f7092862ef910e9f44e81cc4f5f40849bc70
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74820473"
---
# <a name="data-access-in-azure-machine-learning"></a>Azure Machine Learning でのデータ アクセス

この記事では、機械学習タスクのための Azure Machine Learning のデータ管理および統合ソリューションについて説明します。 この記事では、[Azure ストレージ アカウント](https://docs.microsoft.comazure/storage/common/storage-quickstart-create-account?tabs=azure-portal)および [Azure ストレージ サービス](https://docs.microsoft.com/azure/storage/common/storage-introduction) を既に作成していることを前提としています。

ストレージでデータを使用する準備ができたら、次のことをお勧めします。

1. Azure Machine Learning データストアを作成します。
2. そのデータストアから、Azure Machine Learning データセットを作成します。 
3. 以下のどちらかの方法で、そのデータセットを機械学習の実験で使用します。 
    1. モデル トレーニングのために実験のコンピューティング ターゲットにマウントします。

        **OR** 

    1. 自動機械学習 (自動 ML) 実験の実行、機械学習パイプライン、および [Azure Machine Learning デザイナー](concept-designer.md)などの Azure Machine Learning ソリューションで直接使用します。
4. データ ドリフトを検出するために、モデルの入力および出力データセットのためのデータセット モニターを作成します。 
5. データ ドリフトが検出された場合は、データセットを更新し、それに応じてモデルを再トレーニングします。

次の図は、この推奨データ アクセス ワークフローの視覚的なデモンストレーションを示しています。

![データの概念図](media/concept-data/data-concept-diagram.svg)

## <a name="access-data-in-storage"></a>ストレージ内のデータへのアクセス

ストレージ アカウントのデータにアクセスするために、Azure Machine Learning はデータストアとデータセットを提供しています。 データストアは、ストレージ サービスに対して抽象化のレイヤーを提供します。 これにより、接続情報はデータストアに保持され、スクリプトには公開されないため、ストレージのセキュリティおよびストレージへの容易なアクセスを促進します。 データセットは、機械学習の実験に使用したい、下位ストレージ内の特定のファイルを指します。 データストアとデータセットを組み合わせて使用することで、機械学習タスクのための安全でスケーラブルな、かつ再現可能なデータ配信ワークフローを実現できます。

### <a name="datastores"></a>データストア

Azure Machine Learning データストアは、Azure ストレージ サービスに対するストレージの抽象化です。 Azure ストレージ アカウントに簡単に接続し、下位の Azure ストレージ サービスのデータにアクセスするために、[データストア を登録および作成します](how-to-access-data.md)。

データストアとして登録可能な、サポートされている Azure storage サービスは次のとおりです。
+ Azure BLOB コンテナー
+ Azure ファイル共有
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Databricks ファイル システム
+ Azure Database for MySQL

### <a name="datasets"></a>データセット

データストア内のデータと交信し、機械学習タスク用の使用可能なオブジェクトとしてデータをパッケージ化するために、[Azure Machine Learning データセットを作成します](how-to-create-register-datasets.md)。 データ インジェストが複雑になることなく、さまざまな実験でデータセットを共有して再利用できるように、データセットをワークスペースに登録します。

データセットは、ローカル ファイル、パブリック URL、[Azure Open Datasets](#open)、またはデータストア内の特定のファイルから作成できます。 イン メモリの pandas データ フレームからデータセットを作成するには、csv などのローカル ファイルにデータを書き込み、そのファイルからデータセットを作成します。 データセットはデータのコピーではありませんが、ストレージ サービス内のデータを指す参照なので、追加のストレージ コストは発生しません。 

次の図は、Azure ストレージ サービスがない場合に、ローカル ファイル、パブリック URL、または Azure オープン データセットからデータセットを直接作成できることを示しています。 そうすることでデータセットが、実験の [Azure Machine Learning ワークスペース](concept-workspace.md)で自動的に作成された既定のデータストアに接続されます。

![データの概念図](media/concept-data/dataset-workflow.svg)

追加のデータセット機能については、次のドキュメントを参照してください。

+ データセット系列に[バージョンをつけて追跡する](how-to-version-track-datasets.md)。
+ [データセットを監視](how-to-monitor-datasets.md)して、データ ドリフトの検出に役立てる。
+  2 種類のデータセットについては、次のドキュメントを参照してください。
    + [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) は、指定されたファイルまたはファイルのリストを解析して、データを表形式で表します。 これにより、データを Pandas または Spark データフレームに具体化して、さらに操作とクレンジングを行うことができます。 TabularDataset を作成できるファイルの完全な一覧については、[TabularDatasetFactory クラス](https://aka.ms/tabulardataset-api-reference)を参照してください。

    + [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) は、データストアまたはパブリック URL 内の 1 つまたは複数のファイルを参照します。 この方法では、選択したファイルを FileDataset オブジェクトとして、コンピューティング ターゲットにダウンロードしたりマウントしたりすることができます。

## <a name="work-with-your-data"></a>データを処理する

データセットを使用すると、Azure Machine Learning 機能とのシームレスな統合を通じて、数多くの機械学習タスクを実行できます。 

+ [機械学習モデルをトレーニングします](how-to-train-with-datasets.md)。
+ データセットを次で使用します 
     + [自動 ML の実験](how-to-create-portal-experiments.md)
     + [デザイナー](tutorial-designer-automobile-price-train-score.md#import-data) 
+ [機械学習パイプライン](how-to-create-your-first-pipeline.md)でのバッチ推論によるスコアリングのためのデータセットにアクセスします。
+ [データのラベル付けプロジェクト](#label)を作成します。
+ [データ ドリフト](#drift)の検出のためのデータセット モニターを設定します。

<a name="open"></a>

## <a name="azure-open-datasets"></a>Azure Open Datasets

[Azure オープン データセット](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets)は選別されたパブリック データセットであり、機械学習ソリューションにシナリオ固有の機能を追加してモデルの精度を上げるために使用できます。 オープン データセットは Microsoft Azure のクラウドにあり、Azure Machine Learning に統合されています。 API を介してデータセットにアクセスすることもでき、Power BI や Azure Data Factory などの他の製品内でも使用できます。

Azure オープン データセットには、機械学習モデルのトレーニングと予測ソリューションのエンリッチメントに役立つ天気、国勢調査、祝日、公共の安全、場所に関するパブリック ドメイン データが含まれます。 Azure オープン データセット上でパブリック データセットを共有することもできます。

<a name="label"></a>

## <a name="data-labeling"></a>データのラベル付け

大量のデータにラベルを付けることは、多くの場合、機械学習プロジェクトでは困難でした。 画像の分類やオブジェクトの検出など、Computer Vision コンポーネントを使用するそれらには、通常、何千ものイメージとそれに対応するラベルが必要です。

Azure Machine Learning を使用すると、ラベル付けプロジェクトの作成、管理、監視を一元的に行うことができます。 ラベル付けプロジェクトは、データ、ラベル、チーム メンバーの間の調整に役立ち、ラベル付けタスクをより効率的に管理できるようになります。 現在サポートされているタスクは、複数ラベルまたは多クラスでの画像分類と、境界ボックスを使用したオブジェクト識別です。

+ [データラベル付けプロジェクト](how-to-create-labeling-projects.md)を作成し、機械学習の実験で使用するためのデータセットを出力します。

<a name="drift"></a>

## <a name="data-drift"></a>データ ドリフト

機械学習においてデータの誤差とは、モデルのパフォーマンスの低下につながるモデルの入力データの変更のことです。 これはモデルの精度が時間の経過と共に低下する主な理由の 1 つであるため、データ ドリフトの監視はモデルのパフォーマンスに関する問題の検出に役立ちます。
データセット内の新しいデータに対するデータ ドリフトを検出してアラートを生成する方法の詳細については、[データセット モニターの作成](how-to-monitor-datasets.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順 

+ Azure Machine Learning Studio または Python SDK でデータセットを作成するには、[これらの手順に従ってください](how-to-create-register-datasets.md)。
+ [サンプル ノートブック](https://aka.ms/dataset-tutorial)を使用して、データセットのトレーニング例を試してみてください。
+ データ ドリフトの例については、こちらの[データ ドリフトのチュートリアル](https://aka.ms/datadrift-notebook)を参照してください。