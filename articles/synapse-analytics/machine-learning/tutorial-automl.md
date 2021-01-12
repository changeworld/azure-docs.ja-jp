---
title: チュートリアル:自動機械学習を使用してモデルをトレーニングする
description: Azure Synapse Analytics で機械学習モデルをコードなしでトレーニングする方法に関するチュートリアル。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: e219531a88787f19197a2e8c2a80040497c6dc1e
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901421"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>チュートリアル:コードなしで機械学習モデルをトレーニングする

[自動機械学習](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml)を使用してトレーニングする新しい機械学習モデルによって、Spark テーブルのデータをエンリッチすることができます。 Azure Synapse Analytics では、機械学習モデルを構築するためのトレーニング データセットとして使用する Spark テーブルをワークスペースで選択できます。この作業にコーディングは必要ありません。

このチュートリアルでは、Azure Synapse Analytics Studio のコーディング不要なエクスペリエンスを使用して機械学習モデルをトレーニングする方法について説明します。 Azure Machine Learning の自動機械学習を使用し、手動でのコーディングは行いません。 トレーニングするモデルの種類は、解決しようとしている問題によって異なります。

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

- [Azure Synapse Analytics ワークスペース](../get-started-create-workspace.md)。 Azure Data Lake Storage Gen2 ストレージ アカウントが既定のストレージとして構成されている必要があります。 必ず、Data Lake Storage Gen2 ファイル システムの **Storage Blob データ共同作成者** として作業してください。
- Azure Synapse Analytics ワークスペースの Apache Spark プール。 詳細については、「[クイック スタート:Azure Synapse Analytics Studio を使用した専用 SQL プールの作成](../quickstart-create-sql-pool-studio.md)。
- Azure Synapse Analytics ワークスペースの Azure Machine Learning のリンクされたサービス。 詳細については、「[クイック スタート:Azure Synapse Analytics での Azure Machine Learning のリンクされたサービスの新規作成](quickstart-integrate-azure-machine-learning.md)。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-spark-table-for-training-dataset"></a>トレーニング データセットの Spark テーブルを作成する

このチュートリアルには Spark テーブルが必要です。 以下のノートブックで 1 つ作成します。

1. ノートブック [Create-Spark-Table-NYCTaxi- Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229) をダウンロードします。

1. ノートブックを Azure Synapse Analytics Studio にインポートします。
![Azure Synapse Analytics のスクリーンショット。[インポート] オプションが強調表示されています。](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. 使用したい Spark プールを選択し、 **[すべて実行]** を選択します。 これにより、開いているデータセットからニューヨークのタクシー データが取得され、既定の Spark データベースに保存されます。
![Azure Synapse Analytics のスクリーンショット。[すべて実行] と Spark データベースが強調表示されています。](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. ノートブックの実行が完了すると、既定の Spark データベースの下に新しい Spark テーブルが表示されます。 **[データ]** から **nyc_taxi** という名前のテーブルを探します。
![Azure Synapse Analytics の [データ] タブのスクリーンショット。新しいテーブルが強調表示されています。](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automated-machine-learning-wizard"></a>自動機械学習ウィザードを起動する

その方法は次のとおりです。

1. 前の手順で作成した Spark テーブルを右クリックします。 ウィザードを開くには、 **[Machine Learning]**  >  **[Enrich with new model]\(新しいモデルを使用してエンリッチ\)** の順に選択します。
![Spark テーブルのスクリーンショット。[Machine Learning] と [Enrich with new model]\(新しいモデルを使用してエンリッチ\) が強調表示されています。](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. これで、Azure Machine Learning における自動機械学習の実験の実行を作成するための構成の詳細を入力できます。 この実行により、複数のモデルがトレーニングされます。実行が成功すると、最適なモデルが Azure Machine Learning モデル レジストリに登録されます。

   ![[Enrich with new model]\(新しいモデルを使用してエンリッチ\) 構成の指定値のスクリーンショット。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Azure Machine Learning ワークスペース**: 自動機械学習の実験の実行を作成するには、Azure Machine Learning ワークスペースが必要です。 また、[リンクされたサービス](quickstart-integrate-azure-machine-learning.md)を使用して、Azure Synapse Analytics ワークスペースを Azure Machine Learning ワークスペースにリンクする必要があります。 すべての前提条件を満たしたら、この自動実行に使用する Azure Machine Learning ワークスペースを指定できます。

    - **実験名**: 実験名を指定します。 自動機械学習の実行を送信するときは、実験名を指定します。 実行に関する情報は、Azure Machine Learning ワークスペースでその実験に保存されます。 このエクスペリエンスでは、新しい実験が既定で作成され、提案された名前が生成されますが、既存の実験の名前を指定することもできます。

    - **最適なモデル**: 自動実行での最適なモデルの名前を指定します。 最適なモデルにはこの名前が付けられ、この実行の後に、Azure Machine Learning モデル レジストリに自動的に保存されます。 自動機械学習の実行によって、多数の機械学習モデルが作成されます。 後の手順で選択する主要メトリックに基づいて、それらのモデルを比較し、最適なモデルを選択できます。

    - **Target column\(ターゲット列\)** : モデルをトレーニングする際の予測対象です。 予測する列を選択します。 (このチュートリアルでは、ターゲット列として数値列の `fareAmount` を選択します。)

    - **Spark プール**: 自動実験の実行に使用する Spark プール。 指定したプールで計算が実行されます。

    - **Spark configuration details\(Spark 構成の詳細\)** : Spark プールに加えて、セッション構成の詳細を指定するオプションもあります。

1. **[続行]** をクリックします。

## <a name="choose-task-type"></a>タスクの種類を選択する

答えようとしている質問に基づいて、実験の機械学習モデルの種類を選択します。 `fareAmount` がターゲット列です。これは数値であるため、ここでは **[回帰]** を選択します。 その後 **[続行]** を選択します。

![[Enrich with new model]\(新しいモデルを使用してエンリッチ\) のスクリーンショット。[回帰] が強調表示されています。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>追加の構成

前のセクションで **[回帰]** または **[分類]** をモデルの種類として選択した場合、次の構成が利用できます。

- **Primary metric\(主要メトリック\)** : モデルのパフォーマンスの測定に使用されるメトリック。 これは、自動実行で作成されたさまざまなモデルを比較し、パフォーマンスが最も高いモデルを特定するために使用されるメトリックです。

- **トレーニング ジョブ時間 (時間単位)** :実験でモデルを実行してトレーニングするときの最大時間 (時間単位)。 1 未満の値を指定することもできるので注意してください (例: `0.5`)。

- **コンカレント イテレーションの最大数**:並列で実行されるイテレーションの最大数を表します。

- **ONNX model compatibility\(ONNX モデルの互換性\)** : このオプションを有効にすると、自動機械学習によってトレーニングされたモデルが ONNX 形式に変換されます。 これは、モデルを Azure Synapse Analytics SQL プールでのスコアリングに使用する場合に特に関連します。

これらの設定にはすべて、カスタマイズできる既定値があります。
![[Enrich with new model]\(新しいモデルを使用してエンリッチ\) の追加構成のスクリーンショット。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

必須の構成がすべて完了したら、自動実行を開始できます。 **[Create run]\(実行の作成\)** を選択すれば、コードなしで実行が直接開始されます。 代わりにコードを使用したい場合は、 **[Open in notebook]\(ノートブックで開く\)** を選択することができます。 このオプションを選択することにより、実行を作成するコードを確認してノートブックを実行できます。

>[!NOTE]
>前のセクションで **[時系列の予測]** をモデルの種類として選択した場合は、追加の構成を行う必要があります。 また、予測では、ONNX モデルの互換性はサポートされていません。

### <a name="create-run-directly"></a>実行を直接作成する

自動機械学習の実行を直接開始するには、 **[Start Run]\(実行の開始\)** を選択します。 実行が開始されることを示す通知が表示されます。 その後、成功を示す別の通知が表示されます。 Azure Machine Learning で通知内のリンクを選択して状態を確認することもできます。
![成功通知のスクリーンショット。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>ノートブックを使用して実行を作成する

ノートブックを生成するには、 **[Open In Notebook]\(ノートブックで開く\)** を選択します。 その後、 **[すべて実行]** を選択します。 これにより、自動機械学習の実行に設定を追加する機会も得られます。

![ノートブックのスクリーンショット。[すべて実行] が強調表示されています。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

実行の送信に成功すると、Azure Machine Learning ワークスペースでの実験の実行へのリンクがノートブックの出力に表示されます。 リンクを選択すると、Azure Machine Learning で自動実行を監視できます。
![Azure Synapse Analytics のスクリーンショット。リンクが強調表示されています。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png)

## <a name="next-steps"></a>次の手順

- [チュートリアル:専用 SQL プール向けの機械学習モデル スコアリング ウィザード (プレビュー)](tutorial-sql-pool-model-scoring-wizard.md)
- [クイック スタート: Azure Synapse Analytics で Azure Machine Learning のリンクされたサービスを新規作成する](quickstart-integrate-azure-machine-learning.md)
- [Azure Synapse Analytics の機械学習機能](what-is-machine-learning.md)
