---
title: 新しいファイルや更新されたファイルを増分コピーするためのデータ ツール
description: Azure データ ファクトリを作成してから、データのコピー ツールを使用して LastModifiedDate を基に新しいファイルを増分的に読み込みます。
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/12/2021
ms.openlocfilehash: bd9bfff8faf8f5c5e2c81ea9a8abea23af0da6ca
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771652"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>データのコピー ツールを使用し LastModifiedDate に基づいて新しいファイルおよび変更されたファイルを増分コピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

このチュートリアルでは、Azure portal を使用してデータ ファクトリを作成します。 次に、データのコピー ツールを使用して、新しいファイルおよび変更されたファイルのみを Azure Blob Storage から Azure Blob Storage に増分的にコピーするパイプラインを作成します。 `LastModifiedDate` を使用して、どのファイルをコピーするかを決定します。

ここで説明した手順を完了すると Azure Data Factory によってソース ストア内のすべてのファイルがスキャンされ、`LastModifiedDate` によってファイル フィルターが適用されます。また、新しいファイルまたは前回以降に更新されたファイルのみがコピー先ストアにコピーされます。 Data Factory によって多数のファイルがスキャンされる場合は、継続時間が長くなると予想されることに注意してください。 コピーされるデータ量を減らしても、ファイルのスキャンには時間がかかります。

> [!NOTE]
> Data Factory を初めて使用する場合は、「[Azure Data Factory の概要](introduction.md)」を参照してください。

このチュートリアルでは、以下のタスクを完了します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * データのコピー ツールを使用してパイプラインを作成します。
> * パイプラインとアクティビティの実行を監視します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* **Microsoft Azure Storage アカウント**:Blob ストレージをソースおよびシンク データ ストアとして使用します。 Azure Storage アカウントがない場合は、[ストレージ アカウントの作成](../storage/common/storage-account-create.md)に関するページの手順に従ってください。

## <a name="create-two-containers-in-blob-storage"></a>Blob ストレージに 2 つのコンテナーを作成する

次の手順を実行して、チュートリアル用の Blob ストレージを準備します。

1. **source** という名前のコンテナーを作成します。 この作業は、[Azure Storage Explorer](https://storageexplorer.com/) などのさまざまなツールを使用して実行できます。

2. **destination** という名前のコンテナーを作成します。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. 左側のウィンドウで、 **[リソースの作成]** を選びます。 **[統合]**  >  **[Data Factory]** を選択します。

   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="データ ファクトリの選択":::

2. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。

   データ ファクトリの名前はグローバルに一意にする必要があります。 このエラー メッセージが表示されることもあります。

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="重複する名前に関する、新しい Data Factory のエラーメッセージ。":::

   データ ファクトリの名前の値に関するエラー メッセージが表示された場合は、別の名前を入力してください。 たとえば、_**yourname**_**ADFTutorialDataFactory** という名前を使用します。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事をご覧ください。
3. **[サブスクリプション]** で、新しいデータ ファクトリの作成先となる Azure サブスクリプションを選択します。
4. **[リソース グループ]** で、次の手順のいずれかを行います。

    * **[既存のものを使用]** を選択し、一覧から既存のリソース グループを選択します。

    * **[新規作成]** を選択し、リソース グループの名前を入力します。
         
    リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。

5. **[バージョン]** で、 **[V2]** を選択します。
6. **[場所]** で、データ ファクトリの場所を選択します。 サポートされている場所のみがこの一覧に表示されます。 データ ファクトリで使用されるデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (Azure HDInsight など) は、他の場所やリージョンに存在していてもかまいません。
8. **［作成］** を選択します
9. データ ファクトリが作成されるとデータ ファクトリのホーム ページが表示されます。
10. 別のタブで Azure Data Factory のユーザー インターフェイス (UI) を開くには、 **[Open Azure Data Factory Studio]\(Azure Data Factory Studio を開く\)** タイルで **[開く]** を選択します。

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="[Open Azure Data Factory Studio] タイルを含む、Azure Data Factory のホーム ページ。":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>データのコピー ツールを使用してパイプラインを作成する

1. Azure Data Factory のホーム ページで **[取り込む]** タイルを選択して、データ コピー ツールを開きます。

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="ADF のホーム ページを示すスクリーンショット。":::

2. **[プロパティ]** ページで、次の手順を行います。

    1. **[タスクの種類]** で **[Built-in copy task]\(組み込みコピー タスク\)** を選択します。

    1. **[Task cadence or task schedule]\(タスクの周期またはタスクのスケジュール\)** で、 **[Tumbling window]\(タンブリング ウィンドウ\)** を選択します。

    1. **[Recurrence]\(繰り返し\)** で、**15 分** と指定します。

    1. **[次へ]** を選びます。

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png" alt-text="[データのコピー] の [プロパティ] ページ":::

3. **[ソース データ ストア]** ページで、次の手順を実行します。

    1. **[+ 新しい接続]** を選択して、接続を追加します。

    1. ギャラリーから **[Azure Blob Storage]** を選び、 **[続行]** を選択します。

        :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png" alt-text="[Azure Blob Storage] を選択する":::

    1. **[新しい接続 (Azure Blob Storage)]** ページで、 **[Azure サブスクリプション]** の一覧から自分の Azure サブスクリプションを選択し、 **[ストレージ アカウント名]** の一覧から自分のストレージ アカウントを選択します。 接続をテストし、 **[作成]** を選択します。

    1. **[接続]** ブロックで新しく作成した接続を選択します。

    1. **[ファイルまたはフォルダー]** セクションで、 **[参照]** を選択して **source** フォルダーを選択し、 **[OK]** を選択します。

    1. **[File loading behavior]\(ファイル読み込み動作\)** で、 **[Incremental load: LastModifiedDate]\(増分読み込み: LastModifiedDate\)** を選択して、 **[Binary copy]\(バイナリ コピー\)** を選択します。
    
    1. **[次へ]** を選びます。

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png" alt-text="[ソース データ ストア] ページを示すスクリーンショット。":::

4. **[Destination data store]\(コピー先データ ストア\)** ページで、次の手順のようにします。
    1. 作成した **AzureBlobStorage** 接続を選択します。 これは、コピー元データ ストアと同じストレージ アカウントです。

    1. **[フォルダー パス]** セクションで、**destination** フォルダーを見つけて選択し、 **[OK]** を選択します。

    1. **[次へ]** を選びます。

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page.png" alt-text="[Destination data store]\(コピー先データ ストア\) ページを示すスクリーンショット。":::

5. **[設定]** ページの **[タスク名]** に「**DeltaCopyFromBlobPipeline**」と入力し、 **[次へ]** を選択します。 指定したタスク名のパイプラインが Data Factory によって作成されます。

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png" alt-text="[設定] ページを示すスクリーンショット。":::

6. **[Summary]\(概要\)** ページで設定を確認し、 **[次へ]** を選択します。

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png" alt-text="[概要] ページ":::

7. **[Deployment]\(デプロイ\)** ページで **[監視]** を選択してパイプライン (タスク) を監視します。

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png" alt-text="[Deployment]\(デプロイ\) ページ":::

8. 左側の **[監視]** タブが自動的に選択されたことがわかります。 アプリケーションの **[監視]** タブに切り替えます。パイプラインの状態が表示されます。 **[最新の情報に更新]** を選択して、一覧を更新します。 **[パイプライン名]** の下にあるリンクを選択して、アクティビティの実行の詳細を表示するか、パイプラインを再実行します。

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs-1.png" alt-text="リストを最新の情報に更新してアクティビティの実行の詳細を表示する":::

9. パイプラインにはアクティビティ (コピー アクティビティ) が 1 つしかないため、エントリが 1 つだけ表示されます。 コピー操作の詳細を確認するには、 **[アクティビティの実行]** ページで、 **[アクティビティ名]** 列の **[詳細]** リンク (眼鏡アイコン) を選択します。 プロパティの詳細については、[コピー アクティビティの概要](copy-activity-overview.md)に関するページを参照してください。

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png" alt-text="パイプラインのコピー アクティビティ":::

    BLOB ストレージ アカウントの source コンテナーにはファイルがないので、そのアカウントの destination コンテナーにコピーされたファイルは表示されません。

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png" alt-text="source コンテナーまたは destination コンテナーにファイルがない":::

10. 空のテキスト ファイルを作成し、**file1.txt** という名前を付けます。 このテキスト ファイルを、ストレージ アカウントの source コンテナーにアップロードします。 この作業は、[Azure Storage Explorer](https://storageexplorer.com/) をはじめとするさまざまなツールを使用して実行できます。

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png" alt-text="file1.txt を作成して source コンテナーにアップロードする":::

11. **[パイプラインの実行]** ビューに戻るには、 **[アクティビティの実行]** ページの階層リンク メニューで **[すべてのパイプラインの実行]** を選択し、同じパイプラインが再び自動的にトリガーされるまで待ちます。  

12. 2 番目のパイプラインの実行が完了したら、前述と同じ手順に従って、アクティビティの実行の詳細を確認します。  

    BLOB ストレージ アカウントの source コンテナーから destination コンテナーに 1 つのファイル (file1.txt) がコピーされたことが表示されます。

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png" alt-text="file1.txt が source コンテナーから desination コンテナーにコピーされた":::

13. 空のテキスト ファイルをもう 1 つ作成し、**file2.txt** という名前を付けます。 このテキスト ファイルを BLOB ストレージ アカウントの source コンテナーにアップロードします。

14. 2 つ目のテキスト ファイルに対してステップ 11 と 12 を繰り返します。 このパイプライン実行中に、ストレージ アカウントの source コンテナーから destination コンテナーに新しいファイル (file2.txt) だけがコピーされたことがわかります。  

    [Azure Storage Explorer](https://storageexplorer.com/) を使用してファイルをスキャンすることにより、1 つのファイルだけがコピーされたことを確認することもできます。

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png" alt-text="Azure Storage Explorer を使用してファイルをスキャンする":::

## <a name="next-steps"></a>次のステップ
次のチュートリアルに進み、Azure 上の Apache Spark クラスターを使ってデータを変換する方法を学習します。

> [!div class="nextstepaction"]
>[Apache Spark クラスターを使用したクラウドのデータの変換に関するページ](tutorial-transform-data-spark-portal.md)
