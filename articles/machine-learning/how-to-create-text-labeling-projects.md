---
title: テキスト ラベル付けプロジェクトの設定
titleSuffix: Azure Machine Learning
description: データのラベル付けツールを使用してテキストにラベルを付けるプロジェクトを作成します。 テキストの各部分に適用する 1 つまたは複数のラベルを指定します。
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.date: 09/24/2021
ms.custom: data4ml
ms.openlocfilehash: e4d227cf215ab5d37af8a25e6e44e811f739f25b
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2021
ms.locfileid: "129423711"
---
# <a name="create-a-text-labeling-project-and-export-labels-preview"></a>テキスト ラベル付けプロジェクトを作成してラベルをエクスポートする (プレビュー)

Azure Machine Learning でテキスト データにラベル付けするプロジェクトを作成して実行する方法について説明します。  テキストの各部分に適用する 1 つまたは複数のラベルを指定します。

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

テキスト データには、".txt" または ".csv" ファイルを使用できます。

* ".txt" ファイルの場合、各ファイルはラベル付けされる 1 つの項目を表します。
* ".csv" ファイルの場合、各行は、ラベラーに示される 1 つの項目を表します。  その行にラベルを付けするときに使用する 1 つ以上の列を表示できます。

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
    * 各行に 1 つの応答が含まれる .csv ファイルを使用している場合は、 **[表形式]** を選択します。
    * 応答ごとに個別の .txt ファイルを使用している場合は、 **[ファイル]** を選択します。
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
    * 各行が 1 つの応答である .csv ファイルを使用している場合は、 **[表形式]** を選択します。
    * 応答ごとに個別の .txt ファイルを使用している場合は、 **[ファイル]** を選択します。
1. (省略可能) データセットの説明を入力します。
1. **[次へ]** を選択します
1. (省略可能) データストアを選択または作成します。 または、既定のまま、Machine Learning ワークスペースの既定の BLOB ストア ("workspaceblobstore") にアップロードします。
1. **[アップロード]** を選択し、アップロードするローカル ファイルまたはフォルダーを選択します。
1. **[次へ]** を選択します。
1. .csv ファイルをアップロードする場合、以下を行います。
    * 設定とプレビューを確認し、 **[次へ]** を選択します。
    * その行を分類するときにラベラーに示したいテキストのすべての列を含めます。
    * **[次へ]** を選択します。
1.  詳細を確認します。 **[戻る]** を選択して設定を変更するか、 **[作成]** を選択してデータセットを作成します。


## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"> </a> 増分更新を構成する

[!INCLUDE [refresh](../../includes/machine-learning-data-labeling-refresh.md)]

## <a name="specify-label-classes"></a>ラベル クラスを指定する

[!INCLUDE [classes](../../includes/machine-learning-data-labeling-classes.md)]

## <a name="describe-the-text-labeling-task"></a>テキスト ラベル付けタスクを説明する

[!INCLUDE [describe](../../includes/machine-learning-data-labeling-describe.md)]

>[!NOTE]
> ラベラーは最初の 9 つのラベルを 1 から 9 の数字キーを使用して選択できることを必ず明記してください。


## <a name="initialize-the-text-labeling-project"></a>テキスト ラベル付けプロジェクトを初期化する

[!INCLUDE [initialize](../../includes/machine-learning-data-labeling-initialize.md)]

## <a name="run-and-monitor-the-project"></a>プロジェクトを実行して監視する

[!INCLUDE [run](../../includes/machine-learning-data-labeling-run.md)]

### <a name="dashboard"></a>ダッシュボード


**[ダッシュボード]** タブに、ラベル付けタスクの進行状況が表示されます。

:::image type="content" source="./media/how-to-create-labeling-projects/text-labeling-dashboard.png" alt-text="テキスト データのラベル付けダッシュボード":::


進行状況グラフには、ラベル付けされた項目、スキップされた項目、レビューが必要な項目、またはまだそれが行われていない項目の数が表示されます。  グラフの上にカーソルを合わせると、各セクションの項目数が表示されます。

中央のセクションには、これから割り当てられるタスクのキューが表示されます。 


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

