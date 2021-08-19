---
title: 'クイック スタート: Synapse Analytics ギャラリーからのサンプル ノートブックを使用する'
description: Synapse Analytics ギャラリーのサンプル ノートブックを使用して、データを探索し、機械学習モデルを構築する方法について説明します。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: ''
ms.date: 06/11/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: 3fadfc3fdead7249480b2ce841601149f750b958
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112063734"
---
# <a name="quickstart-use-a-sample-notebook-from-the-synapse-analytics-gallery"></a>クイック スタート: Synapse Analytics ギャラリーからのサンプル ノートブックを使用する

このクイックスタートでは、サンプル機械学習ノートブックを Synapse Analytics ギャラリーからワークスペースにコピーして変更し、実行する方法について説明します。
サンプル ノートブックは、NYC タクシー旅行のオープン データセットを取り込み、視覚化を使用してデータを準備します。 次に、特定の旅行にヒントがあるかどうかを予測するためにモデルをトレーニングします。

このノートブックでは、モデルの作成 (**データのインポート**、**データの準備**、**モデルのトレーニング**、**評価**) に使用する基本的な手順について説明します。 このサンプルは、独自のデータを使用してモデルを作成するための開始点として使用できます。

## <a name="prerequisites"></a>前提条件

* Azure Data Lake Storage Gen2 ストレージ アカウントが既定のストレージとして構成されている [Azure Synapse Analytics ワークスペース](../get-started-create-workspace.md)。 使用する Data Lake Storage Gen2 ファイル システムの "*Storage Blob データ共同作成者*" である必要があります。
* Azure Synapse Analytics ワークスペースの [Spark プール](../get-started-analyze-spark.md)。

## <a name="copy-the-notebook-to-your-workspace"></a>ワークスペースにノートブックをコピーする

1. ワークスペースを開き、ホーム ページから **[Learn]** を選択します。
1. **[ナレッジ センター]** で、 **[ギャラリーの参照]** を選択します。
1. ギャラリーで **[ノートブック]** を選択します。
1. ノートブック "データ探索と ML モデリング - Spark MLib を使用した NYC のタクシーの予測" を検索して選択します。

   :::image type="content" source="media\quickstart-gallery-sample-notebook\gallery-select-ml-notebook.png" alt-text="ギャラリーで機械学習サンプル ノートブックを選択します。":::

1. **[続行]** を選択します。
1. ノートブックのプレビュー ページで、 **[ノートブックを開く]** を選択します。 サンプル ノートブックがワークスペースにコピーされ、開かれます。

    :::image type="content" source="media\quickstart-gallery-sample-notebook\gallery-open-ml-notebook.png" alt-text="機械学習サンプル ノートブックをワークスペースに開きます。":::

1. 開いたノートブックの **[アタッチ先]** メニューで、Apache Spark プールを選択します。

## <a name="run-the-notebook"></a>ノートブックを実行する

ノートブックは、それぞれが特定の関数を実行する複数のセルに分割されます。
各セルを手動で実行するか、セルを順番に実行するか、 **[すべて実行]** を選択してすべてのセルを実行できます。

ノートブック内の各セルについての説明を次に示します。

1. ノートブックが使用する PySpark 関数をインポートします。
1. **取り込み日** - Azure Open Dataset **NycTlcYellow** から処理のためにローカル データフレームにデータを取り込みます。 このコードは、特定の期間内のデータを抽出します。開始日と終了日を変更して、異なるデータを取得できます。
1. データセットをダウンサンプリングして、開発を迅速化します。 この手順を変更して、サンプル サイズまたはサンプリング シードを変更できます。
1. **探索的データ分析** - グラフを表示してデータを参照します。 これにより、モデルを作成する前に、どのようなデータ準備が必要かを知ることができます。
1. **データ準備と特徴付け** - データの視覚化によって見つかった外れ値データを除外し、いくつかの有用な派生変数を作成します。
1. **データ準備と特徴付けパート 2** - 不要な列を削除し、いくつかの追加機能を作成します。
1. **エンコード** - 文字列変数を、ロジスティック回帰モデルが想定する数値に変換します。
1. **テストとトレーニング データ セットの生成** - データを個別のテスト データ セットとトレーニング データ セットに分割します。 データを分割するために使用する分数とランダム化シードを変更できます。
1. **モデルのトレーニング** - ロジスティック回帰モデルをトレーニングし、その "ROC 下面積" メトリックを表示して、モデルの動作状態を確認します。 この手順では、他の場所で使用する場合に備えて、トレーニング済みのモデルも保存されます。
1. **評価と視覚化** - モデルの ROC 曲線をプロットして、モデルをさらに評価します。

## <a name="save-the-notebook"></a>ノートブックを保存する

ワークスペースのコマンド バーで **[発行]** を選択してノートブックを保存します。

## <a name="copying-the-sample-notebook"></a>サンプル ノートブックをコピーする

このノートブックのコピーを作成するには、上部のコマンド バーにある省略記号をクリックし、 **[Clone]** を選択してワークスペースにコピーを作成するか、 **[Export]** してノートブック (`.ipynb`) ファイルのコピーをダウンロードします。

:::image type="content" source="media\quickstart-gallery-sample-notebook\copy-notebook.png" alt-text="Export または Clone コマンドを使用してノートブックのコピーを作成します。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

終了時に Spark インスタンスがシャットダウンされるようにするには、接続されているすべてのセッション (ノートブック) を終了します。 プールは、Apache Spark プールに指定されている **アイドル時間** に達したときにシャットダウンされます。 また、ノートブックの右上にあるステータス バーから **[セッションの停止]** を選択することもできます。

:::image type="content" source="media\quickstart-gallery-sample-notebook\stop-session.png" alt-text="セッションを停止します。":::

## <a name="next-steps"></a>次のステップ

* [GitHub で詳細な Synapse サンプル ノートブックを確認する](https://github.com/Azure-Samples/Synapse/tree/main/MachineLearning)
* [Apache Spark による機械学習](../spark/apache-spark-machine-learning-concept.md)
