---
title: Azure データ コピー ツールを使用してデータをコピーする
description: Azure データ ファクトリを作成し、データのコピー ツールを使用して Azure Blob Storage 内のある場所から別の場所にデータをコピーします。
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: quickstart
ms.date: 07/05/2021
ms.openlocfilehash: 2bde0d03c901808e277044db035dc738bb555bb0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750356"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>クイック スタート:データのコピー ツールを使用してデータをコピーする

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください。"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [現在のバージョン](quickstart-create-data-factory-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

このクイック スタートでは、Azure Portal を使用してデータ ファクトリを作成します。 次に、データのコピー ツールを使用して、Azure Blob Storage 内のフォルダーから別のフォルダーにデータをコピーするパイプラインを作成します。 

> [!NOTE]
> Azure Data Factory を初めて使用する場合は、このクイック スタートを実行する前に、「[Azure Data Factory の概要](introduction.md)」を参照してください。 

[!INCLUDE [data-factory-quickstart-prerequisites](includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Data Factory の作成

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
1. [Azure ポータル](https://portal.azure.com)にアクセスします。 
1. Azure portal のメニューで、 **[リソースの作成]**  >  **[統合]**  >  **[Data Factory]** の順に選択します。

    :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="新しいデータ ファクトリの作成":::

1. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。 
 
   Azure Data Factory の名前は、"*グローバルに一意*" にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して ( **&lt;yourname&gt;ADFTutorialDataFactory** など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="名前が使用できないときのエラー":::
1. **[サブスクリプション]** で、データ ファクトリを作成する Azure サブスクリプションを選択します。 
1. **[リソース グループ]** で、次の手順のいずれかを行います。
     
   - **[既存のものを使用]** を選択し、一覧から既存のリソース グループを選択します。 
   - **[新規作成]** を選択し、リソース グループの名前を入力します。   
         
   リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。  
1. **[バージョン]** で、 **[V2]** を選択します。
1. **[場所]** で、データ ファクトリの場所を選択します。

   この一覧に表示されるのは、Data Factory でサポートされ、かつ Azure Data Factory のメタ データが格納される場所のみです。 Data Factory で使用する関連データ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (Azure HDInsight など) は他のリージョンで実行できます。

1. **［作成］** を選択します

1. 作成が完了すると、 **[Data Factory]** ページが表示されます。 **[Open Azure Data Factory Studio]\(Azure Data Factory Studio を開く\)** タイルで **[開く]** を選択して、別のタブで Azure Data Factory ユーザー インターフェイス (UI) アプリケーションを起動します。
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="[Open Azure Data Factory Studio] タイルを含む、Azure Data Factory のホーム ページ。":::
    
## <a name="start-the-copy-data-tool"></a>データのコピー ツールの起動

1. Azure Data Factory のホーム ページで、 **[取り込み]** タイルを選択し、データのコピー ツールを起動します。

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Azure Data Factory ホーム ページのスクリーンショット。":::

1. データのコピー ツールの **[プロパティ]** ページで、 **[タスクの種類]** に **[組み込みコピー タスク]** を選択して、 **[次へ]** を選択します。

   :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png" alt-text="&quot;[プロパティ]&quot; ページ":::

1. **[ソース データ ストア]** ページで、次の手順を実行します。

    1. **[+ 新しい接続の作成]** をクリックして、接続を追加します。

    1. ソース接続用に作成するリンクされたサービスの種類を選択します。 このチュートリアルでは、**Azure Blob Storage** を使用します。 ギャラリーからそれを選択し、 **[続行]** を選択します。
    
       :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png" alt-text="BLOB を選択する":::

    1. **[新しい接続 (Azure Blob Storage)]** ページで、接続の名前を指定します。 **[Azure サブスクリプション]** の一覧から Azure サブスクリプションを選択し、 **[ストレージ アカウント名]** の一覧からストレージ アカウントを選択し、接続をテストしてから **[作成]** を選択します。 

       :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png" alt-text="Azure BLOB ストレージ アカウントの構成":::

    1. **[接続]** ブロックで新しく作成した接続を選択します。
    1. **[ファイルまたはフォルダー]** セクションで **[参照]** を選択して、**adftutorial/input** フォルダーに移動します。**emp.txt** ファイルを選択し、 **[OK]** をクリックします。
    1. ファイルをそのままコピーするため、 **[Binary copy]\(バイナリ コピー\)** チェック ボックスをオンにし、 **[次へ]** を選択します。

       :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/source-data-store.png" alt-text="[ソース データ ストア] ページを示すスクリーンショット。":::

1. **[コピー先データ ストア]** ページで、次の手順を実行します。
    1. **[接続]** ブロックで作成した **[AzureBlobStorage]** 接続を選択します。

    1. **[フォルダー パス]** セクションで、フォルダー パスに「**adftutorial/output**」と入力します。

       :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/destination-data-store.png" alt-text="[コピー先データ ストア] ページを示すスクリーンショット。":::

    1. 他の設定は既定値のままにして、 **[次へ]** を選択します。

1. **[設定]** ページで、パイプラインの名前と説明を指定し、 **[次へ]** を選択して他の既定の構成を使用します。 

    :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/settings.png" alt-text="設定ページを示すスクリーンショット。":::

1. **[概要]** ページで、すべての設定を確認し、 **[次へ]** を選択します。 

1. **[Deployment complete]\(デプロイ完了\)** ページで **[監視]** を選択して、作成したパイプラインを監視します。 

    :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png" alt-text="&quot;[Deployment complete]\(デプロイ完了\)&quot; ページ":::

1. アプリケーションの **[監視]** タブに切り替えます。このタブでは、パイプラインの状態が表示されます。 **[最新の情報に更新]** を選択して、一覧を更新します。 **[パイプライン名]** の下にあるリンクをクリックして、アクティビティの実行の詳細を表示するか、パイプラインを再実行します。 
   
    :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png" alt-text="パイプラインを更新する":::

1. コピー操作の詳細については、[アクティビティの実行] ページで、 **[アクティビティ名]** 列の下にある **[詳細]** リンク (眼鏡アイコン) を選択します。 プロパティの詳細については、[コピー アクティビティの概要](copy-activity-overview.md)に関するページを参照してください。 

1. [パイプラインの実行] ビューに戻るには、階層リンク メニューの **[すべてのパイプラインの実行]** リンクを選択します。 表示を更新するには、 **[最新の情報に更新]** を選択します。 

1. **adftutorial** コンテナーの **output** フォルダーに **emp.txt** ファイルが作成されていることを確認します。 output フォルダーが存在しない場合は、Data Factory サービスによって自動的に作成されます。 

1. **[監視]** タブの上にある **[編集]** タブに切り替えると、リンクされたサービス、データセット、パイプラインを編集できます。 Data Factory UI での編集の詳細については、[Azure Portal を使用したデータ ファクトリの作成](quickstart-create-data-factory-portal.md)に関する記事を参照してください。

    :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/select-author.png" alt-text="[作成者] タブを選択する":::

## <a name="next-steps"></a>次のステップ
このサンプルのパイプラインは、Azure Blob Storage 内のある場所から別の場所にデータをコピーします。 より多くのシナリオで Data Factory を使用する方法については、[チュートリアル](tutorial-copy-data-portal.md)を参照してください。 
