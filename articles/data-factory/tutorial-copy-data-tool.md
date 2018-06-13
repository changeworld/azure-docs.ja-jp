---
title: Azure のデータのコピー ツールを使用してデータをコピーする | Microsoft Docs
description: Azure Data Factory を作成し、データのコピー ツールを使用して Azure Blob Storage から SQL データベースにデータをコピーします。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: d2f1d089c6a08a1dc90f82fd9d1c3cb2b6f6dc0a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
ms.locfileid: "30171807"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>データのコピー ツールを使用して Azure Blob Storage から SQL データベースにデータをコピーする
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [バージョン 2 - プレビュー](tutorial-copy-data-tool.md)

このチュートリアルでは、Azure Portal を使用してデータ ファクトリを作成します。 次に、データのコピー ツールを使用して、Azure Blob Storage から SQL データベースにデータをコピーするパイプラインを作成します。 

> [!NOTE]
> Azure Data Factory を初めて使用する場合は、「[Azure Data Factory の概要](introduction.md)」を参照してください。
>
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 Data Factory のバージョン 1 (一般公開版) を使用する場合は、[Data Factory バージョン 1 の概要](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関する記事をご覧ください。


このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * データのコピー ツールを使用してパイプラインを作成します。
> * パイプラインとアクティビティの実行を監視します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**: Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* **Azure Storage アカウント**: "_ソース_" データ ストアとして Blob Storage を使用します。 Azure Storage アカウントがない場合は、「[ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)」の手順をご覧ください。
* **Azure SQL Database**: "_シンク_" データ ストアとして SQL データベースを使用します。 SQL データベースがない場合は、「[SQL Database の作成](../sql-database/sql-database-get-started-portal.md)」の手順を参照してください。

### <a name="create-a-blob-and-a-sql-table"></a>BLOB と SQL テーブルを作成する

次の手順を実行して、チュートリアルで使用する Blob Storage と SQL データベースを準備します。

#### <a name="create-a-source-blob"></a>ソース BLOB を作成する

1. **メモ帳**を起動します。 次のテキストをコピーし、**inputEmp.txt** というファイル名でディスクに保存します。

    ```
    John|Doe
    Jane|Doe
    ```

2. **adfv2tutorial** という名前のコンテナーを作成し、そこに inputEmp.txt ファイルをアップロードします。 この作業は、[Azure Storage Explorer](http://storageexplorer.com/) をはじめとするさまざまなツールを使用して実行できます。

#### <a name="create-a-sink-sql-table"></a>シンク SQL テーブルを作成する

1. 次の SQL スクリプトを使用して、**dbo.emp** という名前のテーブルを SQL データベースに作成します。

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

2. Azure サービスに SQL Server へのアクセスを許可します。 SQL Server が実行されているサーバーの **[Azure サービスへのアクセスを許可]** 設定が有効になっていることを確認します。 この設定により、Data Factory が SQL Server インスタンスにデータを書き込むことができます。 この設定を確認して有効にするには、次の手順を実行します。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 画面の左側にある **[その他のサービス]** を選択し、**[SQL Server]** を選択します。

    b. 目的のサーバーを選択し、**[設定]** > **[ファイアウォール]** を選択します。

    c. **[ファイアウォール設定]** ページの **[Azure サービスへのアクセスを許可]** オプションを **[オン]** に設定します。

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. 左側のメニューで、**[+ 新規]** > **[データ + 分析]** > **[Data Factory]** を選択します。 
   
   ![新しいデータ ファクトリの作成](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**ADFTutorialDataFactory**」と入力します。 
      
     ![新しいデータ ファクトリ](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   データ ファクトリの名前は "_グローバルに一意_" にする必要があります。 次のエラー メッセージが表示される場合があります。
   
   ![[新しいデータ ファクトリ] のエラー メッセージ](./media/tutorial-copy-data-tool/name-not-available-error.png)

   データ ファクトリの名前の値に関するエラー メッセージが表示された場合は、別の名前を入力してください。 たとえば、_**yourname**_**ADFTutorialDataFactory** という名前を使用します。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関するページを参照してください。
3. 新しいデータ ファクトリの作成先となる Azure **サブスクリプション**を選択します。 
4. **[リソース グループ]** で、次の手順のいずれかを行います。
     
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。

    b. **[新規作成]** を選択し、リソース グループの名前を入力します。 
         
    リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。

5. **[バージョン]** で、**[V2 (プレビュー)]** を選択します。
6. **[場所]** で、データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストア (Azure Storage、SQL Database など) やコンピューティング (Azure HDInsight など) は、他の場所やリージョンに存在していてもかまいません。
7. **[ダッシュボードにピン留めする]** をオンにします。 
8. **[作成]** を選択します。
9. ダッシュボードの **[Deploying Data Factory]\(データ ファクトリをデプロイしています\)** タイルに処理の状態が表示されます。

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. 作成が完了すると、**[Data Factory]** ホーム ページが表示されます。
   
    ![データ ファクトリのホーム ページ](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. 別のタブで Azure Data Factory ユーザー インターフェイス (UI) を起動するには、**[Author & Monitor]\(作成と監視\)** タイルを選択します。 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>データのコピー ツールを使用してパイプラインを作成する

1. **[Let's get started]\(始めましょう\)** ページで、**[データのコピー]** タイルを選択してデータのコピー ツールを起動します。 

   ![データのコピー ツールのタイル](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. **[プロパティ]** ページの **[タスク名]** に「**CopyFromBlobToSqlPipeline**」と入力します。 次に、**[次へ]** を選択します。 指定したタスク名のパイプラインが Data Factory UI によって作成されます。 

    ![[プロパティ] ページ](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. **[ソース データ ストア]** ページで、**[Azure Blob Storage]** を選択し、**[次へ]** を選択します。 ソース データは Blob Storage にあります。 

    ![[ソース データ ストア] ページ](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. **[Specify the Azure Blob storage account]\(Azure Blob Storage アカウントの指定\)** ページで、次の手順を実行します。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[接続名]** に「**AzureStorageLinkedService**」と入力します。

    b. **[ストレージ アカウント名]** ボックスの一覧から目的のストレージ アカウント名を選択します。

    c. **[次へ]** を選択します。 

    ![ストレージ アカウントを指定する](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

    リンクされたサービスは、データ ストアまたは計算をデータ ファクトリにリンクします。 ここでは、ストレージ アカウントをデータ ストアにリンクする、ストレージのリンクされたサービスを作成します。 リンクされたサービスには、Data Factory が実行時に Blob Storage に接続するために使用する接続情報が含まれています。 データセットは、コンテナー、フォルダー、ソース データが含まれているファイル (オプション) を指定します。 

5. **[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)** ページで、次の手順を行います。
    
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **adfv2tutorial/input** フォルダーに移動します。

    b. **inputEmp.txt** ファイルを選択します。

    c. **[選択]** を選択します。 または、**inputEmp.txt** ファイルをダブルクリックしてもかまいません。

    d. **[次へ]** を選択します。 

    ![[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)](./media/tutorial-copy-data-tool/choose-input-file-folder.png)

6. **[File format settings]\(ファイル形式設定\)** ページで、列区切り記号と行区切り記号がツールによって自動的に検出されたことを確認します。 **[次へ]** を選択します。 このページでは、データのプレビューと入力データのスキーマの表示を行うこともできます。 

    ![ファイル形式設定](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. **[Destination data store]\(コピー先データ ストア\)** ページで **[Azure SQL Database]** を選択し、**[次へ]** を選択します。

    ![コピー先データ ストア](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. **[Specify the Azure SQL database]\(Azure SQL データベースの指定\)** ページで、次の手順を実行します。 

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[接続名]** に「**AzureSqlDatabaseLinkedService**」と入力します。

    b. **[サーバー名]** で、使用する SQL Server インスタンスを選択します。

    c. **[データベース名]** で、使用する SQL データベースを選択します。

    d. **[ユーザー名]** に、ユーザーの名前を入力します。

    e. **[パスワード]** に、ユーザーのパスワードを入力します。

    f. **[次へ]** を選択します。 

    ![SQL データベースを指定する](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

    データセットをリンクされたサービスに関連付ける必要があります。 リンクされたサービスには、Data Factory が実行時に SQL データベースに接続するために使用する接続文字列が含まれています。 データセットは、コンテナー、フォルダー、データのコピー先のファイル (オプション) を指定します。

9. **[テーブル マッピング]** ページで **[dbo].[emp]** テーブルを選択し、**[次へ]** を選択します。 

    ![テーブル マッピング](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. **[スキーマ マッピング]** ページで、入力ファイルの最初の列と 2 番目の列が **emp** テーブルの **FirstName** 列と **LastName** 列にマップされていることがわかります。

    ![[スキーマ マッピング] ページ](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. **[設定]** ページで **[次へ]** を選択します。 

    ![[設定] ページ](./media/tutorial-copy-data-tool/settings-page.png)
12. **[サマリー]** ページで設定を確認し、**[次へ]** を選択します。

    ![概要ページ](./media/tutorial-copy-data-tool/summary-page.png)
13. **[Deployment]\(デプロイ\)** ページで **[監視]** を選択してパイプライン (タスク) を監視します。

    ![[Deployment]\(デプロイ\) ページ](./media/tutorial-copy-data-tool/deployment-page.png)
14. 左側の **[監視]** タブが自動的に選択されたことがわかります。 **[アクション]** 列には、アクティビティの実行の詳細を表示するリンクとパイプラインを再実行するリンクが表示されます。 **[最新の情報に更新]** を選択して、一覧を更新します。 

    ![パイプラインの実行を監視する](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、**[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択します。 パイプライン内のアクティビティ (コピー アクティビティ) は 1 つだけなので、エントリは 1 つのみです。 コピー操作の詳細を確認するために、**[アクション]** 列にある **[詳細]** リンク (眼鏡アイコン) を選択します。 **[Pipeline Runs]\(パイプラインの実行\)** ビューに戻るには、上部の **[パイプライン]** リンクを選択します。 表示を更新するには、**[最新の情報に更新]** を選択します。 

    ![アクティビティの実行を監視する](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. 左側の **[編集]** タブを選択して、編集モードに切り替えます。 ツールによって作成されたリンクされたサービス、データセット、パイプラインをエディターで更新できます。 **[コード]** を選択すると、現在エディターで開かれているエンティティの JSON コードが表示されます。 Data Factory UI におけるこれらのエンティティの編集について詳しくは、[このチュートリアルの Azure Portal バージョン](tutorial-copy-data-portal.md)を参照してください。

    ![[編集] タブ](./media/tutorial-copy-data-tool/edit-tab.png)
17. SQL データベースの **emp** テーブルにデータが挿入されたことを確認します。

    ![SQL 出力の確認](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>次の手順
このサンプルのパイプラインでは、Blob Storage から SQL データベースにデータがコピーされます。 以下の方法について学習しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * データのコピー ツールを使用してパイプラインを作成します。
> * パイプラインとアクティビティの実行を監視します。

オンプレミスからクラウドにデータをコピーする方法について学習するには、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
>[オンプレミスからクラウドにデータをコピーする](tutorial-hybrid-copy-data-tool.md)
