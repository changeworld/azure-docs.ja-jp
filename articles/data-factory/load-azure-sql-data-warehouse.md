---
title: "Azure Data Factory を使用した Azure SQL Data Warehouse へのデータの読み込み | Microsoft Docs"
description: "Azure Data Factory を使用して Azure SQL Data Warehouse にデータをコピーします"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 36e24da50386d1abc441e2beb09f570a9612a346
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-using-azure-data-factory"></a>Azure Data Factory を使用した Azure SQL Data Warehouse へのデータの読み込み

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) は、クラウドベースのスケールアウト データベースであり、リレーショナルか非リレーショナルかを問わず、大規模なデータを処理できます。  超並列処理 (MPP) アーキテクチャを基盤とする SQL Data Warehouse は、企業のウェアハウスのワークロード向けに最適化されています。  ストレージとコンピューティングを別々にスケールできる柔軟性によって、クラウドの弾力性を提供します。

Azure SQL Data Warehouse は、**Azure Data Factory** の使用によって、さらに使いやすくなっています。  Azure Data Factory は、完全に管理されたクラウド ベースのデータ統合サービスであり、このサービスを使用して既存のシステムのデータを SQL Data Warehouse に入力することで、貴重な時間を節約しながら、SQL Data Warehouse の評価と分析ソリューションの構築を行うことができます。 Azure Data Factory を使用して Azure SQL Data Warehouse にデータを読み込む主な利点を次に示します。

* **簡単にセットアップ**: 直感的なウィザードが示す 5 つの手順に従うだけです。スクリプトは必要ありません。
* **豊富なデータ ストアのサポート**: オンプレミスとクラウド ベースのデータ ストアの豊富なセットに対するサポートが組み込まれています。詳しくは、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
* **セキュリティとコンプライアンスへの準拠**: データは HTTPS または ExpressRoute で転送され、グローバル サービスの存在により、データが地理的な境界を越えることはありません。
* **PolyBase の使用による比類のないパフォーマンス**: PolyBase の使用は、Azure SQL Data Warehouse にデータを移動するための最も効率的な方法です。 ステージング BLOB の機能を使用して、Azure Blob ストレージと Data Lake Store を含むすべての種類のデータ ストアからデータを高速で読み込むことができます。これは PolyBase が既定でサポートしている機能です。 詳細については、[コピー アクティビティのパフォーマンス](copy-activity-performance.md)に関する記事を参照してください。

この記事では、Data Factory のデータのコピー ツールを使用して **Azure SQL Database から Azure SQL Data Warehouse にデータを読み込む**方法を示します。 その他の種類のデータ ストアからデータをコピーする場合も、同様の手順で実行できます。

> [!NOTE]
>  Azure SQL Data Warehouse との間のデータのコピーに関する Data Factory の機能の一般的な情報については、「[Azure Data Factory を使用して Azure SQL Data Warehouse をコピー先またはコピー元としてデータをコピーする](connector-azure-sql-data-warehouse.md)」を参照してください。
>
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、「[Copy Activity in V1 (V1 でのコピー アクティビティ)](v1/data-factory-data-movement-activities.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
* **Azure SQL Data Warehouse**。 SQL データベースからコピーされたデータは、このデータ ウェアハウスに格納されます。 Azure SQL データ ウェアハウスがない場合は、「[SQL Data Warehouse の作成](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md)」の記事に書かれている作成手順を参照してください。
* **Azure SQL データベース**。 このチュートリアルでは、Adventure Works LT サンプル データを使用して Azure SQL Database からデータをコピーします。データは、[Azure SQL データベースの作成](../sql-database/sql-database-get-started-portal.md)に関する記事に従って作成できます。 
* **Azure Storage アカウント**。 Azure Storage は、一括コピー操作の**ステージング** BLOB として使用されます。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「[ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)」を参照してください。

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. **[新しいデータ ファクトリ]** ページで、次のスクリーンショットに示されているように値を指定します。
      
     ![[新しいデータ ファクトリ] ページ](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
   * **名前。** データ ファクトリのグローバルな一意の名前を入力します。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (yournameADFTutorialDataFactory など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
            `Data factory name "LoadSQLDWDemo" is not available`

    * **サブスクリプション。** データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
    * **リソース グループ。** ドロップダウン リストから既存のリソース グループを選択するか、**[新規作成]** オプションを選択し、リソース グループの名前を入力します。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
    * **バージョン。** **[V2 (プレビュー)]** を選択します。
    * **場所。** データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) は他の場所/リージョンに配置できます。 

3. **Create** をクリックしてください。
4. 作成が完了したら、データ ファクトリに移動すると、図に示されているような **[Data Factory]** ページが表示されます。 **[作成と監視]** タイルをクリックして、別のタブでデータ統合アプリケーションを起動します。
   
   ![データ ファクトリのホーム ページ](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

## <a name="load-data-into-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse へのデータの読み込み

1. 開始ページで **[データのコピー]** タイルをクリックして、データのコピー ツールを起動します。 

   ![データのコピー ツールのタイル](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. データのコピー ツールの **[プロパティ]** ページで、**[タスク名]** に「**CopyFromSQLToSQLDW**」と指定し、**[次へ]** をクリックします。 

    ![データのコピー ツール- プロパティ ページ](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. **[ソース データ ストア]** ページで **[Azure SQL Database]** を選択し、**[次へ]** をクリックします。

    ![[ソース データ ストア] ページ](./media/load-azure-sql-data-warehouse/specify-source.png)
4. **[Specify the Azure SQL database]\(Azure SQL データベースの指定\)** ページで、次の手順を実行します。 
    1. **[サーバー名]** で Azure SQL サーバーを選択します。
    2. **[データベース名]** で Azure SQL データベースを選択します。
    3. **[ユーザー名]** でユーザーの名前を指定します。
    4. **[パスワード]** でユーザーのパスワードを指定します。
    5. **[次へ]** をクリックします。 

        ![Azure SQL DB の指定](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. **[Select tables from which to copy the data or use a custom query]\(データのコピー元またはカスタム クエリの使用元となるテーブルの選択\)** ページで、入力ボックスに「**SalesLT**」を指定してテーブルをフィルター処理します。次に、**[(すべて選択)]** チェックボックスをオンにしてすべてのテーブルを選択し、**[次へ]** をクリックします。 

    ![入力ファイルまたはフォルダーの選択](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. **[配布先データストア]** ページで **[Azure SQL Data Warehouse]** を選択し、**[次へ]** をクリックします。 

    ![[配布先データ ストア] ページ](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. **[Specify the Azure SQL Data Warehouse]\(Azure SQL Data Warehouse の指定\)** ページで、次の手順を実行します。 

    1. **[サーバー名]** で Azure SQL サーバーを選択します。
    2. **[データベース名]** で Azure SQL Data Warehouse を選択します。
    3. **[ユーザー名]** でユーザーの名前を指定します。
    4. **[パスワード]** でユーザーのパスワードを指定します。
    5. **[次へ]** をクリックします。 

        ![Azure SQL Data Warehouse の指定](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. **[テーブル マッピング]** ページで、表示内容を確認した後、**[次へ]** をクリックします。 テーブル名に基づいてコピー元テーブルをコピー先テーブルにマップする、インテリジェント テーブル マッピングが表示されます。 変換先にテーブルが存在しない場合、既定では、Azure Data Factory によって同名のテーブルが作成されます。 既存のテーブルにマップすることもできます。 

    > [!NOTE]
    > SQL Data Warehouse シンクに対するテーブルの自動作成は、SQL Server または Azure SQL Database がソースの場合に適用されます。 その他のソース データ ストアからデータをコピーする場合は、データをコピーする前にシンク Azure SQL Data Warehouse 内にスキーマを事前に作成しておく必要があります。

    ![[テーブル マッピング] ページ](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. **[スキーマ マッピング]** ページで、表示内容を確認した後、**[次へ]** をクリックします。 インテリジェント マッピングは、列名に基づきます。 データ ファクトリの自動テーブル作成を選択すると、マップ元とマップ先のストアの非互換性を修正する必要がある場合に、該当するデータ型の変換が発生します。 コピー元とコピー先の列の間でサポートされていないデータ型変換がある場合、対応するテーブルの横にエラー メッセージが表示されます。

    ![[スキーマ マッピング] ページ](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. **[設定]** ページで、**[ストレージ アカウント名]** ドロップダウン リストから Azure Storage を選択します。 それは、PolyBase を使用して SQL Data Warehouse に読み込む前に、データをステージングするために使用されます。 ストレージ内の暫定データは、コピーの完了後に自動的にクリーンアップされます。 

    [詳細設定] の下の [使用型の既定] オプションをオフにします。

    ![[設定] ページ](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. **[概要]** ページで設定を確認し、**[次へ]** をクリックします。

    ![概要ページ](./media/load-azure-sql-data-warehouse/summary-page.png)
13. **[Deployment]\(デプロイ\)** ページで、**[監視]** をクリックしてパイプライン (タスク) を監視します。

    ![[Deployment]\(デプロイ\) ページ](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. 左側の **[監視]** タブが自動的に選択されたことがわかります。 **[アクション]** 列で、アクティビティの実行の詳細を表示するリンクとパイプラインを再実行するリンクを確認します。 

    ![パイプラインの実行を監視する](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. パイプラインの実行に関連付けられているアクティビティの実行を表示するために、**[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクをクリックします。 パイプラインには 10 個のコピー アクティビティがあり、それぞれが 1 つのデータ テーブルをコピーします。 [Pipeline Runs]\(パイプラインの実行\) ビューに戻るには、上部の **[パイプライン]** リンクをクリックします。 **[最新の情報に更新]** をクリックして、リストを更新します。 

    ![アクティビティの実行を監視する](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. さらに、アクティビティ監視ビューの **[アクション]** の下の **[詳細]** リンクをクリックすることで、各コピー アクティビティの実行状況の詳細を監視できます。 ソースからシンクにコピーされたデータ ボリューム、スループット、対応する期間に処理されるステップ、および使用される構成などの情報が表示されます。

    ![アクティビティの実行状況の詳細の監視](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>次の手順

次の資料に進んで、Azure SQL Data Warehouse のサポートを確認します。 

> [!div class="nextstepaction"]
>[Azure SQL Data Warehouse コネクタ](connector-azure-sql-data-warehouse.md)