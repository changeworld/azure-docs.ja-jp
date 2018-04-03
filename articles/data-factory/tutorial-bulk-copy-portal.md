---
title: Azure Data Factory を使ってデータを一括コピーする | Microsoft Docs
description: Azure Data Factory とコピー アクティビティを使い、ソース データ ストアからコピー先データ ストアにデータを一括コピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 440b07b494b34db7ff3fcdf5d5ac830b165c339d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Azure Data Factory を使って複数のテーブルを一括コピーする
このチュートリアルでは、**Azure SQL Database から Azure SQL Data Warehouse に多数のテーブルをコピーする方法**について説明します。 同じパターンは他のコピー シナリオでも適用できます。 たとえば、SQL Server/Oracle から Azure SQL Database/Data Warehouse/Azure BLOB にテーブルをコピーしたり、BLOB から Azure SQL Database テーブルにさまざまなパスをコピーしたりするシナリオが該当します。

> [!NOTE]
> - Azure Data Factory を初めて使用する場合は、「[Azure Data Factory の概要](introduction.md)」を参照してください。
> - この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。

このチュートリアルは大まかに次の手順で構成されます。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure SQL Database、Azure SQL Data Warehouse、Azure Storage のリンクされたサービスを作成します。
> * Azure SQL Database と Azure SQL Data Warehouse のデータセットを作成します。
> * コピーするテーブルを検索するためのパイプラインと実際のコピー操作を実行するためのパイプラインを作成します。 
> * パイプラインの実行を開始します。
> * パイプラインとアクティビティの実行を監視します。

このチュートリアルでは、Azure Portal を使用します。 その他のツールまたは SDK を使ってデータ ファクトリを作成する方法については、[クイックスタート](quickstart-create-data-factory-dot-net.md)を参照してください。 

## <a name="end-to-end-workflow"></a>エンド ツー エンド ワークフロー
このシナリオでは、SQL Data Warehouse にコピーする必要のあるテーブルが Azure SQL Database に多数存在します。 以下の図は、パイプラインのワークフロー ステップを論理的な発生順に並べたものです。

![ワークフロー](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* 1 つ目のパイプラインでは、シンク データ ストアにコピーするテーブルの一覧が検索されます。  代わりに、シンク データ ストアにコピーするすべてのテーブルが列挙されたメタデータ テーブルを用意する方法もあります。 次に、1 つ目のパイプラインによって 2 つ目のパイプラインがトリガーされ、データベース内の各テーブルを反復処理しながらデータのコピー操作が実行されます。
* 実際のコピーは 2 つ目のパイプラインによって実行されます。 このパイプラインは、テーブルの一覧をパラメーターとして受け取ります。 その一覧の各テーブルについて、Azure SQL Database 内の特定のテーブルを SQL Data Warehouse 内の該当するテーブルにコピーします。この処理には、パフォーマンスを最大限に高めるために、[BLOB ストレージと PolyBase によるステージング コピー](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)が使用されます。 この例では、1 つ目のパイプラインからパラメーターの値としてテーブルの一覧が渡されます。 

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件
* **Azure Storage アカウント**。 この Azure ストレージ アカウントは、一括コピー操作のステージング BLOB ストレージとして使用されます。 
* **Azure SQL データベース**。 ソース データが格納されているデータベースです。 
* **Azure SQL Data Warehouse**。 SQL データベースからコピーされたデータは、このデータ ウェアハウスに格納されます。 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>SQL Database と SQL Data Warehouse の準備

**ソース Azure SQL Database の準備**:

[Azure SQL データベースの作成](../sql-database/sql-database-get-started-portal.md)に関する記事に従い、Adventure Works LT サンプル データを使って Azure SQL データベースを作成します。 このチュートリアルでは、このサンプル データベースからすべてのテーブルを SQL データ ウェアハウスにコピーします。

**シンク Azure SQL Data Warehouse の準備**:

1. Azure SQL データ ウェアハウスがない場合は、「[SQL Data Warehouse の作成](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md)」の記事に書かれている作成手順を参照してください。

2. 対応するテーブル スキーマを SQL Data Warehouse に作成します。 Azure SQL Database から Azure SQL Data Warehouse への**スキーマの移行**には、[移行ユーティリティ](https://www.microsoft.com/download/details.aspx?id=49100)を使用できます。 データの移行/コピーは、後続の手順で Azure Data Factory を使用して行います。

## <a name="azure-services-to-access-sql-server"></a>SQL サーバーにアクセスするための Azure サービス

SQL Database と SQL Data Warehouse の両方について、SQL サーバーへのアクセスを Azure サービスに許可します。 ご利用の Azure SQL サーバーで **[Azure サービスへのアクセスを許可]** 設定を**オン**にしてください。 この設定により、Data Factory サービスが Azure SQL データベースからデータを読み取ったり、Azure SQL データ ウェアハウスにデータを書き込んだりすることができます。 この設定を確認して有効にするには、次の手順を実行します。

1. 左側にある **[More services (その他のサービス)]** ハブをクリックし、**[SQL サーバー]** をクリックします。
2. サーバーを選択し、**[設定]** の **[ファイアウォール]** をクリックします。
3. **[ファイアウォールの設定]** ページの **[Azure サービスへのアクセスを許可]** で **[オン]** をクリックします。

## <a name="create-a-data-factory"></a>Data Factory を作成する。
1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
1. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/tutorial-bulk-copy-portal/new-azure-data-factory-menu.png)
2. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**ADFTutorialBulkCopyDF**」と入力します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-bulk-copy-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 [名前] フィールドで次のエラーが発生した場合は、データ ファクトリの名前を変更してください (yournameADFTutorialBulkCopyDF など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
3. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
4. **[リソース グループ]** について、次の手順のいずれかを行います。
     
      - **[Use existing (既存のものを使用)]**を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
      - **[新規作成]**を選択し、リソース グループの名前を入力します。   
         
      リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
4. **バージョン**として **[V2 (プレビュー)]** を選択します。
5. データ ファクトリの **場所** を選択します。 現在、Data Factory V2 でデータ ファクトリを作成できるリージョンは、米国東部、米国東部 2、および西ヨーロッパだけです。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。
6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **Create** をクリックしてください。
8. ダッシュボードに、**[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media//tutorial-bulk-copy-portal/deploying-data-factory.png)
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
    ![データ ファクトリのホーム ページ](./media/tutorial-bulk-copy-portal/data-factory-home-page.png)
10. **[Author & Monitor]\(作成と監視\)** タイルをクリックして、別のタブで Data Factory UI アプリケーションを起動します。
11. **開始**ページで、次の図に示すように、左パネルの **[編集]** タブに切り替えます。  

    ![開始ページ](./media/tutorial-bulk-copy-portal/get-started-page.png)

## <a name="create-linked-services"></a>リンクされたサービスを作成します
データ ストアやコンピューティングをデータ ファクトリにリンクするには、リンクされたサービスを作成します。 リンクされたサービスは、Data Factory サービスが実行時にデータ ストアに接続するために使用する接続情報を持っています。 

このチュートリアルでは、データ ファクトリに Azure SQL Database、Azure SQL Data Warehouse、および Azure Blob Storage の各データ ストアをリンクします。 Azure SQL Database はソース データ ストアです。 Azure SQL Data Warehouse はシンク (コピー先) データ ストアです。 データは、Azure Blob Storage にステージングされてから、PolyBase を使用して SQL Data Warehouse に読み込まれます。 

### <a name="create-the-source-azure-sql-database-linked-service"></a>ソース Azure SQL Database のリンクされたサービスを作成する
この手順では、Azure SQL Database をデータ ファクトリに接続するためのリンクされたサービスを作成します。 

1. ウィンドウの下部にある **[接続]** をクリックし、ツールバーの **[+ 新規]** をクリックします。 

    ![新しいリンクされたサービスのボタン](./media/tutorial-bulk-copy-portal/new-linked-service-button.png)
2. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで **[Azure SQL Database]** を選択し、**[続行]** をクリックします。 

    ![Azure SQL Database の選択](./media/tutorial-bulk-copy-portal/select-azure-sql-database.png)
3. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。 

    1. **[名前]** に「**AzureSqlDatabaseLinkedService**」と入力します。 
    2. **[サーバー名]** で、使用する Azure SQL Server を選択します。
    3. **[データベース名]** で、使用する Azure SQL Database を選択します。 
    4. **[ユーザー名]** に、Azure SQL Database に接続するユーザーの名前を入力します。 
    5. **[パスワード]**に、そのユーザーのパスワードを入力します。 
    6. 指定した情報を使用して Azure SQL Database への接続をテストするために、**[テスト接続]** をクリックします。
    7. **[Save]** をクリックします。

        ![Azure SQL Database の設定](./media/tutorial-bulk-copy-portal/azure-sql-database-settings.png)

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>シンク Azure SQL Data Warehouse のリンクされたサービスを作成する

1. **[接続]** タブで、再度ツールバーの **[+ 新規]** をクリックします。 
2. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで **[Azure SQL Data Warehouse]** を選択し、**[続行]** をクリックします。 
3. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。 

    1. **[名前]** に「**AzureSqlDWLinkedService**」と入力します。 
    2. **[サーバー名]** で、使用する Azure SQL Server を選択します。
    3. **[データベース名]** で、使用する Azure SQL Database を選択します。 
    4. **[ユーザー名]** に、Azure SQL Database に接続するユーザーの名前を入力します。 
    5. **[パスワード]**に、そのユーザーのパスワードを入力します。 
    6. 指定した情報を使用して Azure SQL Database への接続をテストするために、**[テスト接続]** をクリックします。
    7. **[Save]** をクリックします。

### <a name="create-the-staging-azure-storage-linked-service"></a>ステージング Azure Storage のリンクされたサービスを作成する
このチュートリアルでは、PolyBase でコピーのパフォーマンスを高めるために、中間ステージング領域として Azure BLOB ストレージを使用しています。

1. **[接続]** タブで、再度ツールバーの **[+ 新規]** をクリックします。 
2. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで **[Azure Blob Storage]** を選択し、**[続行]** をクリックします。 
3. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。 

    1. **[名前]** に「**AzureStorageLinkedService**」と入力します。 
    2. **[ストレージ アカウント名]** で、使用する **Azure ストレージ アカウント**を選択します。
    4. **[Save]** をクリックします。


## <a name="create-datasets"></a>データセットを作成する
このチュートリアルでは、データの格納場所を指定するための、ソースとシンクのデータセットを作成します。 

入力データセット AzureSqlDatabaseDataset は、AzureSqlDatabaseLinkedService を参照します。 このリンクされたサービスには、データベースに接続するための接続文字列が指定されています。 データセットには、ソース データが含まれているデータベースとテーブルの名前を指定します。 

出力データセット AzureSqlDWDataset は、AzureSqlDWLinkedService を参照します。 このリンクされたサービスには、データ ウェアハウスに接続するための接続文字列が指定されています。 データセットには、データのコピー先となるデータベースとテーブルを指定します。 

このチュートリアルでは、データセットの定義にソースとコピー先の SQL テーブルをハード コーディングしません。 代わりに、実行時に ForEach アクティビティで、テーブルの名前をコピー アクティビティに渡します。 

### <a name="create-a-dataset-for-source-sql-database"></a>ソース SQL Database のデータセットを作成する

1. 左ウィンドウで **[+]\(プラス記号\)** をクリックし、**[データセット]** をクリックします。 

    ![New dataset menu](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
2. **[新しいデータセット]** ウィンドウで **[Azure SQL Database]** を選択し、**[完了]** をクリックします。 **[AzureSqlTable1]** という新しいタブが表示されます。 
    
    ![Azure SQL Database の選択](./media/tutorial-bulk-copy-portal/select-azure-sql-database-dataset.png)
3. 下部にあるプロパティ ウィンドウで、**[名前]** に「**AzureSqlDatabaseDataset**」と入力します。

    ![ソース データセット名](./media/tutorial-bulk-copy-portal/source-dataset-general.png)
4. **[接続]** タブに切り替えて、次の手順を実行します。 

    1. **[リンクされたサービス]** で **[AzureSqlDatabaseLinkedService]** を選択します。
    2. **[テーブル]** で任意のテーブルを選択します。 このテーブルはダミーのテーブルです。 パイプラインを作成するときに、ソース データセットに対するクエリを指定します。 このクエリは Azure SQL Database からデータを抽出するために使用します。 または、**[編集]** チェック ボックスをオンにして、テーブル名として「**dummyName**」と入力してもかまいません。 

    ![ソース データセットの接続ページ](./media/tutorial-bulk-copy-portal/source-dataset-connection-page.png)
 

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>シンク SQL Data Warehouse のデータセットを作成する

1. 左ウィンドウで **[+]\(プラス記号\)** をクリックし、**[データセット]** をクリックします。 
2. **[新しいデータセット]** ウィンドウで **[Azure SQL Data Warehouse]** を選択し、**[完了]** をクリックします。 **[AzureSqlDWTable1]** という新しいタブが表示されます。 
3. 下部にあるプロパティ ウィンドウで、**[名前]** に「**AzureSqlDWDataset**」と入力します。
4. **[接続]** タブに切り替えて、**[リンクされたサービス]** で「**AzureSqlDatabaseLinkedService**」を選択します。
5. **[パラメーター]** タブに切り替えて、**[+ 新規]** をクリックします。

    ![ソース データセットの接続ページ](./media/tutorial-bulk-copy-portal/sink-dataset-new-parameter-button.png)
6. パラメーター名として「**DWTableName**」と入力します。 ページからこの名前をコピーして貼り付ける場合は、**DWTableName** の末尾に**末尾空白文字**がないことを確認してください。 
7. **[Parameterized properties]\(パラメーター化されたプロパティ\)** セクションで、**[tableName]** プロパティに「`@{dataset().DWTableName}`」と入力します。 データセットの **[tableName]** プロパティに設定される値は、**DWTableName** パラメーターの引数として渡されます。 ForEach アクティビティによってテーブルのリストが反復処理され、コピー アクティビティに 1 つずつ渡されます。 
   
    ![パラメーター名](./media/tutorial-bulk-copy-portal/dwtablename-tablename.png)

## <a name="create-pipelines"></a>パイプラインを作成する
このチュートリアルでは、2 つのパイプライン **IterateAndCopySQLTables** と **GetTableListAndTriggerCopyData** を作成します。 

**GetTableListAndTriggerCopyData** パイプラインは、次の 2 つの手順を実行します。

* Azure SQL Database システム テーブルを検索してコピーするテーブルの一覧を取得します。
* パイプライン **IterateAndCopySQLTables** をトリガーし、実際のデータ コピーを実行します。

**GetTableListAndTriggerCopyData** は、テーブルの一覧をパラメーターとして受け取ります。 その一覧の各テーブルについて、ステージング コピーと PolyBase を使って、Azure SQL Database 内のテーブルから Azure SQL Data Warehouse にデータがコピーされます。

### <a name="create-the-pipeline-iterateandcopysqltables"></a>パイプライン IterateAndCopySQLTables を作成する

1. 左ウィンドウで **[+]\(プラス記号\)** をクリックし、**[パイプライン]** をクリックします。

    ![新しいパイプライン メニュー](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. プロパティ ウィンドウで、パイプラインの名前を「**IterateAndCopySQLTables**」に変更します。 

    ![パイプライン名](./media/tutorial-bulk-copy-portal/first-pipeline-name.png)
3. **[パラメーター]** タブに切り替えて、次の手順を実行します。 

    1. **[+ 新規]** をクリックします。 
    2. **[名前]** に、パラメーター名として「**tableList**」と入力します。
    3. **[型]** で **[オブジェクト]** を選択します。

        ![パイプライン パラメーター](./media/tutorial-bulk-copy-portal/first-pipeline-parameter.png)
4. **[アクティビティ]** ツール ボックスで **[Iteration & Conditions]\(繰り返しと条件\)** を展開し、パイプライン デザイナー画面に **[ForEach]** アクティビティをドラッグ アンド ドロップします。 **[アクティビティ]** ツールボックスで、アクティビティを検索することもできます。 下部にある**プロパティ** ウィンドウで、**[名前]** に「**IterateSQLTables**」と入力します。 

    ![ForEach アクティビティの名前](./media/tutorial-bulk-copy-portal/for-each-activity-name.png)
5. **[設定]** タブに切り替えて、**[項目]** に「`@pipeline().parameters.tableList`」と入力します。

    ![ForEach アクティビティの設定](./media/tutorial-bulk-copy-portal/for-each-activity-settings.png)
6. **ForEach** アクティビティに子アクティビティを追加するには、[ForEach] アクティビティを**ダブルクリック**するか、**[編集]\(鉛筆アイコン\)** をクリックします。 アクティビティのアクション リンクは、アクティビティを選択した場合にのみ表示されます。 

    ![ForEach アクティビティの名前](./media/tutorial-bulk-copy-portal/edit-for-each-activity.png)
7. **[アクティビティ]** ツール ボックスで **[データ フロー]** を展開し、パイプライン デザイナー画面に **[コピー]** アクティビティをドラッグ アンド ドロップして、プロパティ ウィンドウで名前を「**CopyData**」に変更します。 ウィンドウの上部に階層リンク メニューがあります。 IterateAndCopySQLTable はパイプライン名で、IterateSQLTables は ForEach アクティビティ名です。 現在のデザイナー画面には、アクティビティの範囲が表示されています。 ForEach アクティビティからパイプラインにデザイナー画面を切り替えるには、階層リンク メニューのリンクをクリックします。 

    ![ForEach アクティビティ内のコピー](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)
8. **[ソース]** タブに切り替えて、次の手順を実行します。

    1. **[Source Dataset]\(ソース データセット\)** で「**AzureSqlDatabaseDataset**」を選択します。 
    2. **[ユーザー クエリ]** で **[クエリ]** オプションを選択します。 
    3. **[クエリ]** に次の SQL クエリを入力します。

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

        ![コピー ソースの設定](./media/tutorial-bulk-copy-portal/copy-source-settings.png)
9. **[シンク]** タブに切り替えて、次の手順を実行します。 

    1. **[Sink Dataset]\(シンク データセット\)** で「**AzureSqlDWDataset**」を選択します。
    2. **[Polybase Settings]\(Polybase 設定\)** を展開し、**[Allow polybase]\(Polybase を許可する\)** をオンにします。 
    3. **[使用型の既定]** オプションをオフにします。 
    4. **[後処理用スクリプト]** に次の SQL スクリプトを入力します。 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![コピー シンクの設定](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
10. **[パラメーター]** タブに切り替えて、**[Sink Dataset]\(シンク データセット\)** セクションの **DWTableName** パラメーターが見えるまで下へスクロールします。 このパラメーターの値を「`[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]`」に設定します。

    ![コピー シンクのパラメーター](./media/tutorial-bulk-copy-portal/copy-sink-parameters.png)
11. **[設定]** タブに切り替えて、次の手順を実行します。 

    1. **[Enable Staging]\(ステージングの有効化\)** で **[True]\(はい\)** を選択します。
    2. **[Store Account Linked Service]\(ストア アカウントのリンクされたサービス\)** で **[AzureStorageLinkedService]** を選択します。

        ![ステージングの有効化](./media/tutorial-bulk-copy-portal/copy-sink-staging-settings.png)
12. パイプライン設定を検証するために、**[検証]** をクリックします。 検証エラーがないことを確認します。 **[>>]** をクリックして、**[Pipeline Validation Report]\(パイプライン検証レポート\)** を閉じます。

    ![パイプライン検証レポート](./media/tutorial-bulk-copy-portal/first-pipeline-validation-report.png)

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>パイプライン GetTableListAndTriggerCopyData を作成する

このパイプラインでは、次の 2 つのステップが実行されます。

* Azure SQL Database システム テーブルを検索してコピーするテーブルの一覧を取得します。
* パイプライン "IterateAndCopySQLTables" をトリガーして実際にデータのコピーを実行します。

1. 左ウィンドウで **[+]\(プラス記号\)** をクリックし、**[パイプライン]** をクリックします。

    ![新しいパイプライン メニュー](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. プロパティ ウィンドウで、パイプラインの名前を「**GetTableListAndTriggerCopyData**」に変更します。 

    ![パイプライン名](./media/tutorial-bulk-copy-portal/second-pipeline-name.png)
3. **[アクティビティ]** ツール ボックスで **[General]\(一般\)** を展開し、パイプライン デザイナー画面に **[検索]** アクティビティをドラッグ アンド ドロップして、次のステップを実行します。

    1. **[名前]** に「**LookupTableList**」と入力します。 
    2. **[説明]** に「**Azure SQL Database からテーブル リストを取得する**」と入力します。

        ![検索アクティビティ - [全般] ページ](./media/tutorial-bulk-copy-portal/lookup-general-page.png)
4. **[設定]** ページに切り替えて、次の手順を実行します。

    1. **[Source Dataset]\(ソース データセット\)** で「**AzureSqlDatabaseDataset**」を選択します。 
    2. **[クエリの使用]** で **[クエリ]** を選択します。 
    3. **[クエリ]** に次の SQL クエリを入力します。

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    4. **[First row only]\(先頭行のみ\)** フィールドのチェック ボックスをオフにします。

        ![検索アクティビティ - [設定] ページ](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
5. [アクティビティ] ツール ボックスからパイプライン デザイナー画面に **[パイプラインの実行]** アクティビティをドラッグ アンド ドロップし、名前を「**TriggerCopy**」に設定します。

    ![パイプラインの実行アクティビティ - [全般] ページ](./media/tutorial-bulk-copy-portal/execute-pipeline-general-page.png)    
6. **[設定]** ページに切り替えて、次の手順を実行します。 

    1. **[Invoked pipeline]\(呼び出されたパイプライン\)** で **[IterateAndCopySQLTables]** を選択します。 
    2. **[詳細設定]** セクションを展開します。 
    3. **[パラメーター]** セクションで **[+ 新規]** をクリックします。 
    4. **[名前]** に、パラメーター名として「**tableList**」と入力します。
    5. **[値]** にパラメーター値として「`@activity('LookupTableList').output.value`」と入力します。 これで、2 つ目のパイプラインへの入力として検索アクティビティの結果一覧が設定されます。 結果一覧には、コピー先にコピーする必要があるデータが格納されたテーブルの一覧が含まれています。 

        ![パイプラインの実行アクティビティ - [設定] ページ](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)
7. **[検索]** アクティビティを **[パイプラインの実行]** アクティビティに**接続**するために、[検索] アクティビティの横に付いている**緑の四角**を [パイプラインの実行] アクティビティの左へドラッグします。

    ![検索アクティビティとパイプラインの実行アクティビティの接続](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)
8. パイプラインを検証するために、ツール バーの **[検証]** をクリックします。 検証エラーがないことを確認します。 **[>>]** をクリックして、**[Pipeline Validation Report]\(パイプライン検証レポート\)** を閉じます。

    ![2 つ目のパイプライン - 検証レポート](./media/tutorial-bulk-copy-portal/second-pipeline-validation-report.png)
9. エンティティ (データセットやパイプラインなど) を Data Factory サービスに発行するには、**[すべて公開]** をクリックします。 発行が成功するまで待機します。 

    ![[発行] ボタン](./media/tutorial-bulk-copy-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>パイプラインの実行をトリガーする

1. **[GetTableListAndTriggerCopyData]** タブがアクティブであることを確認します。 
2. **[トリガー]** をクリックし、**[Trigger Now]\(今すぐトリガー\)** をクリックします。 

    ![[Trigger Now]\(今すぐトリガー\)](./media/tutorial-bulk-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

1. **[監視]** タブに切り替えます。ソリューションの両方のパイプラインが実行されるまで、**[最新の情報に更新]** をクリックします。 状態が **[成功]** になるまで一覧を更新します。 

    ![パイプライン実行](./media/tutorial-bulk-copy-portal/pipeline-runs.png)
2. GetTableListAndTriggerCopyData パイプラインに関連付けられているアクティビティの実行を表示するには、そのパイプラインの [アクション] リンクにある最初のリンクをクリックします。 このパイプラインの実行の 2 つのアクティビティの実行が表示されます。 

    ![アクティビティの実行](./media/tutorial-bulk-copy-portal/activity-runs-1.png)    
3. **[検索]** アクティビティの出力を表示するには、そのアクティビティの **[出力]** 列のリンクをクリックします。 **[出力]** ウィンドウは最大化したり元のサイズに戻したりできます。 確認したら、**[X]** をクリックして **[出力]** ウィンドウを閉じます。

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
4. **[Pipeline Runs]\(パイプラインの実行\)** ビューに戻るには、上部の **[パイプライン]** リンクをクリックします。 **IterateAndCopySQLTables** パイプラインの **[View Activity Runs]\(アクティビティの実行の表示\)** リンク (**[アクション]** 列にある最初のリンク) をクリックします。 次の図のような出力が表示されます。**検索**アクティビティの出力に含まれている各テーブルに**コピー** アクティビティが 1 つずつあるのがわかります。 

    ![アクティビティの実行](./media/tutorial-bulk-copy-portal/activity-runs-2.png)
5. このチュートリアルで使用したターゲット SQL Data Warehouse にデータがコピーされていることを確認します。 

## <a name="next-steps"></a>次の手順
このチュートリアルでは、以下の手順を実行しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure SQL Database、Azure SQL Data Warehouse、Azure Storage のリンクされたサービスを作成します。
> * Azure SQL Database と Azure SQL Data Warehouse のデータセットを作成します。
> * コピーするテーブルを検索するためのパイプラインと実際のコピー操作を実行するためのパイプラインを作成します。 
> * パイプラインの実行を開始します。
> * パイプラインとアクティビティの実行を監視します。

次のチュートリアルに進んで、ソースからコピー先にデータを増分コピーする方法について学習しましょう。
> [!div class="nextstepaction"]
>[データを増分コピーする](tutorial-incremental-copy-portal.md)