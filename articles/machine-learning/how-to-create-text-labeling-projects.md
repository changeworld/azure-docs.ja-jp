---
title: テキスト ラベル付けプロジェクトの設定
titleSuffix: Azure Machine Learning
description: データのラベル付けツールを使用してテキストにラベルを付けるプロジェクトを作成します。 テキストの各部分に適用する 1 つまたは複数のラベルを指定します。
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: data4ml, ignite-fall-2021
ms.openlocfilehash: e3097c6b00d97287526015836c44ddcaeb08177a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468709"
---
# <a name="create-a-text-labeling-project-and-export-labels-preview"></a>テキスト ラベル付けプロジェクトを作成してラベルをエクスポートする (プレビュー)

Azure Machine Learning でテキスト データにラベル付けするプロジェクトを作成して実行する方法について説明します。  各テキスト項目に適用する 1 つまたは複数のラベルを指定します。

データのラベル付けツールを使用して、[画像ラベル付けプロジェクトを作成する](how-to-create-image-labeling-projects.md)こともできます。

> [!IMPORTANT]
> テキストのラベル付けは、現在、パブリック プレビュー段階にあります。
> プレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="text-labeling-capabilities"></a>テキスト ラベル付け機能

Azure Machine Learning のデータのラベル付け機能を通じて、以下のように、データのラベル付けプロジェクトの作成、管理、監視を一元的に行うことができます。

- データ、ラベル、チーム メンバーを調整して、ラベル付けタスクを効率的に管理する。 
- 進行状況を追跡し、未完了のラベル付けタスクのキューを維持する。
- プロジェクトを開始および停止し、ラベル付けの進行状況を制御する。
- ラベル付きのデータを確認し、Azure Machine Learning データセットとしてエクスポートする。

> [!Important]
> Azure BLOB データストア内のテキスト データにアクセスできることが必要です。 (既存のデータストアがない場合は、プロジェクトの作成時にファイルをアップロードできます)。

テキスト データで使用できるデータ形式:

* **.txt**: 各ファイルはラベル付けされる 1 つの項目を表します。
* **.csv** または **.tsv**: 各行は、ラベラーに示される 1 つの項目を表します。  行にラベル付けするために、ラベラーで表示できる列を決定します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [prerequisites](../../includes/machine-learning-data-labeling-prerequisites.md)]

## <a name="create-a-text-labeling-project"></a>テキスト ラベル付けプロジェクトの作成

[!INCLUDE [start](../../includes/machine-learning-data-labeling-start.md)]

1. プロジェクトを作成するには、 **[プロジェクトの追加]** を選択します。 プロジェクトに適切な名前を付けます。 プロジェクトが将来削除されても、プロジェクト名を再利用することはできません。

1. **[テキスト]** を選択して、テキスト ラベル付けプロジェクトを作成します。

    :::image type="content" source="media/how-to-create-labeling-projects/text-labeling-creation-wizard.png" alt-text="テキストのラベル付けのためのラベル付けプロジェクトの作成":::

    * ラベルのセットから "*1 つのラベル*" だけを各テキストに適用する場合は、プロジェクトに対して **[テキスト分類マルチクラス (プレビュー)]** を選択します。
    * ラベルのセットから "*1 つ以上*" のラベルを各テキストに適用する場合は、プロジェクトに対して **[テキスト分類マルチラベル (プレビュー)]** を選択します。

1. 続行する準備ができたら、 **[次へ]** を選択します。

## <a name="add-workforce-optional"></a>従業員を追加する (省略可能)

[!INCLUDE [outsource](../../includes/machine-learning-data-labeling-outsource.md)]

## <a name="specify-the-data-to-label"></a>ラベル付けの対象データを指定する

対象のデータが含まれるデータセットを既に作成済みの場合は、 **[既存のデータセットを選択します]** ボックスの一覧から選択します。 または、 **[Create a dataset]\(データセットの作成\)** を選択して、既存の Azure データストアを選択するか、ローカル ファイルをアップロードします。

> [!NOTE]
> 1 つのプロジェクトに 50 万個を超えるファイルを含めることはできません。  データセットにこれを超えるファイルがある場合は、最初の 50 万個だけが読み込まれます。  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Azure データストアからデータセットを作成する

多くの場合は、ローカル ファイルをアップロードするだけでかまいません。 ただし、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) には、大量のデータをより高速かつ堅牢に転送する方法が用意されています。 既定のファイル移動方法としては、Storage Explorer をお勧めします。

Azure Blob Storage に既に格納済みのデータからデータセットを作成するには:

1. **[Create a dataset]\(データセットの作成\)**  >  **[From datastore]\(データストアから\)** の順に選択します。
1. データセットに **名前** を割り当てます。
1. 次のように、**データセットの種類** を選択します。
    * 各行に 1 つの応答が含まれる .csv ファイルまたは .tsv ファイルを使用している場合は、 **[表形式]** を選びます。
    * 応答ごとに個別の .txt ファイルを使用している場合は、 **[ファイル]** を選びます。
1. (省略可能) データセットの説明を入力します。
1. **[次へ]** を選択します。
1. データストアを選択します。
1. データがお使いの Blob Storage 内のサブフォルダーにある場合は、 **[参照]** を選択してパスを選択します。
    * 選択したパスのサブフォルダー内にあるすべてのファイルを含めるには、パスの末尾に "/**" を追加します。
    * 現在のコンテナーとそのサブフォルダー内にあるすべてのデータを含めるには、"* */* .*" を追加します。
1. **[次へ]** を選択します。
1. 詳細を確認します。 **[戻る]** を選択して設定を変更するか、 **[作成]** を選択してデータセットを作成します。

### <a name="create-a-dataset-from-uploaded-data"></a>アップロードしたデータからデータセットを作成する

データを直接アップロードするには:

1. **[Create a dataset]\(データセットの作成\)**  >  **[From local files]\(ローカル ファイルから\)** の順に選択します。
1. データセットに **名前** を割り当てます。
1. **データセットの種類** を選択します。
    * 各行に 1 つの応答が含まれる .csv ファイルまたは .tsv ファイルを使用している場合は、 **[表形式]** を選びます。
    * 応答ごとに個別の .txt ファイルを使用している場合は、 **[ファイル]** を選びます。
1. (省略可能) データセットの説明を入力します。
1. **[次へ]** を選択します
1. (省略可能) データストアを選択または作成します。 または、既定のまま、Machine Learning ワークスペースの既定の BLOB ストア ("workspaceblobstore") にアップロードします。
1. **[アップロード]** を選択し、アップロードするローカル ファイルまたはフォルダーを選択します。
1. **[次へ]** を選択します。
1. .csv ファイルまたは .tsv ファイルをアップロードする場合:
    * 設定とプレビューを確認し、 **[次へ]** を選択します。
    * その行を分類するときにラベラーに示したいテキストのすべての列を含めます。  ML によるラベル付けを使用している場合、数値列を追加すると、ML アシスト モデルが低下する可能性があります。
    * **[次へ]** を選択します。
1.  詳細を確認します。 **[戻る]** を選択して設定を変更するか、 **[作成]** を選択してデータセットを作成します。


## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"> </a> 増分更新を構成する

[!INCLUDE [refresh](../../includes/machine-learning-data-labeling-refresh.md)]

> [!NOTE]
> 増分更新は、表形式 (.csv または .tsv) データセット入力を使用するプロジェクトでは使用できません。

## <a name="specify-label-classes"></a>ラベル クラスを指定する

[!INCLUDE [classes](../../includes/machine-learning-data-labeling-classes.md)]

## <a name="describe-the-text-labeling-task"></a>テキスト ラベル付けタスクを説明する

[!INCLUDE [describe](../../includes/machine-learning-data-labeling-describe.md)]

>[!NOTE]
> ラベラーは最初の 9 つのラベルを 1 から 9 の数字キーを使用して選択できることを必ず明記してください。

## <a name="use-ml-assisted-data-labeling"></a>支援付き機械学習データ ラベル付けを使用する

**[ML によるラベル付け]** ページでは、自動機械学習モデルをトリガーして、ラベル付けタスクを高速化することができます。 ML ラベル付けは、ファイル (.txt) と表形式 (.csv) の両方のテキスト データ入力で使用できます。

**ML によるラベル付け** を使用するには、次のようにします。

* **[ML Assist によるラベル付けを有効にする]** を選びます。
* プロジェクトの **データセット言語** を選びます。 [TextDNNLanguages クラス](/python/api/azureml-automl-core/azureml.automl.core.constants.textdnnlanguages?view=azure-ml-py&preserve-view=true)でサポートされているすべての言語が、この一覧に表示されます。
* 使用するコンピューティング先を指定します。 お使いのワークスペース内にない場合は、自動的にコンピューティング クラスターが作成されて、ワークスペースに追加されます。   クラスターは最小限の 0 ノードで作成されるので、使用されていないときはコストがかかりません。

### <a name="how-does-ml-assisted-labeling-work"></a>ML によるラベル付けの仕組み

ラベル付けプロジェクトの初めに、潜在的な偏りを減らすために、項目がランダムな順序にシャッフルされます。 ただし、データセットに偏りが存在すれば、それがトレーニング済みのモデルに反映されます。 たとえば、項目の 80% が単一のクラスに属している場合、モデルのトレーニングに使用されるデータの約 80% は、そのクラスに属することになります。 

ML によるラベル付けによって使用されるテキスト DNN モデルをトレーニングする場合、トレーニングのサンプルごとの入力テキストは、ドキュメント内の最初の約 128 語に制限されます。  表形式入力の場合、この制限を適用する前に、すべてのテキスト列が最初に連結されます。 これは、モデルのトレーニングをタイムリーに完了するために課される実際的な制限です。 ドキュメント内の実際のテキスト (ファイル入力の場合) またはテキスト列のセット (表形式入力の場合) は、128 語を超えることができます。  この制限は、トレーニング プロセス中にモデルによって内部的に利用される内容にのみ関連します。

ML によるラベル付けを開始するために必要とされるラベル付けされた項目の正確な数は固定されていません。 これは、ラベル クラスの数やラベルの分布などの多くの要因によって、ラベル付けプロジェクトごとに大きく異なります。

最終的なラベルは依然としてラベラーからの入力に依存するため、この技術は "*人間参加 (Human in the loop) 型*" のラベル付けと呼ばれることがあります。

> [!NOTE]
> ML によるデータのラベル付けでは、[仮想ネットワーク](how-to-network-security-overview.md)の内側でセキュリティ保護された既定のストレージ アカウントはサポートされません。 ML によるデータのラベル付けには、既定以外のストレージ アカウントを使用する必要があります。 既定以外のストレージ アカウントは、仮想ネットワークの背後でセキュリティ保護できます。

### <a name="pre-labeling"></a>事前ラベル付け

トレーニング用に十分なラベルが送信されると、トレーニング済みのモデルを使用してタグが予測されます。 ラベラーには、各項目に既に存在する予測済みのラベルを含むページが表示されるようになります。 その後、このタスクでは、これらの予測を再検討して、間違ってラベル付けされた項目を修正してから、ページを送信します。  

手動でラベル付けされたデータで機械学習モデルのトレーニングが完了すると、手動でラベル付けされた項目のテスト セットでモデルが評価され、さまざまな信頼度のしきい値でその精度が判定されます。 事前ラベルを表示するだけの精度がモデルにあるかどうかの基準となる信頼度のしきい値は、この評価プロセスを使用して割り出されます。 その後、このモデルは、ラベル付けされていないデータに対して評価されます。 予測の信頼度がこのしきい値を超える項目が、事前ラベル付けに使用されます。

## <a name="initialize-the-text-labeling-project"></a>テキスト ラベル付けプロジェクトを初期化する

[!INCLUDE [initialize](../../includes/machine-learning-data-labeling-initialize.md)]

## <a name="run-and-monitor-the-project"></a>プロジェクトを実行して監視する

[!INCLUDE [run](../../includes/machine-learning-data-labeling-run.md)]

### <a name="dashboard"></a>ダッシュボード


**[ダッシュボード]** タブに、ラベル付けタスクの進行状況が表示されます。

:::image type="content" source="./media/how-to-create-labeling-projects/text-labeling-dashboard.png" alt-text="テキスト データのラベル付けダッシュボード":::


進行状況グラフには、ラベル付けされた項目、スキップされた項目、レビューが必要な項目、またはまだそれが行われていない項目の数が表示されます。  グラフの上にカーソルを合わせると、各セクションの項目数が表示されます。

中央のセクションには、これから割り当てられるタスクのキューが表示されます。 ML によるラベル付けが有効になっている場合、事前にラベル付けされた項目の数も表示されます。


右側にあるのは、完了している当該タスクのラベルの分布です。  プロジェクトの種類によっては、1 つの項目に複数のラベルを含めることができ、その場合、ラベルの合計数が、項目の合計数よりも大きくなる可能性があるので注意してください。

### <a name="data-tab"></a>[データ] タブ

**[データ]** タブでは、データセットを表示して、ラベル付けされたデータを確認できます。 ラベルを表示するには、ラベル付けされたデータをスクロールします。 間違ってラベル付けされたデータが表示された場合は、それを選択して **[拒否]** を選択することで、ラベルを削除し、データをラベルなしのキューに戻すことができます。

### <a name="details-tab"></a>[詳細] タブ

プロジェクトの詳細を表示します。  このタブでは、次のことができます。

* プロジェクトの詳細と入力データセットを表示する
* 増分更新を有効にする
* プロジェクトでのラベル付きの出力を格納するために使用するストレージ コンテナーの詳細を表示する
* プロジェクトにラベルを追加する
* ラベルに付ける指示を編集する

### <a name="access-for-labelers"></a>ラベラー用のアクセス権

[!INCLUDE [access](../../includes/machine-learning-data-labeling-access.md)]

## <a name="add-new-label-class-to-a-project"></a>プロジェクトに新しいラベル クラスを追加する

[!INCLUDE [add-label](../../includes/machine-learning-data-labeling-add-label.md)]

## <a name="export-the-labels"></a>ラベルをエクスポートする
 
ラベル付けプロジェクトの **[プロジェクトの詳細]** ページにある **[エクスポート]** ボタンを使用します。 いつでも、Machine Learning の実験用にラベル データをエクスポートできます。

以下をエクスポートできます。

* CSV ファイル。 CSV ファイルは、Azure Machine Learning ワークスペースの既定の BLOB ストアにある *Labeling/export/csv* 内のフォルダーに作成されます。 
* [ラベル付き Azure Machine Learning データセット](how-to-use-labeled-dataset.md)。 

エクスポートした Azure Machine Learning データセットには、Machine Learning の **[データセット]** セクションでアクセスします。 また、データセットの詳細ページには、Python からラベルにアクセスするためのサンプル コードも表示されます。

![エクスポートされたデータセット](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>トラブルシューティング

[!INCLUDE [troubleshooting](../../includes/machine-learning-data-labeling-troubleshooting.md)]


## <a name="next-steps"></a>次のステップ

* [テキストにタグを付けする方法](how-to-label-data.md#label-text)
