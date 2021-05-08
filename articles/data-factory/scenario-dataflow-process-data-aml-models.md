---
title: データ フローを使用して自動機械学習 (AutoML) モデルのデータを処理する
description: Azure Data Factory データ フローを使用して、自動機械学習 (AutoML) モデルのデータを処理する方法について説明します。
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: 45cd44cc0678b7f3a006a88bf66be2bca091af76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104595381"
---
# <a name="process-data-from-automated-machine-learning-models-by-using-data-flows"></a>データ フローを使用して自動機械学習モデルのデータを処理する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

自動機械学習 (AutoML) は、分類、回帰、および時系列予測に指定したターゲット メトリックを使用して、最適なモデルを自動的にトレーニング、調整、および取得するために、機械学習プロジェクトで採用されています。

AutoML の 1 つの課題は、データ ウェアハウスまたはトランザクション データベースからの生データが 10 GB のような巨大なデータセットになることです。 大規模なデータセットでは、モデルのトレーニングに時間がかかります。そのため、Azure Machine Learning モデルをトレーニングする前に、データ処理を最適化することをお勧めします。 このチュートリアルでは、Azure Data Factory を使用して、データセットを Machine Learning データセットの AutoML ファイルにパーティション分割する方法について説明します。

AutoML プロジェクトには、次の 3 つのデータ処理シナリオが含まれています。

* モデルをトレーニングする前に、大きなデータを AutoML ファイルにパーティション分割します。

     [Pandas データ フレーム](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html)は、一般的に、モデルをトレーニングする前にデータを処理するために使用されます。 Pandas データ フレームは 1 GB 未満のデータ サイズに適していますが、データが 1 GB を超える場合は、Pandas データ フレームでのデータ処理速度が低下します。 場合によっては、メモリ不足のエラー メッセージが表示されることもあります。 機械学習では [Parquet ファイル](https://parquet.apache.org/)形式を使用することをお勧めします。それがバイナリ列形式であるためです。
    
     Data Factory のマッピング データ フローは、データ エンジニアがコードを作成しなくても済むように視覚的に設計されたデータ変換です。 マッピング データ フローは、大きいデータを処理するための強力な手段となります。スケールアウトされた Spark クラスターがパイプラインで使用されるためです。

* トレーニング データセットとテスト データセットを分割します。
    
    トレーニング データセットは、トレーニング モデルに使用されます。 テスト データセットは、機械学習プロジェクトでモデルを評価するために使用されます。 マッピング データ フローの条件分割アクティビティによって、トレーニング データとテスト データが分割されます。

* 非修飾データを削除します。

    非修飾データ (行が 0 の Parquet ファイルなど) は、削除することができます。 このチュートリアルでは、集計アクティビティを使用して、行数を取得します。 この行数は、非修飾データを削除するための条件になります。

## <a name="preparation"></a>準備

次の Azure SQL Database テーブルを使用します。

```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>データ形式を Parquet に変換する

次のデータ フローによって、SQL Database テーブルが Parquet ファイル形式に変換されます。

- **ソース データセット**: SQL Database のトランザクション テーブル。
- **シンク データセット**: Parquet 形式の BLOB ストレージ。

## <a name="remove-unqualified-data-based-on-row-count"></a>行数に基づいて非修飾データを削除する

2 未満の行数を削除する必要があるとします。

1. 集計アクティビティを使用して、行数を取得します。 Col2 に基づいた **[グループ化]** と、行数に `count(1)` を指定した **[集計]** を使用します。

    ![行数を取得するための集計アクティビティの構成を示すスクリーンショット。](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. シンク アクティビティを使用して、 **[シンク]** タブで **[シンクの種類]** として **[キャッシュ]** を選択します。次に、 **[設定]** タブの **[キー列]** ドロップダウン リストから目的の列を選択します。

    ![CacheSink アクティビティを構成して、キャッシュされたシンク内の行数を取得する様子を示すスクリーンショット。](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. 派生列アクティビティを使用して、ソース ストリーム内の行数列を追加します。 **[派生列の設定]** タブで `CacheSink#lookup` 式を使用して、CacheSink から行数を取得します。

    ![source1 内の行数を追加するための派生列アクティビティの構成を示すスクリーンショット。](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. 条件分割アクティビティを使用して、非修飾データを削除します。 この例では、行数は Col2 列に基づいています。 条件は 2 未満の行数を削除することであるため、2 つの行 (ID=2 と ID=7) が削除されます。 データ管理のために、非修飾データを BLOB ストレージに保存します。

    ![2 以上のデータを取得する条件分割アクティビティの構成を示すスクリーンショット。](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    * 後の手順の元のソースで使用される行数を取得するための新しいソースを作成してください。
>    * パフォーマンスの観点から、CacheSink を使用してください。

## <a name="split-training-data-and-test-data"></a>トレーニング データとテスト データを分割する

パーティションごとにトレーニング データとテスト データを分割する必要があります。 この例では、Col2 の値が同じである場合、一番上の 2 行をテスト データとして取得し、残りの行をトレーニング データとして取得します。

1. ウィンドウ アクティビティを使用して、パーティションごとに 1 列の行番号を追加します。 **[Over]** タブで、パーティションの列を選択します。 このチュートリアルでは、Col2 のパーティション分割を行います。 **[並べ替え]** タブで順序を指定します。このチュートリアルでは、これは ID に基づいています。 **[ウィンドウ列]** タブで、各パーティションの行番号として 1 つの列を追加する順序を指定します。

    ![1 つの新しい列を行番号として追加するためのウィンドウ アクティビティの構成を示すスクリーンショット。](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. 条件分割アクティビティを使用して、各パーティションの一番上の 2 行をテスト データセットに、残りの行をトレーニング データセットに分割します。 **[条件分割の設定]** タブで、式 `lesserOrEqual(RowNum,2)` を条件として使用します。

    ![現在のデータセットをトレーニング データセットとテスト データセットに分割する条件分割アクティビティの構成を示すスクリーンショット。](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-the-training-and-test-datasets-with-parquet-format"></a>Parquet 形式でトレーニングおよびテスト データセットをパーティション分割する

**[最適化]** タブで、 **[パーティションごとの一意の値]** を使用してパーティションの列キーとして列を設定して、シンク アクティビティを使用します。

![トレーニング データセットのパーティションを設定するシンク アクティビティの構成を示すスクリーンショット。](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

パイプライン全体のロジックを見てみましょう。

![パイプライン全体のロジックを示すスクリーンショット。](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)

## <a name="next-steps"></a>次のステップ

マッピング データ フローの[変換](concepts-data-flow-overview.md)を使用して、残りのデータ フロー ロジックを構築します。
