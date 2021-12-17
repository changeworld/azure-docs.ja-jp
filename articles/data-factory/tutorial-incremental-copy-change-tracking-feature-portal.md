---
title: Azure portal を使用して Change Tracking を使用してデータを増分コピーする
description: このチュートリアルでは、Azure SQL Database のソース データベースから変更追跡情報に基づく差分データを Azure Blob Storage に読み込むパイプラインを使用して Azure Data Factory を作成します。
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 07/05/2021
ms.openlocfilehash: c3770c9d0d9e051417ca160e454976599f3bb334
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771747"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information-using-the-azure-portal"></a>Azure portal を使用して、変更追跡情報を使用して Azure SQL Database から Azure Blob Storage にデータを増分読み込みする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

このチュートリアルでは、Azure SQL Database のソース データベースから **変更追跡** 情報に基づく差分データを Azure Blob Storage に読み込むパイプラインを使用して Azure Data Factory を作成します。  

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * ソース データ ストアを準備します。
> * データ ファクトリを作成します。
> * リンクされたサービスを作成します。
> * ソース、シンク、変更追跡の各データセットを作成します。
> * フル コピー パイプラインを作成、実行、監視します。
> * ソース テーブルのデータを追加または更新します。
> * 増分コピー パイプラインを作成、実行、監視します。

## <a name="overview"></a>概要
データ統合ソリューションでは、初回データ読み込みの後、増分データを読み込む手法が広く利用されています。 ソース データ ストアから特定の期間に変更されたデータを簡単に切り出すことができる場合もあります (LastModifyTime、CreationTime など)。 一方、前回データを処理した時点からの差分データを明示的に特定する方法がない場合もあります。 こうした差分データは、Azure SQL Database や SQL Server などのデータ ストアでサポートされる Change Tracking テクノロジを使用して特定することができます。  このチュートリアルでは、Azure Data Factory と SQL Change Tracking テクノロジを使用して、Azure SQL Database から Azure Blob Storage に差分データを増分読み込みする方法について説明します。  SQL Change Tracking テクノロジに関するより具体的な情報については、[SQL Server における変更の追跡](/sql/relational-databases/track-changes/about-change-tracking-sql-server)に関するページを参照してください。

## <a name="end-to-end-workflow"></a>エンド ツー エンド ワークフロー
ここでは、Change Tracking テクノロジを使用してデータを増分読み込みする一般的なエンド ツー エンドのワークフロー ステップを取り上げます。

> [!NOTE]
> Azure SQL Database と SQL Server は、どちらも Change Tracking テクノロジをサポートしています。 このチュートリアルでは、Azure SQL Database をソース データ ストアとして使用します。 SQL Server インスタンスを使用してもかまいません。

1. **履歴データの初回読み込みを実行する** (1 回実行)。
    1. Azure SQL Database のソース データベースの Change Tracking テクノロジを有効にします。
    2. データベースから SYS_CHANGE_VERSION の初期値をベースラインとして取得し、変更済みデータをキャプチャします。
    3. ソース データベースから Azure Blob Storage にフル データを読み込みます。
2. **スケジュールに従って差分データの増分読み込みを実行する** (データの初回読み込み後に定期的に実行)。
    1. 新旧の SYS_CHANGE_VERSION 値を取得します。
    3. 差分データを読み込みます。これは、**sys.change_tracking_tables** から得られる変更済みの行 (2 つの SYS_CHANGE_VERSION 値の間にある行) の主キーと **ソース テーブル** 内のデータとを結合した後、その差分データをターゲットに移動することによって行います。
    4. 次回の差分読み込みに備えて SYS_CHANGE_VERSION を更新します。

## <a name="high-level-solution"></a>ソリューションの概略
このチュートリアルでは、次の 2 つの操作を実行する 2 つのパイプラインを作成します。  

1. **初回読み込み:** ソース データ ストア (Azure SQL Database) からターゲット データ ストア (Azure Blob Storage) にデータ全体をコピーするコピー アクティビティを含んだパイプラインを作成します。

    :::image type="content" source="media/tutorial-incremental-copy-change-tracking-feature-portal/full-load-flow-diagram.png" alt-text="データ全体の読み込み":::
1.  **増分読み込み:** 次のアクティビティを含んだパイプラインを作成して定期的に実行します。
    1. **2 つのルックアップ アクティビティ** を作成します。これらのアクティビティによって Azure SQL Database から新旧の SYS_CHANGE_VERSION を取得してコピー アクティビティに渡すことになります。
    2. 2 つの SYS_CHANGE_VERSION 値の間に存在する挿入/更新/削除されたデータを Azure SQL Database から Azure Blob Storage にコピーする **コピー アクティビティを 1 つ** 作成します。
    3. 次回のパイプライン実行に備えて SYS_CHANGE_VERSION の値を更新する **ストアド プロシージャ アクティビティを 1 つ** 作成します。

    :::image type="content" source="media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-load-flow-diagram.png" alt-text="増分読み込みのフロー図":::


Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件
* **Azure SQL データベース**。 **ソース** データ ストアとして使うデータベースです。 Azure SQL Database のデータベースがない場合の作成手順については、[Azure SQL Database のデータベースの作成](../azure-sql/database/single-database-create-quickstart.md)に関する記事を参照してください。
* **Azure Storage アカウント**。 **シンク** データ ストアとして使用する BLOB ストレージです。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「[ストレージ アカウントの作成](../storage/common/storage-account-create.md)」を参照してください。 **adftutorial** という名前のコンテナーを作成します。 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Azure SQL Database にデータ ソース テーブルを作成する

1. **SQL Server Management Studio** を起動し、SQL Database に接続します。
2. **サーバー エクスプローラー** で目的の **データベース** を右クリックして **[新しいクエリ]** を選択します。
3. データベースに対して次の SQL コマンドを実行し、`data_source_table` という名前のテーブルをソース データ ストアとして作成します。  

    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```

4. 次の SQL クエリを実行して、データベースとソース テーブル (data_source_table) の **Change Tracking** 機構を有効にします。

    > [!NOTE]
    > - &lt;your database name&gt; は、data_source_table がある Azure SQL Database のデータベースの名前に置き換えてください。
    > - 現行の例では、変更済みのデータが 2 日間維持されます。 変更済みデータを読み込む間隔を 3 日おき、またはそれ以上にした場合、変更済みデータの一部が読み込まれません。  CHANGE_RETENTION の数値を増やす必要があります。 または、変更済みデータの読み込み間隔を必ず 2 日以内としてください。 詳細については、「[データベースの変更の追跡を有効にする](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database)」を参照してください。

    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  

    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. 新しいテーブルを作成し、ChangeTracking_version を既定値で格納するために、次のクエリを実行します。

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```

    > [!NOTE]
    > SQL Database の変更追跡を有効にした後、データが変更されていなければ、変更追跡バージョンの値は 0 になります。
6. 次のクエリを実行して、データベースにストアド プロシージャを作成します。 このストアド プロシージャをパイプラインで呼び出すことによって、前の手順で作成したテーブルの変更追跡バージョンを更新します。

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS

    BEGIN

    UPDATE table_store_ChangeTracking_version
    SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName

    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-Az-ps)に関するページの手順に従って、最新の Azure PowerShell モジュールをインストールしてください。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
1. 左側のメニューで、 **[リソースの作成]**  >  **[データ + 分析]**  >  **[Data Factory]** の順に選択します。

   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png" alt-text="&quot;[新規]&quot; ペインでの Data Factory の選択":::

2. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。

     :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory.png" alt-text="[新しいデータ ファクトリ] ページ":::

   Azure Data Factory の名前は、**グローバルに一意** にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (yournameADFTutorialDataFactory など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。

   *データ ファクトリ名 "ADFTutorialDataFactory" は利用できません*
3. データ ファクトリを作成する Azure **サブスクリプション** を選択します。
4. **[リソース グループ]** について、次の手順のいずれかを行います。

      - **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。
      - **[新規作成]** を選択し、リソース グループの名前を入力します。   
         
        リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。  
4. **バージョン** として **[V2 (プレビュー)]** を選択します。
5. データ ファクトリの **場所** を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。
6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **Create** をクリックしてください。      
8. ダッシュボードに、 **[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。

    :::image type="content" source="media/tutorial-incremental-copy-change-tracking-feature-portal/deploying-data-factory.png" alt-text="[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル":::
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。

   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="[Open Azure Data Factory Studio] タイルを含む、Azure Data Factory のホーム ページ。":::

10. **[Open Azure Data Factory Studio]\(Azure Data Factory Studio を開く\)** タイルで **[開く]** を選択して、別のタブで Azure Data Factory ユーザー インターフェイス (UI) を起動します。
11. ホーム ページで、次の図に示すように、左パネルの **[管理]** タブに切り替えます。

    :::image type="content" source="media/doc-common-process/get-started-page-manage-button.png" alt-text="[管理] ボタンを示すスクリーンショット。":::

## <a name="create-linked-services"></a>リンクされたサービスを作成します
データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このセクションでは、Azure ストレージ アカウントと Azure SQL Database のデータベースに対するリンクされたサービスを作成します。

### <a name="create-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する
この手順では、Azure ストレージ アカウントをデータ ファクトリにリンクします。

1. **[接続]** をクリックし、 **[+ 新規]** をクリックします。

   :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/new-connection-button-storage.png" alt-text="新しい接続ボタン":::
2. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで **[Azure Blob Storage]** を選択し、 **[続行]** をクリックします。

   :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-storage.png" alt-text="[Azure Blob Storage] の選択":::
3. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。

    1. **[名前]** に「**AzureStorageLinkedService**」と入力します。
    2. **[ストレージ アカウント名]** で、使用する Azure ストレージ アカウントを選択します。
    3. **[保存]** をクリックします。

   :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-storage-linked-service-settings.png" alt-text="Azure Storage アカウントの設定":::


### <a name="create-azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービスを作成する
この手順では、データベースをデータ ファクトリにリンクします。

1. **[接続]** をクリックし、 **[+ 新規]** をクリックします。
2. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで **[Azure SQL Database]** を選択し、 **[続行]** をクリックします。
3. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。

    1. **[名前]** フィールドに「**AzureSqlDatabaseLinkedService**」と入力します。
    2. **[サーバー名]** フィールドでサーバーを選択します。
    3. **[データベース名]** フィールドでデータベースを選択します。
    4. **[ユーザー名]** フィールドにユーザーの名前を入力します。
    5. **[パスワード]** フィールドに、ユーザーのパスワードを入力します。
    6. **[テスト接続]** をクリックして接続をテストします。
    7. **[保存]** をクリックして、リンクされたサービスを保存します。

       :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-sql-database-linked-service-settings.png" alt-text="Azure SQL Database のリンクされたサービスの設定":::

## <a name="create-datasets"></a>データセットを作成する
この手順では、データのコピー元とコピー先、 SYS_CHANGE_VERSION の格納場所を表すデータセットを作成します。

### <a name="create-a-dataset-to-represent-source-data"></a>ソース データを表すデータセットを作成する
この手順では、ソース データを表すデータセットを作成します。

1. ツリー ビューで **[+]\(プラス記号\)** をクリックし、 **[データセット]** をクリックします。

   :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png" alt-text="[新しいデータセット] メニュー":::
2. **[Azure SQL Database]** を選択し、 **[完了]** をクリックします。

   :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-sql-database.png" alt-text="ソース データセットの種類 - Azure SQL Database":::
3. データセットを構成するための新しいタブが表示されます。 ツリー ビューにもデータセットが表示されます。 **[プロパティ]** ウィンドウで、データセットの名前を「**SourceDataset**」に変更します。

   :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-name.png" alt-text="ソース データセット名":::    
4. **[接続]** タブに切り替えて、次の手順を実行します。

    1. **[リンクされたサービス]** で **[AzureSqlDatabaseLinkedService]** を選択します。
    2. **[テーブル]** で **[dbo].[data_source_table]** を選択します。

   :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-connection.png" alt-text="ソースの接続":::

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>シンク データ ストアにコピーされるデータを表すデータセットを作成します。
この手順では、ソース データ ストアからコピーされたデータを表すデータセットを作成します。 前提条件の 1 つとして adftutorial コンテナーを Azure Blob Storage に作成しました。 このコンテナーが存在しない場合は作成するか、または既存のコンテナーの名前に設定してください。 このチュートリアルでは、`@CONCAT('Incremental-', pipeline().RunId, '.txt')` という式を使って出力ファイル名が動的に生成されます。

1. ツリー ビューで **[+]\(プラス記号\)** をクリックし、 **[データセット]** をクリックします。

   :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png" alt-text="[新しいデータセット] メニュー":::
2. **[Azure Blob Storage]** を選択し、 **[完了]** をクリックします。

   :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-type.png" alt-text="シンク データセットの種類 - Azure Blob Storage":::
3. データセットを構成するための新しいタブが表示されます。 ツリー ビューにもデータセットが表示されます。 **[プロパティ]** ウィンドウで、データセットの名前を「**SinkDataset**」に変更します。

   :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-name.png" alt-text="シンク データセット - 名前":::
4. プロパティ ウィンドウで **[接続]** タブに切り替え、以下の手順を実行します。

    1. **[リンクされたサービス]** で **[AzureStorageLinkedService]** を選択します。
    2. **filePath** の **フォルダー** 部分として「**adftutorial/incchgtracking**」と入力します。
    3. **filePath** の **ファイル** 部分として「 **\@CONCAT('Incremental-', pipeline().RunId, '.txt')** 」と入力します。  

       :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-connection.png" alt-text="シンク データセット - 接続":::

### <a name="create-a-dataset-to-represent-change-tracking-data"></a>変更追跡データを表すデータセットを作成する
この手順では、変更追跡バージョンを格納するためのデータセットを作成します。  前提条件の 1 つとして table_store_ChangeTracking_version テーブルを作成しました。

1. ツリー ビューで **[+]\(プラス記号\)** をクリックし、 **[データセット]** をクリックします。
2. **[Azure SQL Database]** を選択し、 **[完了]** をクリックします。
3. データセットを構成するための新しいタブが表示されます。 ツリー ビューにもデータセットが表示されます。 **[プロパティ]** ウィンドウで、データセットの名前を「**ChangeTrackingDataset**」に変更します。
4. **[接続]** タブに切り替えて、次の手順を実行します。

    1. **[リンクされたサービス]** で **[AzureSqlDatabaseLinkedService]** を選択します。
    2. **[テーブル]** で **[dbo].[table_store_ChangeTracking_version]** を選択します。

## <a name="create-a-pipeline-for-the-full-copy"></a>完全コピーのパイプラインを作成する
この手順では、ソース データ ストア (Azure SQL Database) からターゲット データ ストア (Azure Blob Storage) にデータ全体をコピーするコピー アクティビティを含んだパイプラインを作成します。

1. 左ウィンドウで **[+]\(プラス記号\)** をクリックし、 **[パイプライン]** をクリックします。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu.png" alt-text="データ ファクトリの [パイプライン] オプションを示すスクリーンショット。":::
2. パイプラインを構成するための新しいタブが表示されます。 ツリー ビューにもパイプラインが表示されます。 **[プロパティ]** ウィンドウで、パイプラインの名前を「**FullCopyPipeline**」に変更します。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-name.png" alt-text="名前が入力されたパイプラインを示すスクリーンショット。":::
3. **[アクティビティ]** ツールボックスで **[データ フロー]** を展開し、パイプライン デザイナー画面に **[コピー]** アクティビティをドラッグ アンド ドロップして、名前を「**FullCopyActivity**」に設定します。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-activity-name.png" alt-text="完全コピー アクティビティ名":::
4. **[ソース]** タブに切り替えて、 **[Source Dataset]\(ソース データセット\)** フィールドで **[SourceDataset]** を選択します。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-source.png" alt-text="コピー アクティビティ - ソース":::
5. **[シンク]** タブに切り替えて、 **[Sink Dataset]\(シンク データセット\)** フィールドで **[SinkDataset]** を選択します。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-sink.png" alt-text="コピー アクティビティ - シンク":::
6. パイプライン定義を検証するために、ツール バーの **[検証]** をクリックします。 検証エラーがないことを確認します。 **[>>]** をクリックして、 **[Pipeline Validation Report]\(パイプライン検証レポート\)** を閉じます。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-validate.png" alt-text="パイプラインを検証する":::
7. エンティティ (リンクされたサービス、データセット、およびパイプライン) を発行するには、 **[発行]** をクリックします。 発行が成功するまで待機します。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button.png" alt-text="[Publish All]\(すべて発行\) ボタンが呼び出された、データ ファクトリを示すスクリーンショット。":::
8. **[正常に発行されました]** というメッセージが表示されるまで待機します。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/publishing-succeeded.png" alt-text="発行は成功しました":::
9. 左側の **[通知の表示]** ボタンをクリックして、通知を確認することもできます。 通知ウィンドウを閉じるには、 **[X]** をクリックします。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/show-notifications.png" alt-text="通知の表示":::


### <a name="run-the-full-copy-pipeline"></a>フル コピー パイプラインを実行する
パイプラインのツール バーの **[トリガー]** をクリックし、 **[Trigger Now]\(今すぐトリガー\)** をクリックします。

:::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu.png" alt-text="[トリガー] メニューから選択された [Trigger Now]\(今すぐトリガー\) オプションを示すスクリーンショット。":::

### <a name="monitor-the-full-copy-pipeline"></a>フル コピー パイプラインを監視する

1. 左側の **[監視]** タブをクリックします。 一覧にパイプラインの実行とその状態が表示されます。 一覧を更新するには、 **[最新の情報に更新]** をクリックします。 [アクション] 列のリンクを使用すると、パイプラインの実行に関連付けられているアクティビティの実行を表示し、パイプラインを再実行することができます。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/monitor-full-copy-pipeline-run.png" alt-text="データ ファクトリのパイプライン実行を示すスクリーンショット。":::
2. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクをクリックします。 パイプライン内のアクティビティは 1 つだけであるため、一覧に表示されるエントリは 1 つのみです。 [Pipeline Runs]\(パイプラインの実行\) ビューに戻るには、上部の **[パイプライン]** リンクをクリックします。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/activity-runs-full-copy.png" alt-text="[パイプライン] リンクが呼び出された、データ ファクトリのアクティビティの実行を示すスクリーンショット。":::

### <a name="review-the-results"></a>結果の確認
`adftutorial` コンテナーの `incchgtracking` フォルダーに、`incremental-<GUID>.txt` という名前のファイルが確認できます。

:::image type="content" source="media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-output-file.png" alt-text="フル コピーからの出力ファイル":::

このファイルには、データベースからのデータが存在します。

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>ソース テーブルにデータを追加する

データベースに次のクエリを実行して行の追加と更新を行います。

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

```

## <a name="create-a-pipeline-for-the-delta-copy"></a>差分コピーのパイプラインを作成する
この手順では、次のアクティビティを含んだパイプラインを作成して定期的に実行します。 **ルックアップ アクティビティ** は、Azure SQL Database から新旧の SYS_CHANGE_VERSION を取得してコピー アクティビティに渡します。 **コピー アクティビティ** は、2 つの SYS_CHANGE_VERSION 値の間に存在する挿入/更新/削除されたデータを Azure SQL Database から Azure Blob Storage にコピーします。 **ストアド プロシージャ アクティビティ** は、次回のパイプライン実行に備えて SYS_CHANGE_VERSION の値を更新します。

1. Data Factory の UI で、 **[編集]** タブに切り替えます。左ウィンドウで **[+]\(プラス記号\)** をクリックし、 **[パイプライン]** をクリックします。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu-2.png" alt-text="データ ファクトリでパイプラインを作成する方法を示すスクリーンショット。":::
2. パイプラインを構成するための新しいタブが表示されます。 ツリー ビューにもパイプラインが表示されます。 **[プロパティ]** ウィンドウで、パイプラインの名前を「**IncrementalCopyPipeline**」に変更します。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-pipeline-name.png" alt-text="パイプライン名":::
3. **[アクティビティ]** ツール ボックスの **[General]\(一般\)** を展開し、 **[検索]** アクティビティをパイプライン デザイナー画面にドラッグ アンド ドロップします。 アクティビティの名前を「**LookupLastChangeTrackingVersionActivity**」に設定します。 このアクティビティは、直前のコピー操作で使用された変更追跡バージョンを取得します。この値は、テーブル **table_store_ChangeTracking_version** に格納されています。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-name.png" alt-text="パイプラインと検索アクティビティを示すスクリーンショット。":::
4. **[プロパティ]** ウィンドウで **[設定]** に切り替えて、 **[Source Dataset]\(ソース データセット\)** フィールドで **[ChangeTrackingDataset]** を選択します。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-settings.png" alt-text="[プロパティ] ウィンドウの [設定] タブを示すスクリーンショット。":::
5. **[アクティビティ]** ツールボックスからパイプライン デザイナー画面に **[検索]** アクティビティをドラッグ アンド ドロップします。 アクティビティの名前を「**LookupCurrentChangeTrackingVersionActivity**」に設定します。 このアクティビティは、現在の変更追跡バージョンを取得します。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-name.png" alt-text="パイプラインと 2 つの検索アクティビティを示すスクリーンショット。":::
6. **[プロパティ]** ウィンドウで **[設定]** に切り替え、以下の手順を実行します。

   1. **[Source Dataset]\(ソース データセット\)** フィールドで **[SourceDataset]** を選択します。
   2. **[クエリの使用]** で **[クエリ]** を選択します。
   3. **[クエリ]** に次の SQL クエリを入力します。

       ```sql
       SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion
       ```

      :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-settings.png" alt-text="[プロパティ] ウィンドウの [設定] タブに追加されたクエリを示すスクリーンショット。":::
7. **[アクティビティ]** ツールボックスで **[データ フロー]** を展開し、パイプライン デザイナー画面に **[コピー]** アクティビティをドラッグ アンド ドロップします。 アクティビティの名前を「**IncrementalCopyActivity**」に設定します。 このアクティビティは、直前の変更追跡バージョンと現在の変更追跡バージョンとの間のデータをターゲット データ ストアにコピーします。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-activity-name.png" alt-text="コピー アクティビティ - 名前":::
8. **[プロパティ]** ウィンドウで **[ソース]** タブに切り替え、以下の手順を実行します。

   1. **[Source Dataset]\(ソース データセット\)** で **[SourceDataset]** を選択します。
   2. **[クエリの使用]** で **[クエリ]** を選択します。
   3. **[クエリ]** に次の SQL クエリを入力します。

       ```sql
       select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}
       ```

      :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-source-settings.png" alt-text="コピー アクティビティ - ソースの設定":::
9. **[シンク]** タブに切り替えて、 **[Sink Dataset]\(シンク データセット\)** フィールドで **[SinkDataset]** を選択します。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-sink-settings.png" alt-text="コピー アクティビティ - シンクの設定":::
10. 1 つずつ、**両方の検索アクティビティをコピー アクティビティに接続** します。 **[検索]** アクティビティにアタッチされている **緑** のボタンを **[コピー]** アクティビティにドラッグします。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-lookup-and-copy.png" alt-text="検索アクティビティとコピー アクティビティを接続する":::
11. **[アクティビティ]** ツールボックスからパイプライン デザイナー画面に **[ストアド プロシージャ]** アクティビティをドラッグ アンド ドロップします。 アクティビティの名前を「**StoredProceduretoUpdateChangeTrackingActivity**」に設定します。 このアクティビティは、**table_store_ChangeTracking_version** テーブル内の変更追跡バージョンを更新します。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-activity-name.png" alt-text="ストアド プロシージャ アクティビティ - 名前":::
12. *[SQL Account\*]\(SQL アカウント\)* タブに切り替えて、 **[リンクされたサービス]** で **[AzureSqlDatabaseLinkedService]** を選択します。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/sql-account-tab.png" alt-text="ストアド プロシージャ アクティビティ - SQL アカウント":::
13. **[ストアド プロシージャ]** タブに切り替えて、次の手順を実行します。

    1. **[ストアド プロシージャ名]** に **[Update_ChangeTracking_Version]** を選択します。  
    2. **[Import parameter]\(インポート パラメーター\)** を選択します。
    3. **[ストアド プロシージャのパラメーター]** セクションで、各パラメーターに次の値を指定します。

        | 名前 | 種類 | 値 |
        | ---- | ---- | ----- |
        | CurrentTrackingVersion | Int64 | @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion} |
        | TableName | String | @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName} |

        :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-parameters.png" alt-text="ストアド プロシージャ アクティビティ - パラメーター":::
14. **コピー アクティビティをストアド プロシージャ アクティビティに接続します**。 コピー アクティビティにアタッチされている **緑** のボタンをストアド プロシージャ アクティビティにドラッグ アンド ドロップします。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-copy-stored-procedure.png" alt-text="コピー アクティビティとストアド プロシージャ アクティビティを接続する":::
15. ツール バーの **[検証]** をクリックします。 検証エラーがないことを確認します。 **[>>]** をクリックして、 **[Pipeline Validation Report]\(パイプライン検証レポート\)** ウィンドウを閉じます。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/validate-button.png" alt-text="検証ボタン":::
16. **[すべて公開]** ボタンをクリックして、エンティティ (リンクされたサービス、データセット、およびパイプライン) を Data Factory サービスに発行します。 **[発行は成功しました]** というメッセージが表示されるまで待機します。

       :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button-2.png" alt-text="データ ファクトリの [Publish All]\(すべて発行\) ボタンを示すスクリーンショット。":::    

### <a name="run-the-incremental-copy-pipeline"></a>増分コピー パイプラインを実行する
1. パイプラインのツール バーの **[トリガー]** をクリックし、 **[Trigger Now]\(今すぐトリガー\)** をクリックします。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu-2.png" alt-text="パイプラインとアクティビティ、[トリガー] メニューから選択された [Trigger Now]\(今すぐトリガー\) オプションを示すスクリーンショット。":::
2. **[Pipeline Run]\(パイプラインの実行\)** ウィンドウで **[完了]** を選択します。

### <a name="monitor-the-incremental-copy-pipeline"></a>増分コピー パイプラインを監視する
1. 左側の **[監視]** タブをクリックします。 一覧にパイプラインの実行とその状態が表示されます。 一覧を更新するには、 **[最新の情報に更新]** をクリックします。 **[アクション]** 列のリンクを使用すると、パイプラインの実行に関連付けられているアクティビティの実行を表示し、パイプラインを再実行することができます。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-pipeline-runs.png" alt-text="パイプラインを含むデータ ファクトリのパイプライン実行を示すスクリーンショット。":::
2. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクをクリックします。 パイプライン内のアクティビティは 1 つだけであるため、一覧に表示されるエントリは 1 つのみです。 [Pipeline Runs]\(パイプラインの実行\) ビューに戻るには、上部の **[パイプライン]** リンクをクリックします。

    :::image type="content" source="./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-activity-runs.png" alt-text="いくつかが成功としてマークされている、データ ファクトリのパイプライン実行を示すスクリーンショット。":::


### <a name="review-the-results"></a>結果の確認
`adftutorial` コンテナーの `incchgtracking` フォルダーにもう 1 つファイルが確認できます。

:::image type="content" source="media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-output-file.png" alt-text="増分コピーからの出力ファイル":::

このファイルに含まれているのは、データベースからの差分データのみです。 `U` と記録されているレコードはデータベース内の更新された行で、`I` は追加された行です。

```
1,update,10,2,U
6,new,50,1,I
```
最初の 3 つの列は、data_source_table 内の変更済みデータです。 最後の 2 つの列は、変更追跡システム テーブルからのメタデータです。 4 つ目の列は、変更済みの各行の SYS_CHANGE_VERSION です。 5 つ目の列は実行された操作で、U は更新 (Update) を、I は挿入 (Insert) を表します。  変更追跡情報の詳細については、「[CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql)」を参照してください。

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10            2                                 U
6        new     50            1                                 I
```

## <a name="next-steps"></a>次のステップ
次のチュートリアルに進んで、LastModifiedDate に基づいて新規ファイルおよび変更されたファイルのみをコピーする方法について学習してください。

> [!div class="nextstepaction"]
> [lastmodifieddate を基準に新しいファイルをコピーする](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
