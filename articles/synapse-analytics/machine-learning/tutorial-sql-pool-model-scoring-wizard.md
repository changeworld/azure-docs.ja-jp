---
title: チュートリアル:SQL プール向けの機械学習モデル スコアリング ウィザード
description: 機械学習モデル スコアリング ウィザードを使用して Synapse SQL プールのデータを強化する方法を示すチュートリアル
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 8e92ff75bb6a9757c06de3561a385cbcbb7f75ba
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019972"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-for-synapse-sql-pools"></a>チュートリアル:Synapse SQL プール向けの機械学習モデル スコアリング ウィザード

予測機械学習モデルを使用して、SQL プールのデータを簡単に強化する方法について説明します。  データ科学者が作成したモデルは、予測分析のためにデータ専門家から簡単にアクセスできるようになりました。 Synapse のデータ専門家は、Synapse SQL プールに配置するために Azure Machine Learning モデル レジストリからモデルを選択し、予測を起動するだけで、データを強化することができます。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> - 予測機械学習モデルをトレーニングし、モデルを Azure Machine Learning モデル レジストリに登録する
> - SQL スコアリング ウィザードを使用して Synapse SQL プールで予測を起動する

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

- ADLS Gen2 ストレージ アカウントが既定のストレージとして構成されている [Synapse Analytics ワークスペース](../get-started-create-workspace.md)。 使用する ADLS Gen2 ファイル システムの**ストレージ BLOB データ共同作成者**である必要があります。
- Synapse Analytics ワークスペースの Synapse SQL プール。 詳細については、[Synapse SQL プールの作成](../quickstart-create-sql-pool-studio.md)に関するページを参照してください。
- Synapse Analytics ワークスペースにおける Azure Machine Learning のリンクされたサービス。 詳細については、「[Synapse での Azure Machine Learning ワークスペースの作成](quickstart-integrate-azure-machine-learning.md)」を参照してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure ポータル](https://portal.azure.com/)

## <a name="train-a-model-in-azure-machine-learning"></a>Azure Machine Learning でモデルをトレーニングする

開始する前に、**sklearn** のバージョンが 0.20.3 であることを確認します。

ノートブック内のすべてのセルを実行する前に、コンピューティング インスタンスが実行されているかどうかを確認します。

![AML コンピューティングの確認](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Azure Machine Learning ワークスペースに移動します。

1. [Predict NYC Taxi Tips.ipynb](https://go.microsoft.com/fwlink/?linkid=2144301) をダウンロードします。

1. [Azure Machine Learning スタジオ](https://ml.azure.com)で Azure Machine Learning ワークスペースを起動します。

1. **[ノートブック]** に移動して、 **[ファイルのアップロード]** をクリックし、ダウンロードした "Predict NYC Taxi Tips.ipynb" を選択し、ファイルをアップロードします。
   ![ファイルをアップロードする](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. ノートブックがアップロードされて開いたら、 **[すべてのセルを実行]** をクリックします。

   いずれかのセルが失敗する可能性があり、Azure に対して認証するように求めるメッセージが表示される場合があります。 セルの出力でこのメッセージを探し、リンクに従ってコードを入力することによって、ブラウザーで認証します。 その後、そのノートブックを再実行します。

1. ノートブックで ONNX モデルがトレーニングされ、MLFlow に登録されます。 **[モデル]** に移動して、新しいモデルが適切に登録されているかどうかを確認します。
   ![レジストリのモデル](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. ノートブックを実行すると、テスト データも CSV ファイルにエクスポートされます。 この CSV ファイルをローカル システムにダウンロードします。 その後、CSV ファイルを SQL プールにインポートし、そのデータを使用してモデルをテストします。

   CSV ファイルは、ノートブック ファイルと同じフォルダーに作成されます。 すぐに表示されない場合は、ファイル エクスプローラーで [更新] をクリックします。

   ![CSV ファイル](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-sql-scoring-wizard"></a>SQL スコアリング ウィザードを使用して予測を開始する

1. Synapse Studio で Synapse ワークスペースを開きます。

1. **[データ]**  ->  **[リンク済み]**  ->  **[ストレージ アカウント]** の順に移動します。 `test_data.csv` を既定のストレージ アカウントにアップロードします。

   ![データをアップロードする](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. **[開発]**  ->  **[SQL スクリプト]** の順に移動します。 SQL プールに `test_data.csv` を読み込む新しい SQL スクリプトを作成します。

   > [!NOTE]
   > このスクリプトのファイル URL を更新してから実行してください。

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![SQL プールへのデータの読み込み](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. **[データ]**  ->  **[ワークスペース]** の順に移動します。 SQL プール テーブルを右クリックして、SQL スコアリング ウィザードを開きます。 **[機械学習]**  ->  **[既存のモデルで強化]** の順に移動します。

   > [!NOTE]
   > Azure Machine Learning 用に作成したリンク済みサービスがない限り、[機械学習] のオプションは表示されません (このチュートリアルの冒頭にある「**前提条件**」を参照してください)。

   ![[機械学習] オプション](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. ドロップダウン ボックスで、リンク済みの Azure Machine Learning ワークスペースを選択します。 これにより、選択した Azure Machine Learning ワークスペースのモデル レジストリから機械学習モデルの一覧が読み込まれます。 現時点では、ONNX モデルのみがサポートされているため、ONNX モデルのみが表示されます。

1. トレーニングしたモデルを選択し、 **[続行]** をクリックします。

   ![[Azure Machine Learning モデル] を選択します。](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. 次に、テーブルの列をモデル入力にマップし、モデルの出力を指定します。 モデルが MLFlow 形式で保存され、モデル シグネチャが設定されている場合、名前の類似性に基づくロジックを使用してマッピングが自動的に実行されます。 インターフェイスでは、手動マッピングもサポートされています。

   **[続行]** をクリックします。

   ![テーブルからモデルへのマッピング](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. 生成された T-SQL コードは、ストアド プロシージャ内でラップされます。 このため、ストアド プロシージャに名前を指定する必要があります。 メタデータ (バージョンや説明など) を含むモデル バイナリは、Azure Machine Learning から SQL プール テーブルに物理的にコピーされます。 そのため、モデルを保存するテーブルを指定する必要があります。 [既存のテーブルの使用] または [新しいテーブルの作成] のいずれかを選択できます。 完了したら、 **[モデルの配置] > [エディターを開く]** をクリックして、モデルを配置し、T-SQL 予測スクリプトを生成します。

   ![プロシージャの作成](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. スクリプトが生成されたら、[実行] をクリックしてスコアリングを実行し、予測を取得します。

   ![予測の実行](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>次の手順

- [クイック スタート: Synapse で Azure Machine Learning のリンクされたサービスを新規作成する](quickstart-integrate-azure-machine-learning.md)
- [Azure Synapse Analytics の Machine Learning 機能 (ワークスペース プレビュー)](what-is-machine-learning.md)
