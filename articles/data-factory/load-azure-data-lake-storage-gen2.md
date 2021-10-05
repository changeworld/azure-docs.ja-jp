---
title: Azure Data Lake Storage Gen2 にデータを読み込む
description: Azure Data Factory を使用して Azure Data Lake Storage Gen2 にデータをコピーします
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/22/2021
ms.openlocfilehash: 4ed42f5ea6aa5acda8234aab31b26c99057ed6b9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819989"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Azure Data Factory を使用して Azure Data Lake Storage Gen2 にデータを読み込む

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 は、ビッグ データ分析専用の機能セットであり、[Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)に組み込まれます。 ファイル システムとオブジェクト ストレージの両方のパラダイムを使用して、データと連携させることができます。

Azure Data Factory (ADF) は、フル マネージドのクラウドベース データ統合サービスです。 このサービスを使用して、オンプレミスとクラウドベースのデータストアの豊富なセットからのデータをレイクに入力し、分析ソリューションをビルドする際の時間を節約できます。 サポートされるコネクタの詳細な一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。

Azure Data Factory では、スケール アウトしたマネージド データ移動ソリューションを提供しています。 ADF のスケールアウト アーキテクチャにより、高スループットでデータを取り込むことができます。 詳しくは、[コピー アクティビティのパフォーマンス](copy-activity-performance.md)に関する記事をご覧ください。

この記事では、Data Factory のデータのコピー ツールを使用して "_アマゾン ウェブ サービスの S3 サービス_" から _Azure Data Lake Storage Gen2_ にデータを読み込む方法を示します。 その他の種類のデータ ストアからデータをコピーする場合も、同様の手順で実行できます。

>[!TIP]
>Azure Data Lake Storage Gen1 から Gen2 へのデータのコピーについては、[こちらのチュートリアル](load-azure-data-lake-storage-gen2-from-gen1.md)を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* Data Lake Storage Gen2 が有効な Azure Storage アカウント:ストレージ アカウントがない場合、[作成します](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。
* データが含まれる S3 バケットを持つ AWS アカウント:この記事では、Amazon S3 からデータをコピーする方法を示します。 同様の手順に従うことによって、その他のデータ ストアも使用できます。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. 左側のメニューで、 **[リソースの作成]**  >  **[統合]**  >  **[Data Factory]** を選択します。
   
   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="[新規] ペインでの Data Factory の選択":::

2. **[新しいデータ ファクトリ]** ページで、次のフィールドの値を指定します。
 
    * **Name**:Azure Data Factory のグローバルに一意の名前を入力します。 "データ ファクトリ名 *YourDataFactoryName* は利用できません" エラーが発生する場合は、データ ファクトリの別の名前を入力します。 たとえば、_**yourname**_**ADFTutorialDataFactory** という名前を使用できます。 データ ファクトリをもう一度作成してみます。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事をご覧ください。
    * **サブスクリプション**:データ ファクトリを作成する Azure サブスクリプションを選択します。 
    * **リソース グループ**:ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** オプションを選択し、リソース グループの名前を入力します。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。  
    * **バージョン**: **[V2]** を選択します。
    * **[場所]** :データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストアは、他の場所やリージョンにあってもかまいません。 

3. **［作成］** を選択します

4. 作成が完了したら、データ ファクトリに移動します。 次の画像のように **[データ ファクトリ]** ホーム ページが表示されます。 
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="[Open Azure Data Factory Studio] タイルを含む、Azure Data Factory のホーム ページ。":::

   **[Open Azure Data Factory Studio]\(Azure Data Factory Studio を開く\)** タイルで **[開く]** を選択して、別のタブでデータ統合アプリケーションを起動します。

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 にデータを読み込む

1. Azure Data Factory のホーム ページで、 **[取り込み]** タイルを選択し、データのコピー ツールを起動します。

2. **[プロパティ]** ページで、 **[タスクの種類]** の **[Built-in copy task]\(組み込みコピー タスク\)** を選択して、 **[Task cadence or task schedule]\(タスクの周期またはタスクのスケジュール\)** の **[Run once now]\(今すぐ 1 度だけ実行する\)** を選択し、 **[次へ]** を選択します。

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png" alt-text="[プロパティ] ページ":::
3. **[ソース データ ストア]** ページで、次の手順を実行します。
    1. **[+ 新しい接続]** を選択します。 コネクタ ギャラリーから **[Amazon S3]** を選択し、 **[続行]** を選択します。
    
        :::image type="content" source="./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png" alt-text="ソース データ ストアの S3 ページ":::
    
    1. **[New connection (Amazon S3)]\(新しい接続 (Amazon S3)\)** ページで、次の手順のようにします。

        1. **[アクセス キー ID]** の値を指定します。
        1. **[シークレット アクセス キー]** の値を指定します。
        1. **[テスト接続]** を選択して設定を検証し、 **[作成]** を選択します。
    
          :::image type="content" source="./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png" alt-text="Amazon S3 アカウントの指定":::

    1. **[ソース データ ストア]** ページで、新しく作成した Amazon S3 接続が、 **[接続]** ブロックで選択されていることを確認します。 
    1. **[ファイルまたはフォルダー]** セクションで、コピーするフォルダーとファイルを参照します。 フォルダーまたはファイルを選択して、 **[OK]** を選択します。
    1. **[再帰的]** オプションと **[バイナリ コピー]** オプションをオンにすることで、コピーの動作を指定します。 **[次へ]** を選択します。

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/source-data-store.png" alt-text="[ソース データ ストア] ページを示すスクリーンショット。":::
    
4. **[Destination data store]\(コピー先データ ストア\)** ページで、次の手順のようにします。
    1. **[+ 新しい接続]** を選択し、 **[Azure Data Lake Storage Gen2]** を選択して、 **[続行]** を選択します。

        :::image type="content" source="./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png" alt-text="[Destination data store]\(コピー先データ ストア\) ページ":::
    
    1. **[新しい接続 (Azure Data Lake Storage Gen2)]** ページで、[ストレージ アカウント名] ドロップダウンの一覧から Data Lake Storage Gen2 対応のアカウントを選択し、 **[作成]** を選択して接続を作成します。 

        :::image type="content" source="./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png" alt-text="Azure Data Lake Storage Gen2 アカウントを指定する":::

    1. **[Destination data store]\(コピー先データ ストア\)** ページの **[接続]** ブロックで、新しく作成した接続を選択します。 **[フォルダー パス]** で、出力フォルダー名として「**copyfroms3**」と入力し、 **[次へ]** を選択します。 対応する ADLS Gen2 ファイル システムとサブ フォルダーが存在しない場合は、コピー中に ADF によって作成されます。

        :::image type="content" source="./media/load-azure-data-lake-storage-gen2/destiantion-data-store.png" alt-text="[Destination data store]\(コピー先データ ストア\) ページを示すスクリーンショット。":::   
    
5. **[設定]** ページで、 **[タスク名]** フィールドに「**CopyFromAmazonS3ToADLS**」と指定し、 **[次へ]** を選択して既定の設定を使用します。

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/copy-settings.png" alt-text="設定ページ":::

6. **[サマリー]** ページで設定を確認し、 **[次へ]** を選択します。

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/copy-summary.png" alt-text="[概要] ページ":::

7. **[Deployment]\(デプロイ\)** ページで **[監視]** を選択してパイプライン (タスク) を監視します。 
 
8. パイプラインの実行が正常に完了すると、手動トリガーによってトリガーされたパイプラインの実行が表示されます。 **[パイプライン名]** 列のリンクを使用して、アクティビティの詳細を表示したりパイプラインを再実行したりできます。

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png" alt-text="パイプラインの実行を監視する":::

9. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 **[パイプライン名]** 列の **CopyFromAmazonS3ToADLS** リンクを選択します。 コピー操作の詳細を確認するには、 **[アクティビティ名]** 列の **[詳細]** リンク (眼鏡アイコン) を選択します。 ソースからシンクにコピーされるデータのボリューム、データのスループット、実行ステップと対応する期間、使用される構成などの詳細を監視できます。
 
    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png" alt-text="アクティビティの実行を監視する":::
    
    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png" alt-text="アクティビティの実行状況の詳細の監視":::

10. 表示を更新するには、 **[最新の情報に更新]** を選択します。 [パイプラインの実行] ビューに戻るには、一番上にある **[すべてのパイプラインの実行]** を選択します。

11. データが Data Lake Storage Gen2 アカウントにコピーされたことを確認します。

## <a name="next-steps"></a>次のステップ

* [コピー アクティビティの概要](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2 コネクタ](connector-azure-data-lake-storage.md)
