---
title: 時間でパーティション分割されたファイル名に基づいて新しいファイルを増分コピーする
description: Azure データ ファクトリを作成してから、データのコピー ツールを使用して、時間でパーティション分割されたファイル名のみに基づく新しいファイルを増分的に読み込みます。
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2021
ms.openlocfilehash: f2fe1e854027cb6fd15b6d0e2e659cc62528a2e2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757859"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>データのコピー ツールを使用することにより、パーティション分割されたファイル名に基づく新しいファイルを増分コピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

このチュートリアルでは、Azure Portal を使用してデータ ファクトリを作成します。 次に、時間でパーティション分割されたファイル名に基づく新しいファイルを Azure Blob Storage から Azure Blob Storage に増分コピーするパイプラインを、データのコピー ツールを使用して作成します。

> [!NOTE]
> Azure Data Factory を初めて使用する場合は、「[Azure Data Factory の概要](introduction.md)」を参照してください。

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * データのコピー ツールを使用してパイプラインを作成します。
> * パイプラインとアクティビティの実行を監視します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* **Azure ストレージ アカウント**:Blob Storage を "_ソース_" および "_シンク_" データ ストアとして使用します。 Azure ストレージ アカウントがない場合は、「[ストレージ アカウントの作成](../storage/common/storage-account-create.md)」の手順をご覧ください。

### <a name="create-two-containers-in-blob-storage"></a>Blob ストレージに 2 つのコンテナーを作成する

次の手順を行って、チュートリアルで使用する Blob ストレージを準備します。

1. **source** という名前のコンテナーを作成します。  自分のコンテナー内に、**2021/07/15/06** というフォルダー パスを作成します。 空のテキスト ファイルを作成し、**file1.txt** という名前を付けます。 自分のストレージ アカウントのフォルダー パス **source/2021/07/15/06** に、file1.txt をアップロードします。  この作業は、[Azure Storage Explorer](https://storageexplorer.com/) をはじめとするさまざまなツールを使用して実行できます。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png" alt-text="[ファイルのアップロード]":::

    > [!NOTE]
    > フォルダー名は、UTC 時間に合わせて調整してください。  たとえば、現在の UTC 時刻が 2021 年 7 月 15 日午前 6 時 10 分である場合は、**source/{Year}/{Month}/{Day}/{Hour}/** のルールに従って、**source/2021/07/15/06/** というフォルダー パスを作成できます。

2. **destination** という名前のコンテナーを作成します。 この作業は、[Azure Storage Explorer](https://storageexplorer.com/) をはじめとするさまざまなツールを使用して実行できます。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. 左側のメニューで、 **[リソースの作成]**  >  **[統合]**  >  **[Data Factory]** を選択します。

   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="&quot;[新規]&quot; ペインでの Data Factory の選択":::

2. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。

    データ ファクトリの名前は "_グローバルに一意_" にする必要があります。 次のエラー メッセージが表示される場合があります。

   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="重複する名前に関する、新しいデータ ファクトリのエラー メッセージ。":::

   データ ファクトリの名前の値に関するエラー メッセージが表示された場合は、別の名前を入力してください。 たとえば、_**yourname**_**ADFTutorialDataFactory** という名前を使用します。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事をご覧ください。
3. 新しいデータ ファクトリの作成先となる Azure **サブスクリプション** を選択します。
4. **[リソース グループ]** で、次の手順のいずれかを行います。

    a. **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。

    b. **[新規作成]** を選択し、リソース グループの名前を入力します。 
         
    リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。

5. **[バージョン]** で、バージョンとして **[V2]** を選択します。
6. **[場所]** で、データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストア (Azure Storage、SQL Database など) やコンピューティング (Azure HDInsight など) は、他の場所やリージョンに存在していてもかまいません。
7. **［作成］** を選択します
8. 作成が完了すると、 **[Data Factory]** ホーム ページが表示されます。
9. 別のタブで Azure Data Factory ユーザー インターフェイス (UI) を起動するには、 **[Open Azure Data Factory Studio]\(Azure Data Factory Studio を開く\)**  タイルで **[開く]** を選択します。

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="[Open Azure Data Factory Studio] タイルを含む、Azure Data Factory のホーム ページ。":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>データのコピー ツールを使用してパイプラインを作成する

1. Azure Data Factory のホーム ページで **[取り込む]** タイトルを選択して、データ コピー ツールを起動します。

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="ADF のホーム ページを示すスクリーンショット。":::

2. **[プロパティ]** ページで、次の手順を行います。
    1. **[タスクの種類]** で **[Built-in copy task]\(組み込みコピー タスク\)** を選択します。

    1. **[Task cadence or task schedule]\(タスクの周期またはタスクのスケジュール\)** で、 **[Tumbling window]\(タンブリング ウィンドウ\)** を選択します。

    1. **[Recurrence]\(繰り返し\)** で、**1 時間** と指定します。

    1. **[次へ]** を選択します。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png" alt-text="[プロパティ] ページ":::
3. **[ソース データ ストア]** ページで、次の手順を実行します。

    a. **[+ 新しい接続]** を選択して、接続を追加します。
    
    b. ギャラリーから **[Azure Blob Storage]** を選択し、 **[続行]** を選択します。
    
    c. **[新しい接続 (Azure Blob Storage)]** ページで、接続の名前を入力します。 Azure サブスクリプションを選択し、 **[ストレージ アカウント名]** 一覧からご利用のストレージ アカウントを選びます。 接続をテストし、 **[作成]** を選択します。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-connection.png" alt-text="[ソース データ ストア] ページ":::

    d. **[ソース データ ストア]** ページの **[接続]** セクションで、新しく作成した接続を選択します。

    e. **[ファイルまたはフォルダー]** セクションで、**source** コンテナーを見つけて選択し、 **[OK]** を選択します。

    f. **[File loading behavior]\(ファイル読み込み動作\)** で、 **[Incremental load: time-partitioned folder/file names]\(段階的に読み込み: 時間でパーティション分割されたフォルダー/ファイル名\)** を選択します。

    g. 動的フォルダー パスを **source/{year}/{month}/{day}/{hour}/** として入力し、次のスクリーンショットに示すように形式を変更します。 
    
    h. **[Binary copy]\(バイナリ コピー\)** をオンにして、**[次へ]** を選択します。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png" alt-text="[ソース データ ストア] ページの構成を示すスクリーンショット。":::


4. **[コピー先データ ストア]** ページで、次の手順を実行します。
    1. データ ソース ストアと同じストレージ アカウントである **AzureBlobStorage** を選択します。

    1. **destination** フォルダーを見つけて選択し、 **[OK]** を選択します。

    1. 動的フォルダー パスを **destination/{year}/{month}/{day}/{hour}/** として入力し、次のスクリーンショットに示すように形式を変更します。

    1. **[次へ]** を選びます。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store.png" alt-text="[Destination data store]\(コピー先データ ストア\) ページの構成を示すスクリーンショット。":::

5. **[設定]** ページの **[タスク名]** に「**DeltaCopyFromBlobPipeline**」と入力し、 **[次へ]** を選択します。 指定したタスク名のパイプラインが Data Factory UI によって作成されます。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png" alt-text="設定ページの構成を示すスクリーンショット。":::

6. **[サマリー]** ページで設定を確認し、 **[次へ]** を選択します。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png" alt-text="[概要] ページ":::

7. **[Deployment]\(デプロイ\)** ページで **[監視]** を選択してパイプライン (タスク) を監視します。
    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png" alt-text="[Deployment]\(デプロイ\) ページ":::

8. 左側の **[監視]** タブが自動的に選択されたことがわかります。  パイプラインの実行が自動的にトリガーされるまで待機する必要があります (約 1 時間後)。 実行時に、パイプライン名のリンク **DeltaCopyFromBlobPipeline** を選択してアクティビティの実行の詳細を表示するか、パイプラインを再実行します。 **[最新の情報に更新]** を選択して、一覧を更新します。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs-1.png" alt-text="スクリーンショットに、[パイプライン実行] ウィンドウが示されています。":::
9. パイプライン内のアクティビティ (コピー アクティビティ) は 1 つだけなので、エントリは 1 つのみです。 **[ソース]** および **[ターゲット]** 列の列幅を (必要に応じて) 調整し、詳細が表示されるようにすると、ソース ファイル (file1.txt) が *source/2021/07/15/06/* から、同じファイル名の *destination/2021/07/15/06/* にコピーされていることがわかります。 

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png" alt-text="スクリーンショットに、パイプライン実行の詳細が示されています。":::

    Azure Storage Explorer (https://storageexplorer.com/) ) を使用してファイルをスキャンしても、同じことを確認できます。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png" alt-text="スクリーンショットに、宛先のパイプライン実行の詳細が示されています。":::

10. 別の空のテキスト ファイルを作成し、**file2.txt** という新しい名前を付けます。 自分のストレージ アカウントのフォルダー パス **source/2021/07/15/07** に、file2.txt ファイルをアップロードします。 この作業は、[Azure Storage Explorer](https://storageexplorer.com/) をはじめとするさまざまなツールを使用して実行できます。

    > [!NOTE]
    > 新しいフォルダー パスを作成する必要がないか注意してください。 フォルダー名は、UTC 時間に合わせて調整してください。  たとえば、現在の UTC 時刻が 2021 年 7 月 15 日午前 7 時 30 分の場合、 **source/{Year}/{Month}/{Day}/{Hour}/** のルールに従って、**source/2021/07/15/07/** というフォルダー パスを作成できます。

11. **[パイプラインの実行]** ビューに戻るには、 **[All Pipelines Runs]\(すべてのパイプライン実行\)** を選択し、さらに 1 時間後に同じパイプラインが再び自動的にトリガーされるまで待ちます。  

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png" alt-text="スクリーンショットに、そのページに戻るための [すべてのパイプライン実行] リンクが示されています。":::

12. 2 つ目のパイプライン実行については、新しい **DeltaCopyFromBlobPipeline** を選択し、同じことを行って詳細を確認します。 ソース ファイル (file2.txt) が **source/2021/07/15/07/** から、同じファイル名の **destination/2021/07/15/07/** にコピーされたことがわかります。 Azure Storage Explorer (https://storageexplorer.com/) ) を使用して **destination** コンテナー内でファイルをスキャンしても、同じことを確認できます。


## <a name="next-steps"></a>次のステップ
次のチュートリアルに進み、Azure 上の Spark クラスターを使ってデータを変換する方法について学習しましょう。

> [!div class="nextstepaction"]
>[クラウド内の Spark クラスターを使用してデータを変換する](tutorial-transform-data-spark-portal.md)
