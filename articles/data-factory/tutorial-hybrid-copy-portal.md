---
title: "Azure Data Factory を使用して SQL Server から Blob Storage にデータをコピーする | Microsoft Docs"
description: "Azure Data Factory 内のセルフホステッド統合ランタイムを使って、オンプレミスのデータ ストアから Azure クラウドにデータをコピーする方法について説明します。"
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
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: 64cd758e2f40ff2b18abbff1194a7e57389d8a54
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2018
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>チュートリアル: オンプレミスの SQL Server データベースから Azure Blob Storage にデータをコピーする
このチュートリアルでは、Azure Data Factory ユーザー インターフェイス (UI) を使用して、オンプレミスの SQL Server データベースから Azure Blob Storage にデータをコピーするデータ ファクトリ パイプラインを作成します。 セルフホステッド統合ランタイムを作成して使用すると、データはオンプレミス データ ストアとクラウド データ ストア間を移動します。 

> [!NOTE]
> この記事は、現在プレビュー段階にある Azure Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。
> 
> この記事では、Data Factory サービスの概要については詳しく取り上げません。 詳細については、[Azure Data Factory の概要](introduction.md)に関するページをご覧ください。 

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * セルフホステッド統合ランタイムを作成します。
> * SQL Server と Azure Storage のリンクされたサービスを作成します。 
> * SQL Server と Azure BLOB のデータセットを作成します。
> * コピー アクティビティでデータを移動するパイプラインを作成します。
> * パイプラインの実行を開始します。
> * パイプラインの実行を監視します。

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

5. **emp** テーブルを作成していくつかのサンプル データを挿入するために、次のクエリ スクリプトをデータベースに対して実行します。

   ```
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

6. ツリー ビューで、作成したデータベースを右クリックし、**[新しいクエリ]** をクリックします。

### <a name="azure-storage-account"></a>Azure ストレージ アカウント
このチュートリアルでは、コピー先/シンク データ ストアに汎用の Azure Storage アカウント (具体的には Azure Blob Storage) を使用します。 汎用の Azure Storage アカウントがない場合は、「[ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)」をご覧ください。 このチュートリアルで作成するデータ ファクトリ内のパイプラインは、オンプレミスの SQL Server データベース (ソース) からこの Azure Blob Storage (シンク) にデータをコピーします。 

#### <a name="get-storage-account-name-and-account-key"></a>ストレージ アカウント名とアカウント キーの取得
このチュートリアルでは、Azure ストレージ アカウントの名前とキーを使用します。 以下の手順に従って、ご利用のストレージ アカウントの名前とキーを取得してください。 

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
この手順では、データ ファクトリを作成するほか、Azure Data Factory UI を起動してそのデータ ファクトリにパイプラインを作成します。 

1. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
2. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**ADFTutorialDataFactory**」と入力します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 名前フィールドで次のエラーが発生した場合は、データ ファクトリの名前を変更してください (yournameADFTutorialDataFactory など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
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

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
10. **[Author & Monitor]\(作成と監視\)** タイルをクリックして、別のタブで Azure Data Factory (UI) を起動します。 


## <a name="create-a-pipeline"></a>パイプラインを作成する。

1. **開始**ページで **[Create Pipeline]\(パイプラインの作成\)** をクリックします。 パイプラインが自動的に作成されます。 ツリー ビューのパイプラインとそのエディターが開かれるのがわかります。 

   ![開始ページ](./media/tutorial-hybrid-copy-portal/get-started-page.png)
2. 下部にある**プロパティ** ウィンドウの **[General]\(一般\)** タブで、**[名前]** に「**SQLServerToBlobPipeline**」と入力します。

   ![パイプライン名](./media/tutorial-hybrid-copy-portal/pipeline-name.png)
2. **[アクティビティ]** ツール ボックスで **[データ フロー]** を展開し、**[コピー]** アクティビティをパイプライン デザイナー画面にドラッグ アンド ドロップします。 アクティビティの名前を「**CopySqlServerToAzureBlobActivity**」に設定します。

   ![アクティビティ名](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)
3. プロパティ ウィンドウで **[ソース]** タブに切り替え、**[+ 新規]** をクリックします。

   ![新しいソース データセット - ボタン](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)
4. **[新しいデータセット]** ウィンドウで、**SQL Server** を検索して **[SQL Server]** を選択し、**[完了]** をクリックします。 **[SqlServerTable1]** という新しいタブが表示されます。 左側のツリービューにも **SqlServerTable1** データセットが表示されます。 

   ![SQL Server の選択](./media/tutorial-hybrid-copy-portal/select-sql-server.png)
5. プロパティ ウィンドウの **[General]\(一般\)** タブで、**[名前]** に「**SqlServerDataset**」と入力します。
    
   ![ソース データセット - 名前](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)
6. **[接続]** タブに切り替えて、**[+ 新規]** をクリックします。 この手順でソース データ ストア (SQL Server データベース) への接続を作成します。 

   ![シンク データセット - 新しい接続のボタン](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)
7. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、**[新規統合ランタイム]** をクリックします。 このセクションでは、セルフホステッド統合ランタイムを作成し、SQL Server データベースがあるオンプレミスのマシンに関連付けます。 セルフホステッド統合ランタイムは、マシンの SQL Server データベースから Azure Blob Storage にデータをコピーするコンポーネントです。 

   ![新規統合ランタイム](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)
8. **[Integration Runtime Setup]\(統合ランタイムのセットアップ\)** ウィンドウで、**[プライベート ネットワーク]** を選択して **[次へ]** クリックします。 

   ![[プライベート ネットワーク] の選択](./media/tutorial-hybrid-copy-portal/select-private-network.png)
9. 統合ランタイムの名前を入力して、**[次へ]** をクリックします。  
    
   ![統合ランタイム - 名前](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)
10. **[Option 1: Express setup]\(オプション 1: 高速セットアップ\)** セクションの **[Click here to launch the express setup for this computer]\(このコンピューターで高速セットアップを起動するにはここをクリック\)** をクリックします。 

   ![高速セットアップのリンクのクリック](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)
11. **[Integration Runtime (セルフホステッド) 高速セットアップ]** ウィンドウで、**[閉じる]** をクリックします。 

   ![統合ランタイムのセットアップ - 成功](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)
12. Web ブラウザーの **[Integration Runtime Setup]\(統合ランタイムのセットアップ\)** ウィンドウで、**[完了]** をクリックします。 

   ![統合ランタイムのセットアップ - 完了](./media/tutorial-hybrid-copy-portal/click-finish-integration-runtime-setup.png)
13. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。

    1. **[名前]** に「**SqlServerLinkedService**」と入力します。
    2. 先ほど作成したセルフホステッド統合ランタイムが **[Connect via integration runtime]\(統合ランタイム経由で接続\)** に表示されることを確認します。
    3. **[サーバー名]** に SQL サーバーの名前を指定します。 
    4. **[データベース名]** フィールドに、**emp** テーブルが含まれたデータベースの名前を指定します。 
    5. Data Factory による SQL Server データベースへの接続に使用される適切な**認証の種類**を選択します。 
    6. **[ユーザー]** 名と **[パスワード]** を入力します。 ユーザー アカウントまたはサーバー名にバックスラッシュ (\\) を使用する必要がある場合は、エスケープ文字 (\\) に続けて入力してください。 たとえば、「*mydomain\\\\myuser*」のように入力します。 
    7. **[接続テスト]** をクリックします。 この手順は、作成したセルフホステッド統合ランタイムを使用して Data Factory サービスが SQL Server データベースに接続できることを確認するために行います。 
    8. リンクされたサービスを保存するために、**[保存]** をクリックします。

       ![SQL Server のリンクされたサービス - 設定](./media/tutorial-hybrid-copy-portal/sql-server-linked-service-settings.png)
14. ソース データセットが開かれているウィンドウに戻ります。 **プロパティ** ウィンドウの **[接続]** で、以下の手順を実行します。 

    1. **[リンクされたサービス]** が **SqlServerLinkedService** であることを確認します。 
    2. **[テーブル]** で **[dbo].[emp]** を選択します。

        ![ソース データセット - 接続情報](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)
15. SQLServerToBlobPipeline のタブに切り替えるか、またはツリービューの **SQLServerToBlobPipeline** をクリックします。 

    ![パイプラインのタブ](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)
16. **プロパティ** ウィンドウにある **[シンク]** タブに切り替え、**[+ 新規]** をクリックします。 

    ![シンク データセット - [新規] ボタン](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)
17. **[新しいデータセット]** ウィンドウで **[Azure BLOB ストレージ]** を選択し、**[完了]** をクリックします。 データセット用の新しいタブが開かれます。 ツリー ビューにもデータセットが表示されます。 

    ![[Azure Blob Storage] の選択](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)
18. **[名前]** に **AzureBlobDataset** と入力します。

    ![シンク データセット - 名前](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)
19. **プロパティ** ウィンドウにある **[接続]** タブに切り替えて、**[リンクされたサービス]** で **[+ 新規]** をクリックします。 

    ![新しいリンクされたサービス - ボタン](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)
20. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。

    1. **[名前]** に「**AzureStorageLinkedService**」と入力します。
    2. **[ストレージ アカウント名]** で、使用する Azure ストレージ アカウントを選択します。 
    3. **[テスト接続]** をクリックして、Azure ストレージ アカウントへの接続をテストします。
    4. **[Save]** をクリックします。

        ![Azure Storage のリンクされたサービス - 設定](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 
21.  シンク データセットが開かれているウィンドウに戻ります。 **[接続]** タブで、次の手順を実行します。 

        1. **[リンクされたサービス]** で **AzureStorageLinkedService** が選択されていることを確認します。
        2. **[ファイル パス]** の**フォルダー**部分に「**adftutorial/fromonprem**」と入力します。 adftutorial コンテナーに出力フォルダーが存在しない場合、Data Factory サービスによって自動的に出力フォルダーが作成されます。
        3. **[ファイル パス]** の**ファイル名**部分に「`@CONCAT(pipeline().RunId, '.txt')`」と入力します。

            ![シンク データセット - 接続](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)
22. **パイプライン**が開かれているタブに切り替えるか、**ツリー ビュー**にあるパイプラインをクリックします。 **[Sink Dataset]\(シンク データセット\)** で **AzureBlobDataset** が選択されていることを確認します。 

    ![シンク データセットの選択 ](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)
23. パイプライン設定を検証するには、パイプラインのツール バーにある [検証] をクリックします。 右隅にある **[X]** をクリックして、**パイプラインの検証レポート**を閉じます。 

    ![パイプラインの検証](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
1. **[発行]** をクリックして、作成されたエンティティを Azure Data Factory サービスに発行します。

    ![[発行] ボタン](./media/tutorial-hybrid-copy-portal/publish-button.png)
24. **[発行は成功しました]** というポップアップが表示されるまで待機します。 発行の状態は、左側にある **[通知の表示]** リンクをクリックしてチェックすることもできます。 **[X]** をクリックして通知ウィンドウを閉じます。 

    ![発行は成功しました](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)
    

## <a name="trigger-a-pipeline-run"></a>パイプラインの実行をトリガーする
パイプラインのツール バーの **[トリガー]** をクリックし、**[Trigger Now]\(今すぐトリガー\)** をクリックします。

![[Trigger Now]\(今すぐトリガー\)](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

1. **[監視]** タブに切り替えます。前の手順で手動でトリガーしたパイプラインを確認します。 

    ![パイプライン実行](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. パイプラインの実行に関連付けられているアクティビティの実行を表示するために、**[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクをクリックします。 パイプラインにはアクティビティが 1 つしかないため、表示されるアクティビティの実行は 1 つのみです。 コピー操作の詳細を確認するために、**[アクション]** 列にある **[詳細]** リンク (眼鏡アイコン) をクリックします。 上部の **[パイプライン]** をクリックすると、パイプラインの実行ビューに戻ることができます。

    ![アクティビティの実行](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>出力を検証する
このパイプラインは、`adftutorial` BLOB コンテナーに対して *fromonprem* という名前の出力フォルダーを自動的に作成します。 出力フォルダーに *dbo.emp.txt* ファイルがあることを確認してください。 

1. Azure Portal の **adftutorial** コンテナー ウィンドウで **[最新の情報に更新]** を選択して出力フォルダーを表示します。

    ![作成された出力フォルダー](media/tutorial-hybrid-copy-portal/fromonprem-folder.png)
2. フォルダーの一覧で、`fromonprem` を選択します。 
3. `dbo.emp.txt` という名前のファイルが表示されていることを確認します。

    ![出力ファイル](media/tutorial-hybrid-copy-portal/fromonprem-file.png)


## <a name="next-steps"></a>次の手順
このサンプルのパイプラインは、Azure Blob Storage 内のある場所から別の場所にデータをコピーするものです。 以下の方法について学習しました。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * セルフホステッド統合ランタイムを作成します。
> * SQL Server と Azure Storage のリンクされたサービスを作成します。 
> * SQL Server と Azure BLOB のデータセットを作成します。
> * コピー アクティビティでデータを移動するパイプラインを作成します。
> * パイプラインの実行を開始します。
> * パイプラインの実行を監視します。

Data Factory でサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。

次のチュートリアルに進んで、ソースからコピー先にデータを一括コピーする方法について学習しましょう。

> [!div class="nextstepaction"]
>[データを一括コピーする](tutorial-bulk-copy-portal.md)
