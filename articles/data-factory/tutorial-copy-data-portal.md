---
title: Azure Portal を使用して Data Factory パイプラインを作成する | Microsoft Docs
description: このチュートリアルでは、Azure Portal を使用してパイプラインを備えたデータ ファクトリを作成するための詳細な手順について説明します。 パイプラインでコピー アクティビティを使用して、Azure Blob Storage から SQL データベースにデータをコピーします。
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
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: 34c78a114c1d106c400a94941aa113153383e206
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
ms.locfileid: "30173340"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Blob Storage から SQL データベースにデータをコピーする
このチュートリアルでは、Azure Data Factory ユーザー インターフェイス (UI) を使用してデータ ファクトリを作成します。 このデータ ファクトリのパイプラインでは、Azure Blob Storage から SQL データベースにデータをコピーします。 このチュートリアルの構成パターンは、ファイルベースのデータ ストアからリレーショナル データ ストアへのコピーに適用されます。 ソースおよびシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する表を参照してください。

> [!NOTE]
> - Data Factory を初めて使用する場合は、「[Azure Data Factory の概要](introduction.md)」を参照してください。
>
> - この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 Data Factory のバージョン 1 (一般公開版) を使用する場合は、[Data Factory バージョン 1 の概要](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関する記事をご覧ください。

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * コピー アクティビティを含むパイプラインを作成します。
> * パイプラインをテスト実行します。
> * パイプラインを手動でトリガーします。
> * スケジュールに基づいてパイプラインをトリガーします。
> * パイプラインとアクティビティの実行を監視します。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
* **Azure ストレージ アカウント**。 Blob Storage を "*ソース*" データ ストアとして使用します。 ストレージ アカウントがない場合の作成手順については、[Azure のストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)に関するページを参照してください。
* **Azure SQL データベース**。 データベースを "*シンク*" データ ストアとして使用します。 SQL データベースがない場合の作成手順については、「[SQL Database の作成](../sql-database/sql-database-get-started-portal.md)」を参照してください。

### <a name="create-a-blob-and-a-sql-table"></a>BLOB と SQL テーブルを作成する

ここからは、次の手順を実行して、チュートリアルで使用する Blob Storage と SQL データベースを準備します。

#### <a name="create-a-source-blob"></a>ソース BLOB を作成する

1. メモ帳を起動します。 次のテキストをコピーし、**emp.txt** ファイルとしてディスクに保存します。

    ```
    John,Doe
    Jane,Doe
    ```

2. Blob Storage に **adftutorial** という名前のコンテナーを作成します。 このコンテナーに **input** という名前のフォルダーを作成します。 次に、**input** フォルダーに **emp.txt** ファイルをアップロードします。 Azure Portal を使用するか、または [Azure Storage Explorer](http://storageexplorer.com/) などのツールを使用して、これらのタスクを実行します。

#### <a name="create-a-sink-sql-table"></a>シンク SQL テーブルを作成する

1. 次の SQL スクリプトを使用して、**dbo.emp** テーブルを SQL データベースに作成します。

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Azure サービスに SQL Server へのアクセスを許可します。 Data Factory から SQL Server にデータを書き込むことができるように、SQL Server で **[Azure サービスへのアクセスを許可]** が**オン**になっていることを確認します。 この設定を確認して有効にするには、次の手順を実行します。

    a. 左側の **[その他のサービス]** > **[SQL Server]** を選択します。

    b. サーバーを選択し、**[設定]** の **[ファイアウォール]** を選択します。

    c. **[ファイアウォールの設定]** ページの **[Azure サービスへのアクセスを許可]** で **[オン]** を選択します。

## <a name="create-a-data-factory"></a>Data Factory を作成する。
この手順では、データ ファクトリを作成するほか、Data Factory UI を起動してそのデータ ファクトリにパイプラインを作成します。 

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を開きます。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
2. 左側のメニューで、**[新規]** > **[データ + 分析]** > **[Data Factory]** を選択します。 
  
   ![新しいデータ ファクトリの作成](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
3. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**ADFTutorialDataFactory**」と入力します。 
      
     ![新しいデータ ファクトリ](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は *グローバルに一意*にする必要があります。 名前フィールドで次のエラー メッセージが発生した場合は、データ ファクトリの名前を変更してください (yournameADFTutorialDataFactory など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関するページを参照してください。
  
   ![エラー メッセージ](./media/tutorial-copy-data-portal/name-not-available-error.png)
4. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
5. **[リソース グループ]** で、次の手順のいずれかを行います。
     
    a. **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。

    b. **[新規作成]** を選択し、リソース グループの名前を入力します。 
         
    リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。 
6. **[バージョン]** で、**[V2 (プレビュー)]** を選択します。
7. **[場所]** で、データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストア (Azure Storage、SQL Database など) やコンピューティング (Azure HDInsight など) は、他のリージョンに存在していてもかまいません。
8. **[ダッシュボードにピン留めする]** をオンにします。 
9. **[作成]** を選択します。 
10. ダッシュボードに、**[Deploying Data Factory]\(Data Factory をデプロイしています\)** というステータスを示した次のタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-copy-data-portal/deploying-data-factory.png)
11. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
    ![データ ファクトリのホーム ページ](./media/tutorial-copy-data-portal/data-factory-home-page.png)
12. **[Author & Monitor]\(作成と監視\)** を選択して、別のタブで Data Factory (UI) を起動します。

## <a name="create-a-pipeline"></a>パイプラインを作成する。
この手順では、コピー アクティビティが含まれたパイプラインをデータ ファクトリに作成します。 コピー アクティビティによって、Blob Storage から SQL Database にデータがコピーされます。 [クイックスタート チュートリアル](quickstart-create-data-factory-portal.md)では、次の手順でパイプラインを作成しました。

1. リンクされたサービスを作成します。 
2. 入力データセットと出力データセットを作成します。
3. パイプラインを作成します。

このチュートリアルでは、最初にパイプラインを作成します。 その後、パイプラインの構成に必要な場合にリンクされたサービスとデータセットを作成します。 

1. **[Let's get started]\(始めましょう\)** ページで **[Create pipeline]\(パイプラインの作成\)** を選択します。 

   ![パイプラインの作成](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
2. パイプラインの **[プロパティ]** ウィンドウの **[名前]** に、パイプラインの名前として「**CopyPipeline**」と入力します。

    ![パイプライン名](./media/tutorial-copy-data-portal/pipeline-name.png)
3. **[アクティビティ]** ツール ボックスで **[Data Flow]\(データ フロー\)** カテゴリを展開し、ツール ボックスからパイプライン デザイナー画面に **[コピー]** アクティビティをドラッグ アンド ドロップします。 

    ![コピー アクティビティ](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
4. **[プロパティ]** ウィンドウの **[General]\(一般\)** タブで、アクティビティの名前として「**CopyFromBlobToSql**」を入力します。

    ![アクティビティ名](./media/tutorial-copy-data-portal/activity-name.png)
5. **[ソース]** タブに移動します。**[+ 新規]** を選択して、ソース データセットを作成します。 

    ![[ソース] タブ](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
6. **[新しいデータセット]** ウィンドウで **[Azure Blob Storage]** を選択し、**[完了]** を選択します。 ソース データは Blob Storage にあるので、ソース データセットには **Azure Blob Storage** を選択します。 

    ![Storage の選択](./media/tutorial-copy-data-portal/select-azure-storage.png)
7. アプリケーションで **[AzureBlob1]** という名前の新しいタブが開かれたことがわかります。

    ![[AzureBlob1] タブ ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
8. **[プロパティ]** ウィンドウの下部にある **[General]\(一般\)** タブで、**[名前]** に「**SourceBlobDataset**」と入力します。

    ![データセットの名前](./media/tutorial-copy-data-portal/dataset-name.png)
9. **[プロパティ]** ウィンドウの **[接続]** タブに移動します。 **[リンクされたサービス]** ボックスの横にある **[+ 新規]** をクリックします。 

    リンクされたサービスは、データ ストアまたは計算をデータ ファクトリにリンクします。 ここでは、ストレージ アカウントをデータ ストアにリンクする、ストレージのリンクされたサービスを作成します。 リンクされたサービスには、Data Factory が実行時に Blob Storage に接続するために使用する接続情報が含まれています。 データセットは、コンテナー、フォルダー、ソース データが含まれているファイル (オプション) を指定します。 

    ![新しいリンクされたサービスのボタン](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
10. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。 

    a. **[名前]** に「**AzureStorageLinkedService**」と入力します。 

    b. **[ストレージ アカウント名]** で、使用するストレージ アカウントを選択します。

    c. **[テスト接続]** を選択して、ストレージ アカウントへの接続をテストします。

    d. **[保存]** を選択して、リンクされたサービスを保存します。

    ![新規のリンクされたサービス](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
11. **[ファイル パス]** の横にある **[参照]** を選択します。

    ![ファイル パスの [参照] ボタン](./media/tutorial-copy-data-portal/file-browse-button.png)
12. **adftutorial/input** フォルダーに移動して **emp.txt** ファイルを選択し、**[完了]** を選択します。 または、**emp.txt** をダブルクリックしてもかまいません。 

    ![入力ファイルの選択](./media/tutorial-copy-data-portal/select-input-file.png)
13. **[File format]\(ファイル形式\)** が **[Text format]\(テキスト形式\)** に設定されており、**[列区切り記号]** が **[コンマ (`,`)]** に設定されていることを確認します。 ソース ファイルで異なる行および列の区切り記号が使用されている場合、**[File format]\(ファイル形式\)** で **[Detect Text Format]\(テキスト形式の検出\)** を選択できます。 データのコピー ツールによって、ファイル形式と区切り記号が自動で検出されます。 これらの値は上書きすることもできます。 このページのデータをプレビューするには、**[データのプレビュー]** を選択します。

    ![テキスト形式の検出](./media/tutorial-copy-data-portal/detect-text-format.png)
14. **[プロパティ]** ウィンドウの **[スキーマ]** タブに移動し、**[スキーマのインポート]** を選択します。 アプリケーションによってソース ファイル内の 2 つの列が検出されたことがわかります。 ソース データ ストアからシンク データ ストアに列をマップできるように、ここでスキーマをインポートします。 列をマップする必要がない場合、この手順はスキップしてかまいません。 このチュートリアルでは、スキーマをインポートします。

    ![ソース スキーマの検出](./media/tutorial-copy-data-portal/detect-source-schema.png)  
15. パイプラインが表示されているタブに移動するか、左側でパイプラインを選択します。

    ![パイプラインのタブ](./media/tutorial-copy-data-portal/pipeline-tab.png)
16. **[プロパティ]** ウィンドウの **[Source Dataset]\(ソース データセット\)** で **SourceBlobDataset** が選択されていることを確認します。 このページのデータをプレビューするには、**[データのプレビュー]** を選択します。 
    
    ![ソース データセット](./media/tutorial-copy-data-portal/source-dataset-selected.png)
17. **[シンク]** タブに移動し、**[+ 新規]** を選択してシンク データセットを作成します。 

    ![シンク データセット](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
18. **[新しいデータセット]** ウィンドウで **[Azure SQL Database]** を選択し、**[完了]** を選択します。 このチュートリアルでは、SQL データベースにデータをコピーします。 

    ![SQL データベースの選択](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
19. **[プロパティ]** ウィンドウの **[General]\(一般\)** タブの **[名前]** に「**OutputSqlDataset**」と入力します。 
    
    ![出力データセットの名前](./media/tutorial-copy-data-portal/output-dataset-name.png)
20. **[接続]** タブに移動し、**[リンクされたサービス]** の横の **[+ 新規]** を選択します。 データセットをリンクされたサービスに関連付ける必要があります。 リンクされたサービスには、Data Factory が実行時に SQL データベースに接続するために使用する接続文字列が含まれています。 データセットは、コンテナー、フォルダー、データのコピー先のファイル (オプション) を指定します。 
    
    ![リンクされたサービス](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
21. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。 

    a. **[名前]** に「**AzureSqlDatabaseLinkedService**」と入力します。

    b. **[サーバー名]** で、使用する SQL Server インスタンスを選択します。

    c. **[データベース名]** で、使用する SQL データベースを選択します。

    d. **[ユーザー名]** に、ユーザーの名前を入力します。

    e. **[パスワード]** に、ユーザーのパスワードを入力します。

    f. **[テスト接続]** を選択して接続をテストします。

    g. **[保存]** を選択して、リンクされたサービスを保存します。 
    
    ![新しいリンクされたサービスの保存](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

22. **[テーブル]** で **[dbo].[emp]** を選択します。 

    ![テーブル](./media/tutorial-copy-data-portal/select-emp-table.png)
23. **[スキーマ]** タブに移動し、**[スキーマのインポート]** を選択します。 

    ![[スキーマのインポート] を選択](./media/tutorial-copy-data-portal/import-destination-schema.png)
24. **[ID]** 列を選択し、**[削除]** を選択します。 この **ID** 列は、SQL データベース内の ID 列です。そのため、コピー アクティビティでデータをこの列に挿入する必要はありません。

    ![ID 列の削除](./media/tutorial-copy-data-portal/delete-id-column.png)
25. パイプラインがあるタブに移動し、**[Sink Dataset]\(シンク データセット\)** で **OutputSqlDataset** が選択されていることを確認します。

    ![パイプラインのタブ](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
26. **[プロパティ]** ウィンドウの一番下にある **[マップ]** タブに移動し、**[スキーマのインポート]** を選択します。 ソース ファイルの最初と 2 番目の列が SQL データベースの **FirstName** と **LastName** にマップされていることがわかります。

    ![スキーマのマップ](./media/tutorial-copy-data-portal/map-schemas.png)
27. パイプラインを検証するには、**[検証]** を選択します。 右上隅の右矢印を選択して、検証ウィンドウを閉じます。

    ![パイプラインの検証の出力](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
28. 右上隅にある **[コード]** を選択します。 パイプラインに関連付けられた JSON コードが表示されます。 

    ![[コード] ボタン](./media/tutorial-copy-data-portal/code-button.png)
29. 次のスニペットのような JSON コードが表示されます。 

    ```json
    {
        "name": "CopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToSql",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 20
                    },
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "SqlSink",
                            "writeBatchSize": 10000
                        },
                        "enableStaging": false,
                        "parallelCopies": 0,
                        "cloudDataMovementUnits": 0,
                        "translator": {
                            "type": "TabularTranslator",
                            "columnMappings": "Prop_0: FirstName, Prop_1: LastName"
                        }
                    },
                    "inputs": [
                        {
                            "referenceName": "SourceBlobDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputSqlDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ]
                }
            ]
        }
    }
    ```

## <a name="test-run-the-pipeline"></a>パイプラインをテスト実行する
Data Factory または独自の Visual Studio Team Services Git リポジトリにアーティファクト (リンクされたサービス、データセット、パイプライン) を発行する前に、パイプラインの実行をテストできます。 

1. パイプラインの実行をテストするには、ツール バーの **[テストの実行]** を選択します。 ウィンドウ下部の **[出力]** タブにパイプラインの実行の状態が表示されます。 

    ![パイプラインのテスト](./media/tutorial-copy-data-portal/test-run-output.png)
2. ソース ファイルからのデータが宛先 SQL データベースに挿入されていることを確認します。 

    ![SQL 出力の確認](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. 左側のウィンドウで、**[すべて公開]** を選択します。 これにより、作成したエンティティ (リンクされたサービス、データセット、パイプライン) が Data Factory に発行されます。

    ![[発行]](./media/tutorial-copy-data-portal/publish-button.png)
4. **[正常に発行されました]** というメッセージが表示されるまで待機します。 通知メッセージを表示するには、左のサイド バーで **[通知の表示]** タブを選択します。通知ウィンドウを閉じるには、**[閉じる]** を選択します。

    ![通知の表示](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>コード リポジトリを構成する
データ ファクトリ アーティファクトに関連付けられたコードを、Visual Studio Team Services コード リポジトリに発行できます。 この手順では、コード リポジトリを作成します。  VSTS 統合でのビジュアルの作成の詳細については、「[VSTS Git 統合での作成](author-visually.md#author-with-vsts-git-integration)」を参照してください。

Visual Studio Team Services コード リポジトリの作業が不要であれば、この手順はスキップしてかまいません。 前の手順で行った Data Factory への発行作業に進むことができます。 

1. 左上隅の **[Data Factory]** を選択するか、またはその横にある下矢印を使用して、**[Configure Code Repository]\(コード リポジトリの構成\)** を選択します。 

    ![コード リポジトリを構成する](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. **[Repository Settings]\(リポジトリ設定\)** ページで、次の手順を実行します。

    a. **[リポジトリの種類]** で **[Visual Studio Team Services Git]** を選択します。

    b. **[Visual Studio Team Services アカウント]** で、Visual Studio Team Services アカウントを選択します。

    c. **[プロジェクト名]** で、Visual Studio Team Services アカウント内のプロジェクトを選択します。

    d. **[Git repository name]\(Git リポジトリ名\)** で、データ ファクトリに関連付けられる Git リポジトリの名前として「**Tutorial2**」と入力します。

    e. **[Import existing Data Factory resources to repository]\(既存の Data Factory リソースをリポジトリにインポートする\)** チェック ボックスがオンになっていることを確認します。

    f. **[保存]** を選択して設定を保存します。 

    ![リポジトリ設定](./media/tutorial-copy-data-portal/repository-settings.png)
3. リポジトリで **[VSTS GIT]** が選択されていることを確認します。

    ![VSTS GIT の選択](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. Web ブラウザーの別のタブで **Tutorial2** リポジトリに移動します。 **adf_publish** と **master** の 2 つのブランチが表示されます。

    ![master ブランチと adf_publish ブランチ](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. Data Factory エンティティの JSON ファイルが **master** ブランチにあることを確認します。

    ![master ブランチ内のファイル](./media/tutorial-copy-data-portal/master-branch-files.png)
6. **adf_publish** ブランチにはまだ JSON ファイルがないことを確認します。 

    ![adf_publish ブランチ内のファイル](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. パイプラインの説明を **[説明]** に追加し、ツール バーの **[保存]** を選択します。 

    ![パイプラインの説明](./media/tutorial-copy-data-portal/pipeline-description.png)
8. これで、自分のユーザー名の付いたブランチが **Tutorial2** リポジトリに表示されます。 変更は master ブランチではなく、自身のブランチに対して行われました。 エンティティは master ブランチからのみ発行できます。

    ![自分のブランチ](./media/tutorial-copy-data-portal/your-branch.png)
9. **[同期]** ボタンにマウス ポインターを移動し (まだ選択しないでください)、**[変更の確定]** チェック ボックスをオンにします。次に **[同期]** を選択して、変更を master ブランチと同期します。 

    ![変更のコミットおよび同期](./media/tutorial-copy-data-portal/commit-and-sync.png)
10. **[Sync your changes]\(変更の同期\)** ウィンドウで、次の操作を実行します。 

    a. 更新された**パイプライン**の一覧に **CopyPipeline** が表示されていることを確認します。

    b. **[Publish changes after sync]\(同期後に変更を発行\)** がオンになっていることを確認します。 このチェック ボックスをオフにした場合、自分のブランチ内の変更が単に master ブランチと同期されます。 Data Factory には発行されません。 **[発行]** ボタンを使用すれば、後でそれらを発行できます。 このチェック ボックスがオンの場合、まず変更が master と同期され、次にそれらの変更が Data Factory に発行されます。

    c. **[同期]** を選択します。 

    ![変更を同期](./media/tutorial-copy-data-portal/sync-your-changes.png)
11. これで、**Tutorial2** リポジトリの **adf_publish** ブランチでファイルを確認できます。 このブランチでは Data Factory ソリューション用の Azure Resource Manager テンプレートも見つかります。 

    ![adf_publish ブランチ内のファイル一覧](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>パイプラインを手動でトリガーする
この手順では、前の手順で発行したパイプラインを手動でトリガーします。 

1. ツール バーの **[トリガー]** を選択し、**[Trigger Now]\(今すぐトリガー\)** を選択します。 **[Pipeline Run]\(パイプラインの実行\)** ページで **[完了]** を選択します。  

    ![パイプラインのトリガー](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. 左側の **[監視]** タブに移動します。 手動トリガーによってトリガーされたパイプラインの実行が表示されます。 **[アクション]** 列のリンクを使用して、アクティビティの詳細を表示したりパイプラインを再実行したりできます。

    ![パイプラインの実行を監視する](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、**[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択します。 この例では、アクティビティが 1 つだけなので、一覧に表示されるエントリは 1 つのみです。 コピー操作の詳細を確認するために、**[アクション]** 列にある **[詳細]** リンク (眼鏡アイコン) を選択します。 再度**パイプラインの実行**ビューに移動するには、一番上にある **[パイプライン]** を選択します。 表示を更新するには、**[最新の情報に更新]** を選択します。

    ![アクティビティの実行を監視する](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. SQL データベースの **emp** テーブルに 2 つの行が追加されていることを確認します。 

## <a name="trigger-the-pipeline-on-a-schedule"></a>スケジュールに基づいてパイプラインをトリガーする
このスケジュールでは、パイプラインのスケジュール トリガーを作成します。 このトリガーは、指定されたスケジュール (1 時間に 1 回、毎日など) に基づいてパイプラインを実行します。 この例では、指定された終了日時まで毎分実行されるようトリガーを設定します。 

1. 左側の **[編集]** タブに移動します。 

    ![[編集] タブ](./media/tutorial-copy-data-portal/edit-tab.png)
2. **[トリガー]** を選択し、**[New/Edit]\(新規作成/編集\)** を選択します。 パイプラインがアクティブでない場合、パイプラインに移動します。 

    ![トリガー オプション](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. **[Add Triggers]\(トリガーの追加\)** ウィンドウで、**[Choose trigger]\(トリガーの選択\)** を選択し、**[+ 新規]** を選択します。 

    ![[新規] ボタン](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. **[新しいトリガー]** ウィンドウで、次の手順を実行します。 

    a. **[名前]** に「**RunEveryMinute**」と入力します。

    b. **[終了]** で **[指定日]** を選択します。

    c. **[End On]\(終了日\)** のドロップダウン リストを選択します。

    d. **現在の日付**のオプションを選択します。 既定では、終了日は翌日に設定されています。

    e. **分**の部分を現在の日時の数分後に変更します。 トリガーは、変更を発行した後にのみアクティブ化されます。 これをわずか数分後に設定し、それまでに発行しなかった場合、トリガー実行は表示されません。

    f. **[適用]** を選択します。 

    ![トリガーのプロパティ](./media/tutorial-copy-data-portal/set-trigger-properties.png)

    g. **[アクティブ化]** オプションをオンにします。 このチェック ボックスを使用して、後で非アクティブ化したりアクティブ化したりできます。

    h. **[次へ]** を選択します。

    ![[アクティブ化] ボタン](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > パイプラインの実行ごとにコストが関連付けられるため、終了日は適切に設定してください。 
5. **[Trigger Run Parameters]\(トリガー実行のパラメーター\)** ページで、警告を確認し、**[完了]** を選択します。 この例のパイプラインにはパラメーターはありません。 

    ![トリガー実行のパラメーター](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
6. **[同期]** を選択して、自分のブランチ内の変更を master ブランチと同期します。 既定では、**[Publish changes after sync]\(同期後に変更を発行\)** がオンになっています。 **[同期]** を選択すると同時に、更新後のエンティティが master ブランチから Data Factory に発行されます。 トリガーは発行が成功して初めてアクティブ化されます。

    ![変更を同期](./media/tutorial-copy-data-portal/sync-your-changes-with-trigger.png) 
7. 左側の **[モニター]** タブに移動して、トリガーされたパイプラインの実行を確認します。 

    ![トリガーされたパイプラインの実行](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
8. **パイプラインの実行**ビューから**トリガーの実行**ビューに切り替えるには、**[Pipeline Runs]\(パイプラインの実行\)** を選択して **[Trigger Runs]\(トリガーの実行\)** を選択します。
    
    ![トリガーの実行](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
9. トリガーの実行が一覧で表示されます。 

    ![トリガーの実行の一覧](./media/tutorial-copy-data-portal/trigger-runs-list.png)
10. 指定された終了日時まで、(各パイプライン実行について) 1 分ごとに 2 つの行が **emp** テーブルに挿入されていることを確認します。 

## <a name="next-steps"></a>次の手順
このサンプルのパイプラインは、Blob Storage 内のある場所から別の場所にデータをコピーします。 以下の方法について学習しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * コピー アクティビティを含むパイプラインを作成します。
> * パイプラインをテスト実行します。
> * パイプラインを手動でトリガーします。
> * スケジュールに基づいてパイプラインをトリガーします。
> * パイプラインとアクティビティの実行を監視します。


オンプレミスからクラウドにデータをコピーする方法について学習するには、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
>[オンプレミスからクラウドにデータをコピーする](tutorial-hybrid-copy-portal.md)
