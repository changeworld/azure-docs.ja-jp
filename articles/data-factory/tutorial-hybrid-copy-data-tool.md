---
title: "Azure のデータのコピー ツールを使用してオンプレミスのデータをコピーする | Microsoft Docs"
description: "Azure データ ファクトリを作成し、データのコピー ツールを使用してオンプレミスの SQL Server データベースから Azure Blob Storage にデータをコピーします。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/04/2018
ms.author: jingwang
ms.openlocfilehash: aba8b13d47b2b9236a0d5cc868e53780e894c406
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-copy-data-tool"></a>データのコピー ツールを使用してオンプレミスの SQL Server データベースから Azure Blob Storage にデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [バージョン 2 - プレビュー](tutorial-hybrid-copy-data-tool.md)

このチュートリアルでは、Azure Portal を使用してデータ ファクトリを作成します。 次に、データのコピー ツールを使用して、オンプレミスの SQL Server データベースから Azure BLOB ストレージにデータをコピーするパイプラインを作成します。

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
### <a name="azure-subscription"></a>Azure サブスクリプション
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

### <a name="azure-roles"></a>Azure ロール
Data Factory インスタンスを作成するには、Azure へのログインに使用するユーザー アカウントが、"*共同作成者*" または "*所有者*" ロールに属しているか、Azure サブスクリプションの "*管理者*" である必要があります。 

サブスクリプションで自分が持っているアクセス許可を表示するには、Azure Portal で右上隅にあるユーザー名をクリックし、**[アクセス許可]** を選択します。 複数のサブスクリプションへのアクセス権がある場合は、適切なサブスクリプションを選択します。 ロールにユーザーを追加するサンプル手順については、[ロールの追加](../billing/billing-add-change-azure-subscription-administrator.md)に関する記事を参照してください。

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014、2016、2017
このチュートリアルでは、オンプレミスの SQL Server データベースを "*ソース*" データ ストアとして使用します。 このチュートリアルで作成するデータ ファクトリ内のパイプラインは、このオンプレミスの SQL Server データベース (ソース) から Azure Blob Storage (シンク) にデータをコピーします。 SQL Server データベース内に **emp** という名前のテーブルを作成し、このテーブルにサンプル エントリをいくつか挿入します。 

1. SQL Server Management Studio を起動します。 ご使用のマシンにまだインストールされていない場合は、「[SQL Server Management Studio のダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)」にアクセスしてください。 

2. 自分の資格情報で SQL Server インスタンスに接続します。 

3. サンプル データベースを作成します。 ツリー ビューで **[データベース]** を右クリックし、**[新しいデータベース]** を選択します。 
 
4. **[新しいデータベース]** ウィンドウで、データベースの名前を入力し、**[OK]** を選択します。 

5. **emp** テーブルを作成していくつかのサンプル データを挿入するために、次のクエリ スクリプトをデータベースに対して実行します。ツリー ビューで、作成したデータベースを右クリックし、**[新しいクエリ]** を選択します。

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Azure ストレージ アカウント
このチュートリアルでは、コピー先/シンク データ ストアに汎用の Azure Storage アカウント (具体的には Azure Blob Storage) を使用します。 汎用の Azure ストレージ アカウントがない場合、作成する手順について「[ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)」を参照してください。 このチュートリアルで作成するデータ ファクトリ内のパイプラインは、オンプレミスの SQL Server データベース (ソース) からこの Azure Blob Storage (シンク) にデータをコピーします。 

#### <a name="get-storage-account-name-and-account-key"></a>ストレージ アカウント名とアカウント キーの取得
このチュートリアルでは、Azure ストレージ アカウントの名前とキーを使用します。 次の手順に従って、ご利用のストレージ アカウントの名前とキーを取得してください。 

1. Azure のユーザー名とパスワードを使用して、[Azure Portal](https://portal.azure.com) にサインインします。 

2. 左側のウィンドウの **[その他のサービス]** を選択し、「**ストレージ**」というキーワードでフィルタリングして、**[ストレージ アカウント]** を選択します。

    ![ストレージ アカウントを検索](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. ストレージ アカウントの一覧で、ご利用のストレージ アカウントを (必要に応じて) フィルターで抽出し、該当するストレージ アカウントを選択します。 

4. **[ストレージ アカウント]** ウィンドウで **[アクセス キー]** を選択します。

    ![ストレージ アカウントの名前とキーを取得](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. **[ストレージ アカウント名]** ボックスと **[key1]** ボックスの値をコピーし、メモ帳などのエディターに貼り付けます。これらの値は、後でこのチュートリアルの中で使用します。 

#### <a name="create-the-adftutorial-container"></a>adftutorial コンテナーの作成 
このセクションでは、**adftutorial** という名前の BLOB コンテナーを Azure Blob Storage に作成します。 

1. **[ストレージ アカウント]** ウィンドウで **[概要]** に切り替え、**[BLOB]** を選択します。 

    ![BLOB オプションを選択する](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. **[Blob service]** ウィンドウで **[コンテナー]** を選択します。 

    ![コンテナーの追加ボタン](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. **[新しいコンテナー]** ウィンドウの **[名前]** ボックスに「**adftutorial**」と入力し、**[OK]** を選択します。 

    ![コンテナー名を入力する](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. コンテナーの一覧で、**[adftutorial]** を選択します。  

    ![コンテナーを選択する](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. **adftutorial** の **[コンテナー]** ウィンドウを開いたままにしておきます。 チュートリアルの最後で、このページを使用して出力を確認します。 このコンテナーには output フォルダーが Data Factory によって自動的に作成されます。手動で作成する必要はありません。

    ![[コンテナー] ウィンドウ](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
2. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**ADFTutorialDataFactory**」と入力します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 名前フィールドで次のエラーが発生した場合は、データ ファクトリの名前を変更してください (yournameADFTutorialDataFactory など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
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

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
10. **[Author & Monitor]\(作成と監視\)** タイルをクリックして、別のタブで Azure Data Factory ユーザー インターフェイス (UI) を起動します。 

## <a name="use-copy-data-tool-to-create-a-pipeline"></a>データのコピー ツールを使用してパイプラインを作成する

1. 開始ページで **[データのコピー]** タイルをクリックして、データのコピー ツールを起動します。 

   ![データのコピー ツールのタイル](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)
2. データのコピー ツールの **[プロパティ]** ページで、**[タスク名]** に「**CopyFromOnPremSqlToAzureBlobPipeline**」と指定し、**[次へ]** をクリックします。 このフィールドで指定した名前のパイプラインが、データのコピー ツールによって作成されます。 
    
   ![[プロパティ] ページ](./media/tutorial-hybrid-copy-data-tool/properties-page.png)
3. **[ソース データ ストア]** ページで **[SQL Server]** を選択し、**[次へ]** をクリックします。 必要に応じて、下へスクロールして一覧の **[SQL Server]** を表示してください。 

   ![[ソース データ ストア] ページ](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)
4. **[接続名]** に「**SqlServerLinkedService**」と入力し、**[Create Integration Runtime]\(統合ランタイムの作成\)** リンクをクリックします。 セルフホステッド統合ランタイムを作成してマシンにダウンロードし、それを Data Factory サービスに登録する必要があります。 セルフホステッド統合ランタイムによって、オンプレミスの環境と Azure クラウドとの間でデータがコピーされます。

   ![[Create Integration Runtime]\(統合ランタイムの作成\) リンク](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)
5. **[Create Integration Runtime]\(統合ランタイムの作成\)** ダイアログで、**[名前]** フィールドに「**TutorialIntegration Runtime**」と入力し、**[作成]** をクリックします。 

   ![[Create Integration Runtime]\(統合ランタイムの作成\) ダイアログ](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)
6. **[Launch express setup on this computer]\(このコンピューターで高速セットアップを起動する\)** をクリックします。 この操作により、統合ランタイムがマシンにインストールされ、Data Factory サービスに登録されます。 別の方法として、手動セットアップのオプションを使用できます。インストール ファイルをダウンロードして実行し、キーを使用して統合ランタイムを登録します。 

    ![[Create Integration Runtime]\(統合ランタイムの作成\) リンク](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)
7. ダウンロードされたアプリケーションを実行します。 ウィンドウには高速セットアップの**状態**が表示されます。 

    ![高速セットアップの状態](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)
8. **[統合ランタイム]** フィールドで **TutorialIntegrationRuntime** が選択されていることを確認します。

    ![統合ランタイムの選択](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)
9. **[Specify the on-premises SQL Server database]\(オンプレミスの SQL Server データベースを指定\)** で、次の手順を実行します。 

    1. **[接続名]** に「**OnPremSqlLinkedService**」と入力します。
    2. **[サーバー名]** にオンプレミスの SQL サーバーの名前を入力します。
    3. **[データベース名]** にオンプレミスのデータベースの名前を入力します。 
    4. **[認証の種類]** で適切な認証を選択します。
    5. **[ユーザー名]** に、オンプレミスの SQL サーバーへのアクセス権を持つユーザーの名前を入力します。
    6. ユーザーの**パスワード**を入力します。 
10. **[Select tables from which to copy the data or use a custom query]\(データのコピー元またはカスタム クエリの使用元となるテーブルの選択\)** ページで、一覧の **[dbo].[emp]** テーブルを選択し、**[次へ]** をクリックします。 

    ![emp テーブルの選択](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)
11. **[Destination data store]\(コピー先データ ストア\)** ページで **[Azure Blob Storage]** を選択し、**[次へ]** をクリックします。

    ![[Azure Blob Storage] の選択](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)
12. **[Specify the Azure Blob storage account]\(Azure BLOB ストレージ アカウントの指定\)** ページで、次の手順を実行します。 

    1. **[接続名]** に「**AzureStorageLinkedService**」と入力します。
    2. **[ストレージ アカウント名]** ドロップダウン リストから、使用する Azure ストレージ アカウントを選択します。 
    3. **[次へ]** をクリックします。

        ![[Azure Blob Storage] の選択](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)
13. **[Choose the output file or folder]\(出力ファイルまたはフォルダーの選択\)** ページで、次の手順を行います。 
    
    1. **[フォルダー パス]** に「**adftutorial/fromonprem**」と入力します。 前提条件の 1 つとして **adftutorial** コンテナーを作成しました。 出力フォルダーが存在しない場合は、Data Factory サービスによって自動的に作成されます。 また、**[参照]** ボタンを使用して、BLOB ストレージとそのコンテナー/フォルダーに移動することもできます。 既定では出力ファイルの名前が **dbo.emp** に設定されていることがわかります。
        
        ![出力ファイルまたはフォルダーの選択](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)
14. **[File format settings]\(ファイル形式設定\)** ページで **[次へ]** をクリックします。 

    ![[File format settings]\(ファイル形式設定\) ページ](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)
15. **[設定]** ページで **[次へ]** をクリックします。 

    ![[設定] ページ](./media/tutorial-hybrid-copy-data-tool/settings-page.png)
16. **[概要]** ページですべての設定の値を確認し、**[次へ]** をクリックします。 

    ![概要ページ](./media/tutorial-hybrid-copy-data-tool/summary-page.png)
17. **[Deployment]\(デプロイ\)** ページで **[監視]** をクリックして、作成されたパイプラインまたはタスクを監視します。

    ![[Deployment]\(デプロイ\) ページ](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)
18. **[監視]** タブでは、作成したパイプラインの状態を表示できます。 **[アクション]** 列のリンクを使用すると、パイプラインの実行に関連付けられているアクティビティの実行を表示したり、パイプラインを再実行したりできます。 

    ![パイプライン実行](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)
19. **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクをクリックして、パイプラインの実行に関連付けられているアクティビティの実行を表示します。 コピー操作の詳細を確認するために、**[アクション]** 列にある **[詳細]** リンク (眼鏡アイコン) をクリックします。 上部の **[パイプライン]** をクリックすると、パイプラインの実行ビューに戻ることができます。

    ![アクティビティの実行](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)
20. **adftutorial** コンテナーの **fromonprem** フォルダーに出力ファイルがあることを確認します。   
 
    ![出力 BLOB](./media/tutorial-hybrid-copy-data-tool/output-blob.png)
21. 左側の **[編集]** タブをクリックして、編集モードに切り替えます。 ツールによって作成されたリンクされたサービス、データセット、パイプラインをエディターで更新できます。 **[コード]** をクリックすると、エディターで開かれているエンティティに関する JSON コードが表示されます。 Data Factory UI におけるこれらのエンティティの編集について詳しくは、[このチュートリアルの Azure Portal バージョン](tutorial-copy-data-portal.md)を参照してください。

    ![[編集] タブ](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>次の手順
このサンプルのパイプラインでは、オンプレミスの SQL Server データベースから Azure BLOB ストレージにデータがコピーされます。 以下の方法について学習しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * データのコピー ツールを使用してパイプラインを作成します。
> * パイプラインとアクティビティの実行を監視します。

Data Factory でサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。

次のチュートリアルに進んで、ソースからコピー先にデータを一括コピーする方法について学習しましょう。

> [!div class="nextstepaction"]
>[データを一括コピーする](tutorial-bulk-copy-portal.md)