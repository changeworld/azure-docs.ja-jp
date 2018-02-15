---
title: "Azure Portal を使用して Data Factory パイプラインを作成する | Microsoft Docs"
description: "このチュートリアルでは、Azure Portal を使用してパイプラインを備えたデータ ファクトリを作成するための詳細な手順について説明します。 パイプラインでコピー アクティビティを使用して、Azure BLOB ストレージから Azure SQL データベースにデータをコピーします。 "
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: 8b5211e9c932221c6b6134e7e0627f4d7f964123
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Azure Data Factory を使用して Azure BLOB から Azure SQL Database にデータをコピーする
このチュートリアルでは、Azure Data Factory ユーザー インターフェイス (UI) を使用してデータ ファクトリを作成します。 このデータ ファクトリのパイプラインでは、Azure Blob Storage から Azure SQL Database にデータをコピーします。 このチュートリアルの構成パターンは、ファイルベースのデータ ストアからリレーショナル データ ストアへのコピーに適用されます。 ソースおよびシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する表を参照してください。

> [!NOTE]
> - Azure Data Factory を初めて使用する場合は、「[Azure Data Factory の概要](introduction.md)」を参照してください。
>
> - この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 の使用](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関する記事をご覧ください。

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * コピー アクティビティを含むパイプラインを作成します。
> * パイプラインのテストを実行します
> * パイプラインを手動でトリガーします
> * スケジュールに基づいてパイプラインをトリガーする
> * パイプラインとアクティビティの実行を監視します。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
* **Azure Storage アカウント**。 BLOB ストレージを**ソース** データ ストアとして使用します。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「[ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)」を参照してください。
* **Azure SQL データベース**。 データベースを**シンク** データ ストアとして使用します。 Azure SQL データベースがない場合は、[Azure SQL データベースの作成](../sql-database/sql-database-get-started-portal.md)に関する記事に書かれている手順を参照して作成してください。

### <a name="create-a-blob-and-a-sql-table"></a>BLOB と SQL テーブルを作成する

ここでは、次の手順を実行して、チュートリアルで使用する Azure BLOB と Azure SQL Database を準備します。

#### <a name="create-a-source-blob"></a>ソース BLOB を作成する

1. メモ帳を起動します。 次のテキストをコピーし、**emp.txt** ファイルとしてディスクに保存します。

    ```
    John,Doe
    Jane,Doe
    ```

2. Azure BLOB ストレージに **adftutorial** という名前のコンテナーを作成します。 このコンテナーに **input** という名前のフォルダーを作成します。 次に、**input** フォルダーに **emp.txt** ファイルをアップロードします。 Azure Portal を使用するか、または [Azure Storage Explorer](http://storageexplorer.com/) などのツールを使用して、これらのタスクを実行します。

#### <a name="create-a-sink-sql-table"></a>シンク SQL テーブルを作成する

1. 次の SQL スクリプトを使用して、**dbo.emp** テーブルを Azure SQL データベースに作成します。

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

2. Azure サービスに SQL サーバーへのアクセスを許可します。 Data Factory サービスから Azure SQL サーバーにデータを書き込むことができるように、Azure SQL サーバーで **[Azure サービスへのアクセスを許可]** の設定が**オン**になっていることを確認します。 この設定を確認して有効にするには、次の手順を実行します。

    1. 左側にある **[More services (その他のサービス)]** ハブをクリックし、**[SQL サーバー]** をクリックします。
    2. サーバーを選択し、**[設定]** の **[ファイアウォール]** をクリックします。
    3. **[ファイアウォールの設定]** ページの **[Azure サービスへのアクセスを許可]** で **[オン]** をクリックします。

## <a name="create-a-data-factory"></a>Data Factory を作成する。
この手順では、データ ファクトリを作成するほか、Azure Data Factory UI を起動してそのデータ ファクトリにパイプラインを作成します。 

1. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
2. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**ADFTutorialDataFactory**」と入力します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 名前フィールドで次のエラーが発生した場合は、データ ファクトリの名前を変更してください (yournameADFTutorialDataFactory など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-copy-data-portal/name-not-available-error.png)
3. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
4. **[リソース グループ]** について、次の手順のいずれかを行います。
     
      - **[Use existing (既存のものを使用)]**を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
      - **[新規作成]**を選択し、リソース グループの名前を入力します。   
         
        リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
4. **バージョン**として **[V2 (プレビュー)]** を選択します。
5. データ ファクトリの **場所** を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。
6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **Create** をクリックしてください。      
8. ダッシュボードに、**[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-copy-data-portal/deploying-data-factory.png)
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/tutorial-copy-data-portal/data-factory-home-page.png)
10. **[Author & Monitor]\(作成と監視\)** タイルをクリックして、別のタブで Azure Data Factory ユーザー インターフェイス (UI) を起動します。

## <a name="create-a-pipeline"></a>パイプラインを作成する。
この手順では、コピー アクティビティが含まれたパイプラインをデータ ファクトリに作成します。 コピー アクティビティによって、Azure Blob Storage から Azure SQL Database にデータがコピーされます。 [クイックスタート チュートリアル](quickstart-create-data-factory-portal.md)では、次の手順でパイプラインを作成しました。

1. リンクされたサービスを作成します。 
2. 入力データセットと出力データセットを作成します。
3. 次に、パイプラインを作成します。

このチュートリアルでは、最初にパイプラインを作成します。また、パイプラインの構成に必要な場合にリンクされたサービスとデータセットを作成します。 

1. 開始ページで、**[Create Pipeline]\(パイプラインの作成\)** タイルをクリックします。 

   ![[Create Pipeline]\(パイプラインの作成\) タイル](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
3. パイプラインの**プロパティ** ウィンドウで、パイプラインの **[名前]** を「**CopyPipeline**」に設定します。

    ![パイプライン名](./media/tutorial-copy-data-portal/pipeline-name.png)
4. **[アクティビティ]** ツール ボックスで **[DataFlow]\(データ フロー\)** カテゴリを展開し、ツール ボックスからパイプライン デザイナー画面に **[コピー]** アクティビティをドラッグ アンド ドロップします。 

    ![コピー アクティビティのドラッグ アンド ドロップ](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
5. **プロパティ** ウィンドウの **[General]\(一般\)** タブで、アクティビティの名前として「**CopyFromBlobToSql**」を指定します。

    ![アクティビティ名](./media/tutorial-copy-data-portal/activity-name.png)
6. **[ソース]** タブに切り替えます。**[+ 新規]** をクリックして、ソース データセットを作成します。 

    ![新しいソース データセットのメニュー](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
7. **[新しいデータセット]** ウィンドウで **[Azure BLOB ストレージ]** を選択し、**[完了]** をクリックします。 ソース データは Azure BLOB ストレージにあるので、ソース データセットには Azure Blob Storage を選択します。 

    ![[Azure Blob Storage] の選択](./media/tutorial-copy-data-portal/select-azure-storage.png)
8. アプリケーションで **[AzureBlob1]** という名前の新しい**タブ**が開かれたことがわかります。

    ![[AzureBlob1] タブ ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
9. 下部にある**プロパティ** ウィンドウの **[General]\(一般\)** タブで、**[名前]** として「**SourceBlobDataset**」を指定します。

    ![データセットの名前](./media/tutorial-copy-data-portal/dataset-name.png)
10. プロパティ ウィンドウで **[接続]** タブに切り替えます。 **[リンクされたサービス]** ボックスの横にある **[+ 新規]** をクリックします。 リンクされたサービスは、データ ストアまたは計算をデータ ファクトリにリンクします。 ここでは、Azure ストレージ アカウントをデータ ストアにリンクする、Azure Storage のリンクされたサービスを作成します。 リンクされたサービスには、Data Factory サービスが実行時に BLOB ストレージに接続するために使用する接続情報が含まれています。 データセットは、コンテナー、フォルダー、ソース データが含まれているファイル (オプション) を指定します。 

    ![新しいリンクされたサービスのボタン](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
12. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。 

    1. **[名前]** フィールドで「**AzureStorageLinkedService**」と指定します。 
    2. **[ストレージ アカウント名]** フィールドで Azure ストレージ アカウントを選択します。
    3. **[テスト接続]** をクリックして、Azure ストレージ アカウントへの接続をテストします。  
    4. **[保存]** をクリックして、リンクされたサービスを保存します。

        ![新しい Azure Storage のリンクされたサービス](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
13. **[ファイル パス]** フィールドの **[参照]** をクリックします。  

    ![ファイルの [参照] ボタン](./media/tutorial-copy-data-portal/file-browse-button.png)
14. **adftutorial/input** フォルダーに移動して **emp.txt** ファイルを選択し、**[完了]** をクリックします。 または、emp.txt をダブルクリックすることができます。 

    ![入力ファイルの選択](./media/tutorial-copy-data-portal/select-input-file.png)
15. **[File format]\(ファイル形式\)** が **[Text format]\(テキスト形式\)** に設定されており、**[列区切り記号]** が **[コンマ (`,`)]** に設定されていることを確認します。 ソース ファイルで異なる行および列の区切り記号が使用されている場合、**[File format]\(ファイル形式\)** フィールドで **[Detect Text Format]\(テキスト形式の検出\)** をクリックできます。 データのコピー ツールによって、ファイル形式と区切り記号が自動で検出されます。 これらの値は上書きすることもできます。 このページのデータは、**[データのプレビュー]** をクリックしてプレビューできます。

    ![テキスト形式の検出](./media/tutorial-copy-data-portal/detect-text-format.png)
17. プロパティ ウィンドウで **[スキーマ]** タブに切り替え、**[スキーマのインポート]** をクリックします。 アプリケーションによってソース ファイル内の 2 つの列が検出されたことがわかります。 ソース データ ストアからシンク データ ストアに列をマップできるように、ここでスキーマをインポートします。 列をマップする必要がない場合、この手順はスキップしてかまいません。 このチュートリアルでは、スキーマをインポートします。

    ![ソース スキーマの検出](./media/tutorial-copy-data-portal/detect-source-schema.png)  
19. 次に、**パイプラインのタブ**に切り替えるか、または左側の**ツリービュー**にあるパイプラインをクリックします。  

    ![パイプラインのタブ](./media/tutorial-copy-data-portal/pipeline-tab.png)
20. プロパティ ウィンドウの [Source Dataset]\(ソース データセット\) フィールドで **SourceBlobDataset** が選択されていることを確認します。 このページのデータは、**[データのプレビュー]** をクリックしてプレビューできます。 
    
    ![ソース データセット](./media/tutorial-copy-data-portal/source-dataset-selected.png)
21. **[シンク]** タブに切り替えて、**[新規]** をクリックしてシンク データセットを作成します。 

    ![新しいシンク データセットのメニュー](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
22. **[新しいデータセット]** ウィンドウで **[Azure SQL Database]** を選択し、**[完了]** をクリックします。 このチュートリアルではデータを Azure SQL データベースにコピーします。 

    ![Azure SQL Database の選択](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
23. プロパティ ウィンドウの **[General]\(一般\)** タブで、名前として「**OutputSqlDataset**」を設定します。 
    
    ![出力データセットの名前](./media/tutorial-copy-data-portal/output-dataset-name.png)
24. **[接続]** タブに切り替えて、**[リンクされたサービス]** で **[新規]** をクリックします。 データセットをリンクされたサービスに関連付ける必要があります。 リンクされたサービスには、Data Factory サービスが実行時に Azure SQL データベースに接続するために使用する接続文字列が含まれています。 データセットは、コンテナー、フォルダー、データのコピー先のファイル (オプション) を指定します。 
    
    ![新しいリンクされたサービスのボタン](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
25. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。 

    1. **[名前]** フィールドに「**AzureSqlDatabaseLinkedService**」と入力します。 
    2. **[サーバー名]** フィールドで Azure SQL サーバーを選択します。
    4. **[データベース名]** フィールドで Azure SQL データベースを選択します。 
    5. **[ユーザー名]** フィールドにユーザーの名前を入力します。 
    6. **[パスワード]** フィールドに、ユーザーのパスワードを入力します。 
    7. **[テスト接続]** をクリックして接続をテストします。
    8. **[保存]** をクリックして、リンクされたサービスを保存します。 
    
        ![新しい Azure SQL Database のリンクされたサービス](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

26. **[テーブル]** で **[dbo].[emp]** を選択します。 

    ![emp テーブルの選択](./media/tutorial-copy-data-portal/select-emp-table.png)
27. **[スキーマ]** タブに切り替えて、[スキーマのインポート] をクリックします。 

    ![宛先スキーマのインポート](./media/tutorial-copy-data-portal/import-destination-schema.png)
28. **[ID]** 列を選択して、**[削除]** をクリックします。 この ID 列は、SQL データベース内の ID 列です。そのため、コピー アクティビティでデータをこの列に挿入する必要はありません。

    ![ID 列の削除](./media/tutorial-copy-data-portal/delete-id-column.png)
30. **パイプライン**があるタブに切り替えて、**[Sink Dataset]\(シンク データセット\)** で **OutputSqlDataset** が選択されていることを確認します。

    ![パイプラインのタブ](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
31. 下部にあるプロパティ ウィンドウで **[マップ]** タブに切り替え、**[スキーマのインポート]** をクリックします。 ソース ファイルの最初と 2 番目の列が SQL データベースの **FirstName** フィールドと **LastName** フィールドにマップされたことがわかります。

    ![スキーマのマップ](./media/tutorial-copy-data-portal/map-schemas.png)
33. **[検証]** ボタンをクリックして、パイプラインを検証します。 **右矢印**をクリックして、検証ウィンドウを閉じます。

    ![パイプラインの検証の出力](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
34. 右隅にある **[コード]** ボタンをクリックします。 パイプラインに関連付けられた JSON コードが表示されます。 

    ![[コード] ボタン](./media/tutorial-copy-data-portal/code-button.png)
35. 次のスニペットのような JSON コードが表示されます。  

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

## <a name="test-run-the-pipeline"></a>パイプラインのテストを実行する
Data Factory または独自の VSTS Git リポジトリにアーティファクト (リンクされたサービス、データセット、パイプライン) を発行する前に、パイプラインの実行をテストできます。 

1. パイプラインの実行をテストするには、ツール バーの **[テストの実行]** をクリックします。 下部にあるウィンドウの **[出力]** タブにパイプラインの実行の状態が表示されます。 

    ![[テストの実行] ボタン](./media/tutorial-copy-data-portal/test-run-output.png)
2. ソース ファイルからのデータが宛先 SQL データベースに挿入されていることを確認します。 

    ![SQL 出力の確認](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. 左側のウィンドウで **[すべて発行]** をクリックします。 これにより、作成したエンティティ (リンクされたサービス、データセット、パイプライン) が Azure Data Factory に発行されます。

    ![[発行] ボタン](./media/tutorial-copy-data-portal/publish-button.png)
4. **[正常に発行されました]** というメッセージが表示されるまで待機します。 通知メッセージを表示するには、左のサイド バーで **[通知の表示]** タブをクリックします。 **[X]** をクリックして通知ウィンドウを閉じます。

    ![通知の表示](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>コード リポジトリを構成する
データ ファクトリ アーティファクトに関連付けられたコードを、Visual Studio Team System (VSTS) コード レポジトリに発行できます。 この手順では、コード リポジトリを作成します。 

VSTS コード リポジトリを使用しない場合、この手順をスキップして、前の手順で行ったとおり Data Factory への発行を続行できます。 

1. 左隅にある **[Data Factory]** またはその横にある下向き矢印をクリックして、**[Configure Code Repository]\(コード リポジトリの構成\)** をクリックします。 

    ![[コード] ボタン](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. **[Repository Settings]\(リポジトリ設定\)** ページで、次の手順を行います。 
    1. **[リポジトリの種類]** フィールドで **[Visual Studio Team Services Git]** を選択します。
    2. **[Visual Studio Team Services アカウント]** フィールドで VSTS アカウントを選択します。
    3. **[プロジェクト名]** フィールドで VSTS アカウント内のプロジェクトを選択します。
    4. データ ファクトリに関連付けられる **Git リポジトリの名前**として「**Tutorial2**」と入力します。 
    5. **[Import existing Data Factory resources to repository]\(既存の Data Factory リソースをリポジトリにインポートする\)** オプションが選択されていることを確認します。 
    6. **[保存]** をクリックして設定を保存します。 

        ![リポジトリ設定](./media/tutorial-copy-data-portal/repository-settings.png)
3. リポジトリで **[VSTS GIT]** が選択されていることを確認します。

    ![VSTS GIT の選択](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. Web ブラウザーの別のタブで **Tutorial2** リポジトリに移動すると、**master** と **adf_publish** という 2 つのブランチが表示されます。

    ![master ブランチと adf_publish ブランチ](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. Data Factory エンティティの **JSON ファイル**が **master** ブランチにあることを確認します。

    ![master ブランチ内のファイル](./media/tutorial-copy-data-portal/master-branch-files.png)
6. **adf_publish** ブランチにはまだ **JSON ファイル**がないことを確認します。 

    ![adf_publish ブランチ内のファイル](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. **パイプライン**の**説明**を追加し、ツール バーの **[保存]** ボタンをクリックします。 

    ![パイプラインの説明の追加](./media/tutorial-copy-data-portal/pipeline-description.png)
8. これで、自分のユーザー名の付いた**ブランチ**が **Tutorial2** リポジトリに表示されます。 変更は master ブランチではなく、自身のブランチに対して行われました。 エンティティは master ブランチからのみ発行できます。

    ![自分のブランチ](./media/tutorial-copy-data-portal/your-branch.png)
9. **[同期]** ボタンに移動し (まだクリックしないでください)、**[変更の確定]** オプションをオンにします。次に **[同期]** ボタンをクリックして、変更を **master** ブランチと同期します。 

    ![変更のコミットおよび同期](./media/tutorial-copy-data-portal/commit-and-sync.png)
9. [Sync your changes]\(変更の同期\) ウィンドウで、次の操作を行います。 

    1. 更新されたパイプラインの一覧に **CopyPipeline** が表示されていることを確認します。
    2. **[Publish changes after sync]\(同期後に変更を発行\)** がオンになっていることを確認します。 このオプションをオフにした場合、自分のブランチの変更が master ブランチに同期されるだけで、それらの変更は Data Factory サービスに発行されません。 **[発行]** ボタンを使用すれば、後でそれらを発行できます。 このオプションがオンの場合、まず変更が master に同期され、次にそれらの変更が Data Factory サービスに発行されます。
    3. **[同期]** をクリックします。 

    ![[Sync your changes]\(変更の同期\) ウィンドウ](./media/tutorial-copy-data-portal/sync-your-changes.png)
10. これで、**Tutorial2** リポジトリの **adf_publish** ブランチでファイルを確認できます。 このブランチでは Data Factory ソリューション用の Azure Resource Manager テンプレートも見つかります。  

    ![adf_publish ブランチ内のファイル](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>パイプラインを手動でトリガーする
この手順では、前の手順で発行したパイプラインを手動でトリガーします。 

1. ツール バーの **[トリガー]** をクリックし、**[Trigger Now]\(今すぐトリガー\)** をクリックします。 **[Pipeline Run]\(パイプラインの実行\)** ページで **[完了]** をクリックします。  

    ![[Trigger Now]\(今すぐトリガー\) メニュー](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. 左側で **[監視]** タブに切り替えます。 手動トリガーによってトリガーされたパイプラインの実行が表示されます。 [アクション] 列のリンクを使用して、アクティビティの詳細を表示したりパイプラインを再実行したりできます。

    ![パイプラインの実行の監視](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. パイプラインの実行に関連付けられているアクティビティの実行を表示するために、**[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクをクリックします。 この例では、アクティビティが 1 つだけなので、一覧に表示されるエントリは 1 つのみです。 コピー操作の詳細を確認するために、**[アクション]** 列にある **[詳細]** リンク (眼鏡アイコン) をクリックします。 上部の **[パイプライン]** をクリックすると、**パイプラインの実行**ビューに戻すことができます。 ビューを更新するには、**[Refresh]\(最新の情報に更新\)** をクリックします。

    ![アクティビティの実行の表示](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. Azure SQL データベースの **emp** テーブルに 2 つの行が追加されていることを確認します。 

## <a name="trigger-the-pipeline-on-a-schedule"></a>スケジュールに基づいてパイプラインをトリガーする
このスケジュールでは、パイプラインのスケジューラ トリガーを作成します。 このトリガーは、指定されたスケジュール (1 時間に 1 回、毎日など) に基づいてパイプラインを実行します。 この例では、指定された終了日時まで毎分実行されるようトリガーを設定します。 

1. 左側で **[編集]** タブに切り替えます。 

    ![[編集] タブ](./media/tutorial-copy-data-portal/edit-tab.png)
2. **[トリガー]** をクリックし、**[New/Edit]\(新規作成/編集\)** を選択します。 パイプラインがアクティブでない場合、アクティブに切り替えます。 

    ![[トリガー] の [New/Edit]\(新規作成/編集\) メニュー](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. **[Add Triggers]\(トリガーの追加\)** ウィンドウで、**[Choose trigger...]\(トリガーの選択...\)**、**[+ 新規]** の順にクリックします。 

    ![トリガーの追加 - 新しいトリガー](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. **[新しいトリガー]** ウィンドウで、次の手順を実行します。 

    1. **[名前]** を「**RunEveryMinute**」に設定します。
    2. **[終了]** で **[指定日]** を選択します。 
    3. **[End On]\(終了日\)** のドロップダウン リストをクリックします。
    4. **現在の日付**を選択します。 既定では、終了日は翌日に設定されています。 
    5. **分**の部分を現在の日時の数分後に変更します。 トリガーは、変更を発行した後にのみアクティブ化されます。 そのため、これをわずか数分後に設定し、それまでに発行しなかった場合、トリガー実行は表示されません。  
    6. **[Apply]**をクリックします。 

        ![トリガーのプロパティの設定](./media/tutorial-copy-data-portal/set-trigger-properties.png)
    7. **[アクティブ化]** オプションをオンにします。 このチェック ボックスを使用して、後で非アクティブ化したりアクティブ化したりできます。
    8. **[次へ]** をクリックします。

        ![トリガーのアクティブ化 - 次へ](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > 各パイプライン実行に関連したコストがかかります。 そのため、終了日は適切に設定してください。 
6. **[Trigger Run Parameters]\(トリガー実行のパラメーター\)** ページで、警告を確認し、**[完了]** をクリックします。 この例のパイプラインにはパラメーターはありません。 

    ![パイプラインのパラメーター](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
7. **[同期]** をクリックして、自分のブランチ内の変更を master ブランチと同期します。 既定では、**[Publish changes after sync]\(同期後に変更を発行\)** がオンになっています。 そのため、**[同期]** を選択すると同時に、更新後のエンティティが master ブランチから Azure Data Factory サービスに発行されます。 トリガーは発行が成功して初めてアクティブ化されます。

    ![トリガーの発行](./media/tutorial-copy-data-portal/sync-your-changes-with-trigger.png) 
9. 左側で **[モニター]** タブに切り替えて、トリガーされたパイプラインの実行を確認します。 

    ![トリガーされたパイプラインの実行](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
9. パイプラインの実行ビューからトリガー実行ビューに切り替えるには、[Pipeline Runs]\(パイプラインの実行\) をクリックして [Trigger Runs]\(トリガーの実行\) を選択します。
    
    ![[Trigger Runs]\(トリガーの実行\) メニュー](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
10. トリガーの実行が一覧で表示されます。 

    ![トリガーの実行の一覧](./media/tutorial-copy-data-portal/trigger-runs-list.png)
11. 指定された終了日時まで、(各パイプライン実行について) 1 分ごとに 2 つの行が **emp** テーブルに挿入されていることを確認します。 

## <a name="next-steps"></a>次の手順
このサンプルのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。 以下の方法について学習しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * コピー アクティビティを含むパイプラインを作成します。
> * パイプラインのテストを実行します
> * パイプラインを手動でトリガーします
> * スケジュールに基づいてパイプラインをトリガーする
> * パイプラインとアクティビティの実行を監視します。


次のチュートリアルに進んで、オンプレミスからクラウドにデータをコピーする方法について学習しましょう。 

> [!div class="nextstepaction"]
>[オンプレミスからクラウドにデータをコピーする](tutorial-hybrid-copy-portal.md)
