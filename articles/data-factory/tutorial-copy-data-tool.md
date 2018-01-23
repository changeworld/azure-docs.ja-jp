---
title: "Azure のデータのコピー ツールを使用してデータをコピーする | Microsoft Docs"
description: "Azure データ ファクトリを作成し、データのコピー ツールを使用して Azure Blob Storage から Azure SQL Database にデータをコピーします。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: a8c7035ebf462bb168147e9d8eb60742333ce8b8
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-copy-data-tool"></a>データのコピー ツールを使用して Azure BLOB から Azure SQL Database にデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [バージョン 2 - プレビュー](tutorial-copy-data-tool.md)

このチュートリアルでは、Azure Portal を使用してデータ ファクトリを作成します。 次に、データのコピー ツールを使用して、Azure BLOB ストレージから Azure SQL データベースにデータをコピーするパイプラインを作成します。 

> [!NOTE]
> - Azure Data Factory を初めて使用する場合は、「[Azure Data Factory の概要](introduction.md)」を参照してください。
>
> - この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 の使用](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関する記事をご覧ください。


このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * データのコピー ツールを使用してパイプラインを作成します。
> * パイプラインとアクティビティの実行を監視します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
* **Azure Storage アカウント**。 BLOB ストレージを**ソース** データ ストアとして使用します。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「[ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)」を参照してください。
* **Azure SQL データベース**。 データベースを**シンク** データ ストアとして使用します。 Azure SQL データベースがない場合は、[Azure SQL データベースの作成](../sql-database/sql-database-get-started-portal.md)に関する記事に書かれている手順を参照して作成してください。

### <a name="create-a-blob-and-a-sql-table"></a>BLOB と SQL テーブルを作成する

ここでは、次の手順を実行して、チュートリアルで使用する Azure BLOB と Azure SQL Database を準備します。

#### <a name="create-a-source-blob"></a>ソース BLOB を作成する

1. メモ帳を起動します。 次のテキストをコピーし、**inputEmp.txt** ファイルとしてディスクに保存します。

    ```
    John|Doe
    Jane|Doe
    ```

2. [Azure Storage Explorer](http://storageexplorer.com/) などのツールを使用して **adfv2tutorial** コンテナーを作成し、このコンテナーに **inputEmp.txt** ファイルをアップロードします。

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

2. Azure サービスに SQL サーバーへのアクセスを許可します。 Data Factory サービスから Azure SQL サーバーにデータを書き込むことができるように、Azure SQL サーバーで **[Azure サービスへのアクセスを許可]** の設定が有効になっていることを確認します。 この設定を確認して有効にするには、次の手順を実行します。

    1. 左側にある **[More services (その他のサービス)]** ハブをクリックし、**[SQL サーバー]** をクリックします。
    2. サーバーを選択し、**[設定]** の **[ファイアウォール]** をクリックします。
    3. **[ファイアウォールの設定]** ページの **[Azure サービスへのアクセスを許可]** で **[オン]** をクリックします。

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**ADFTutorialDataFactory**」と入力します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 名前フィールドで次のエラーが発生した場合は、データ ファクトリの名前を変更してください (yournameADFTutorialDataFactory など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-copy-data-tool/name-not-available-error.png)
3. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
4. **[リソース グループ]** について、次の手順のいずれかを行います。
     
      - **[Use existing (既存のものを使用)]**を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
      - **[新規作成]**を選択し、リソース グループの名前を入力します。   
         
      リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
4. **バージョン**として **[V2 (プレビュー)]** を選択します。
5. データ ファクトリの **場所** を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他の場所/リージョンに配置できます。
6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **Create** をクリックしてください。
8. ダッシュボードに、**[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-copy-data-tool/deploying-data-factory.png)
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/tutorial-copy-data-tool/data-factory-home-page.png)
10. **[Author & Monitor]\(作成と監視\)** タイルをクリックして、別のタブで Azure Data Factory ユーザー インターフェイス (UI) を起動します。 

## <a name="use-copy-data-tool-to-create-a-pipeline"></a>データのコピー ツールを使用してパイプラインを作成する

1. 開始ページで **[データのコピー]** タイルをクリックして、データのコピー ツールを起動します。 

   ![データのコピー ツールのタイル](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. データのコピー ツールの**プロパティ** ページで、**[タスク名]** に「**CopyFromBlobToSqlPipeline**」と指定し、**[次へ]** をクリックします。 タスク名で指定した名前のパイプラインが Data Factory UI によって作成されます。 

    ![データのコピー ツール- プロパティ ページ](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. **[ソース データ ストア]** ページで **[Azure Blob Storage]** を選択し、**[次へ]** をクリックします。 ソース データは Azure BLOB ストレージにあります。 

    ![[ソース データ ストア] ページ](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. **[Specify the Azure Blob storage account]\(Azure BLOB ストレージ アカウントの指定\)** ページで、次の手順を実行します。 
    1. **[接続名]** に「**AzureStorageLinkedService**」と入力します。
    2. ドロップダウン リストから**ストレージ アカウント名**を選択します。
    3. **[次へ]** をクリックします。 

        ![BLOB ストレージ アカウントの指定](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

        リンクされたサービスは、データ ストアまたは計算をデータ ファクトリにリンクします。 ここでは、Azure ストレージ アカウントをデータ ストアにリンクする、Azure Storage のリンクされたサービスを作成します。 リンクされたサービスには、Data Factory サービスが実行時に BLOB ストレージに接続するために使用する接続情報が含まれています。 データセットは、コンテナー、フォルダー、ソース データが含まれているファイル (オプション) を指定します。 
5. **[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)** ページで、次の手順を行います。
    
    1. **adfv2tutorial/input** フォルダーに移動します。
    2. **inputEmp.txt** を選択します。
    3. **[選択]** をクリックします。 または、**inputEmp.txt** をダブルクリックすることができます。 
    4. **[次へ]** をクリックします。 

    ![入力ファイルまたはフォルダーの選択](./media/tutorial-copy-data-tool/choose-input-file-folder.png)
6. **[File format settings]\(ファイル形式設定\)** ページで、列区切り記号と行区切り記号がツールによって自動的に検出されたことを確認して、**[次へ]** をクリックします。 このページでは、データのプレビューと入力データのスキーマの表示を行うこともできます。 

    ![[File format settings]\(ファイル形式設定\) ページ](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. **[Destination data store]\(コピー先データ ストア\)** ページで **[Azure SQL Database]** を選択し、**[次へ]** をクリックします。 

    ![[Destination data store]\(コピー先データ ストア\) ページ](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. **[Specify the Azure SQL database]\(Azure SQL データベースの指定\)** ページで、次の手順を実行します。 

    1. **[接続名]** に「**AzureSqlDatabaseLinkedService**」と指定します。 
    2. **[サーバー名]** で Azure SQL サーバーを選択します。
    3. **[データベース名]** で Azure SQL データベースを選択します。
    4. **[ユーザー名]** でユーザーの名前を指定します。
    5. **[パスワード]** でユーザーのパスワードを指定します。
    6. **[次へ]** をクリックします。 

        ![Azure SQL データベースの指定](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

        データセットをリンクされたサービスに関連付ける必要があります。 リンクされたサービスには、Data Factory サービスが実行時に Azure SQL データベースに接続するために使用する接続文字列が含まれています。 データセットは、コンテナー、フォルダー、データのコピー先のファイル (オプション) を指定します。
9.  **[テーブル マッピング]** ページで、**[dbo].[emp]** を選択して **[次へ]** をクリックします。 

    ![[テーブル マッピング] ページ](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. **[スキーマ マッピング]** ページで、入力ファイルの最初の列と 2 番目の列が **emp** テーブルの **FirstName** 列と **LastName** 列にマップされていることがわかります。 

    ![[スキーマ マッピング] ページ](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. **[設定]** ページで **[次へ]** をクリックします。 

    ![[設定] ページ](./media/tutorial-copy-data-tool/settings-page.png)
12. **[概要]** ページで設定を確認し、**[次へ]** をクリックします。

    ![概要ページ](./media/tutorial-copy-data-tool/summary-page.png)
13. **[Deployment]\(デプロイ\)** ページで、**[監視]** をクリックしてパイプライン (タスク) を監視します。

    ![[Deployment]\(デプロイ\) ページ](./media/tutorial-copy-data-tool/deployment-page.png)
14. 左側の **[監視]** タブが自動的に選択されたことがわかります。 **[アクション]** 列で、アクティビティの実行の詳細を表示するリンクとパイプラインを再実行するリンクを確認します。 **[最新の情報に更新]** をクリックして、リストを更新します。 

    ![パイプラインの実行を監視する](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. パイプラインの実行に関連付けられているアクティビティの実行を表示するために、**[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクをクリックします。 パイプライン内のアクティビティ (コピー アクティビティ) は 1 つだけなので、エントリは 1 つのみです。 コピー操作の詳細を確認するために、**[アクション]** 列にある **[詳細]** リンク (眼鏡アイコン) をクリックします。 [Pipeline Runs]\(パイプラインの実行\) ビューに戻るには、上部の **[パイプライン]** リンクをクリックします。 ビューを更新するには、**[Refresh]\(最新の情報に更新\)** をクリックします。 

    ![アクティビティの実行を監視する](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. 左側の **[編集]** タブをクリックして、編集モードに切り替えます。 ツールによって作成されたリンクされたサービス、データセット、パイプラインをエディターで更新できます。 **[コード]** をクリックすると、エディターで開かれているエンティティに関する JSON コードが表示されます。 Data Factory UI におけるこれらのエンティティの編集について詳しくは、[このチュートリアルの Azure Portal バージョン](tutorial-copy-data-portal.md)を参照してください。

    ![[編集] タブ](./media/tutorial-copy-data-tool/edit-tab.png)
17. Azure SQL データベースの **emp** テーブルにデータが挿入されたことを確認します。 

    ![SQL 出力の確認](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>次の手順
このサンプルのパイプラインでは、Azure BLOB ストレージから Azure SQL データベースにデータがコピーされます。 以下の方法について学習しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * データのコピー ツールを使用してパイプラインを作成します。
> * パイプラインとアクティビティの実行を監視します。

次のチュートリアルに進んで、オンプレミスからクラウドにデータをコピーする方法について学習しましょう。 

> [!div class="nextstepaction"]
>[オンプレミスからクラウドにデータをコピーする](tutorial-hybrid-copy-data-tool.md)