---
title: "Azure のデータのコピー ツールを使用してデータをコピーする | Microsoft Docs"
description: "Azure データ ファクトリを作成し、データのコピー ツールを使用して Azure Blob Storage 内のフォルダーから別のフォルダーにデータをコピーします。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: aa9cdba4f4e891d5321eb8af6349d8b141faee03
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2018
---
# <a name="use-the-copy-data-tool-to-copy-data"></a>データのコピー ツールを使用してデータをコピーする 
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [バージョン 2 - プレビュー](quickstart-create-data-factory-copy-data-tool.md)

このクイック スタートでは、Azure Portal を使用してデータ ファクトリを作成します。 次に、データのコピー ツールを使用して、Azure Blob Storage 内のフォルダーから別のフォルダーにデータをコピーするパイプラインを作成します。 

> [!NOTE]
> Azure Data Factory を初めて使用する場合は、このクイック スタートを実行する前に、「[Azure Data Factory の概要](data-factory-introduction.md)」を参照してください。 
>
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されているサービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 の使用](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関する記事を参照してください。


[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. 左側のメニューの **[新規]** を選択し、**[データ + 分析]** を選択して、**[Data Factory]** を選択します。 
   
   ![[新規] ウィンドウでの [Data Factory] の選択](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
2. **[新しいデータ ファクトリ]** ページで、**[名前]**に「**ADFTutorialDataFactory**」と入力します。 
      
   ![[新しいデータ ファクトリ] ページ](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は *グローバルに一意*にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (**&lt;yourname&gt;ADFTutorialDataFactory** など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
   ![名前が使用できないときのエラー](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. **[サブスクリプション]** で、データ ファクトリを作成する Azure サブスクリプションを選択します。 
4. **[リソース グループ]** で、次の手順のいずれかを行います。
     
   - **[既存のものを使用]** を選択し、一覧から既存のリソース グループを選択します。 
   - **[新規作成]**を選択し、リソース グループの名前を入力します。   
         
   リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
4. **[バージョン]** では、**[V2 (プレビュー)]** を選択します。
5. **[場所]** で、データ ファクトリの場所を選択します。 

   このリストに表示されるのは、サポートされている場所のみです。 Data Factory で使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (Azure HDInsight など) は他の場所/リージョンに配置できます。

6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **[作成]**を選択します。
8. ダッシュボードに、**[Deploying Data Factory]\(Data Factory をデプロイしています\)** というステータスを示した次のタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
9. 作成が完了すると、**[Data Factory]** ページが表示されます。 **[作成と監視]** タイルを選択して、別のタブで Azure Data Factory ユーザー インターフェイス (UI) アプリケーションを起動します。
   
   ![[作成と監視] タイルが表示された、データ ファクトリのホーム ページ](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>データのコピー ツールの起動

1. **[Let's get started]\(始めましょう\)** ページで、**[データのコピー]** タイルを選択してデータのコピー ツールを起動します。 

   ![[データのコピー] タイル](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)
2. データのコピー ツールの **[プロパティ]** ページで、**[次へ]** を選択します。 このページで、パイプラインの名前とその説明を指定できます。 

   ![[プロパティ] ページ](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. **[ソース データ ストア]** ページで、**[Azure Blob Storage]** を選択し、**[次へ]** を選択します。

   ![[ソース データ ストア] ページ](./media/quickstart-create-data-factory-copy-data-tool/source-data-store-page.png)
4. **[Specify the Azure Blob storage account]\(Azure BLOB ストレージ アカウントの指定\)** ページで、**[ストレージ アカウント名]** ボックスの一覧からストレージ アカウントを選択し、**[次へ]** をクリックします。 

   ![[Specify the Azure Blob storage account]\(Azure BLOB ストレージ アカウントの指定\) ページ](./media/quickstart-create-data-factory-copy-data-tool/specify-blob-storage-account.png)
5. **[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)** ページで、次の手順を実行します。

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **adftutorial/input** フォルダーを参照します。

   b. **emp.txt** ファイルを選択します。

   c. **[選択]** を選択します。 **emp.txt** をダブルクリックすると、この手順をスキップできます。

   d. **[次へ]**を選択します。 

   ![[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\) ページ](./media/quickstart-create-data-factory-copy-data-tool/choose-input-file-folder.png)
6. **[File format settings]\(ファイル形式設定\)** ページで、列区切り記号と行区切り記号がツールによって自動的に検出されたことを確認して、**[次へ]** を選択します。 このページでは、データのプレビューと入力データのスキーマの表示を行うこともできます。 

   ![[File format settings]\(ファイル形式設定\) ページ](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-page.png)
7. **[Destination data store]\(コピー先データ ストア\)** ページで **[Azure Blob Storage]** を選択し、**[次へ]** を選択します。 

   ![[Destination data store]\(コピー先データ ストア\) ページ](./media/quickstart-create-data-factory-copy-data-tool/destination-data-store-page.png)    
8. **[Specify the Azure Blob storage account]\(Azure BLOB ストレージ アカウントの指定\)** ページで、使用する Azure BLOB ストレージ アカウントを選択し、**[次へ]** を選択します。 

   ![[Specify the Azure Blob storage account]\(Azure BLOB ストレージ アカウントの指定\) ページ](./media/quickstart-create-data-factory-copy-data-tool/specify-sink-blob-storage-account.png)
9. **[Choose the output file or folder]\(出力ファイルまたはフォルダーの選択\)** ページで、次の手順を実行します。 

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 フォルダー パスに「**adftutorial/output**」と入力します。

   b. ファイル名に「**emp.txt**」と入力します。

   c. **[次へ]**を選択します。 

   ![[Choose the output file or folder]\(出力ファイルまたはフォルダーの選択\) ページ](./media/quickstart-create-data-factory-copy-data-tool/choose-output-file-folder.png) 
10. **[File format settings]\(ファイル形式設定\)** ページで **[次へ]** を選択します。 

    ![[File format settings]\(ファイル形式設定\) ページ](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-output-page.png)
11. **[設定]** ページで **[次へ]** を選択します。 

    ![[設定] ページ](./media/quickstart-create-data-factory-copy-data-tool/advanced-settings-page.png)
12. **[概要]** ページで、すべての設定を確認し、**[次へ]** を選択します。 

    ![[概要] ページ](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)
13. **[Deployment complete]\(デプロイ完了\)** ページで **[監視]** を選択して、作成したパイプラインを監視します。 

    ![[Deployment complete]\(デプロイ完了\) ページ](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)
14. アプリケーションの **[監視]** タブに切り替えます。このタブでは、パイプラインの状態が表示されます。**[最新の情報に更新]** を選択して、一覧を更新します。 
    
    ![[最新の情報に更新] ボタンが表示されている、パイプライン実行を監視するためのタブ](./media/quickstart-create-data-factory-copy-data-tool/monitor-pipeline-runs-page.png)
15. **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択します。 このパイプラインには、**コピー**という種類のアクティビティが 1 つのみ含まれます。 

    ![アクティビティの実行の一覧](./media/quickstart-create-data-factory-copy-data-tool/activity-runs.png)
16. コピー操作の詳細を確認するには、**[アクション]** 列にある **[詳細]** (眼鏡アイコン) リンクを選択します。 プロパティの詳細については、[コピー アクティビティの概要](copy-activity-overview.md)に関するページを参照してください。 

    ![コピー操作の詳細](./media/quickstart-create-data-factory-copy-data-tool/copy-operation-details.png)
17. **adftutorial** コンテナーの **output** フォルダーに **emp.txt** ファイルが作成されていることを確認します。 output フォルダーが存在しない場合は、Data Factory サービスによって自動的に作成されます。 
18. **[編集]** タブに切り替えると、リンクされたサービス、データセット、パイプラインを編集できます。 Data Factory UI での編集の詳細については、[Azure Portal を使用したデータ ファクトリの作成](quickstart-create-data-factory-portal.md)に関する記事を参照してください。

    ![[編集] タブ](./media/quickstart-create-data-factory-copy-data-tool/edit-tab.png)

## <a name="next-steps"></a>次の手順
このサンプルのパイプラインは、Azure Blob Storage 内のある場所から別の場所にデータをコピーします。 より多くのシナリオで Data Factory を使用する方法については、[チュートリアル](tutorial-copy-data-portal.md)を参照してください。 