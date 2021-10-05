---
title: Azure Synapse Analytics にデータを読み込む
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Azure Synapse パイプラインを使用して、Azure Synapse Analytics にデータをコピーします。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: e91d2dbae4f73e99a5e341330c0841e8173cedc8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819919"
---
# <a name="load-data-into-azure-synapse-analytics-using-azure-data-factory-or-a-synapse-pipeline"></a>Azure Data Factory または Synapse パイプラインを使用して Azure Synapse Analytics にデータを読み込む

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) は、クラウドベースのスケールアウト データベースであり、リレーショナルか非リレーショナルかを問わず、大量のデータを処理できます。 Azure Synapse Analytics は、企業のデータ ウェアハウスのワークロード向けに最適化された超並列処理 (MPP) アーキテクチャを基盤としています。 ストレージとコンピューティングを別々にスケールできる柔軟性によって、クラウドの弾力性を提供します。

Azure Synapse Analytics の使用開始は、これまでになく簡単になっています。 Azure Data Factory と、Azure Synapse 自体の同等のパイプライン機能によって、完全に管理されたクラウドベースのデータ統合サービスが提供されます。 このサービスを使用して、既存のシステムのデータで Azure Synapse Analytics を設定し、分析ソリューションを構築する際の時間を節約できます。

Azure Data Factory と Synapse パイプラインには、Azure Synapse Analytics へのデータの読み込みにとって次の利点があります。

* **簡単なセットアップ**: 直感的なウィザードが示す 5 つの手順に従うだけです。スクリプトは必要ありません。
* **豊富なデータ ストアのサポート**:オンプレミスとクラウドベースのデータ ストアの豊富なセットに対するサポートが組み込まれています。 詳しい一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
* **セキュリティとコンプライアンスへの準拠**:データは HTTPS または ExpressRoute 経由で転送されます。 グローバル サービスの存在により、データが地理的な境界を越えることはありません。
* **PolyBase の使用による比類のないパフォーマンス**:PolyBase は、Azure Synapse Analytics にデータを移動するための最も効率的な方法です。 ステージング BLOB の機能を使用して、Azure Blob Storage と Data Lake Store を含むすべての種類のデータ ストアからデータを高速で読み込むことができます。 (PolyBase では Azure Blob Storage と Azure Data Lake Store が既定でサポートされます。)詳しくは、[コピー アクティビティのパフォーマンス](copy-activity-performance.md)に関する記事をご覧ください。

この記事では、データのコピー ツールを使用して "_Azure SQL Database から Azure Synapse Analytics にデータを読み込む_" 方法を示します。 その他の種類のデータ ストアからデータをコピーする場合も、同様の手順で実行できます。

> [!NOTE]
> 詳しくは、[Azure Synapse Analytics をコピー先またはコピー元としてデータをコピーする](connector-azure-sql-data-warehouse.md)ことに関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* Azure Synapse Analytics:SQL データベースからコピーされたデータは、データ ウェアハウスに格納されます。 Azure Synapse Analytics がない場合は、[Azure Synapse Analytics インスタンスの作成](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md)の手順を参照してください。
* Azure SQL Database:このチュートリアルでは、Azure SQL Database 内に Adventure Works LT サンプル データセットからのデータをコピーします。 SQL Database 内にこのサンプル データベースを作成するには、[Azure SQL Database でのサンプル データベースの作成](../azure-sql/database/single-database-create-quickstart.md)に関する記事の手順に従います。
* Azure ストレージ アカウント:Azure Storage は、一括コピー操作の _ステージング_ BLOB として使用されます。 Azure ストレージ アカウントがない場合は、「[ストレージ アカウントの作成](../storage/common/storage-account-create.md)」の手順をご覧ください。

## <a name="create-a-data-factory"></a>Data Factory の作成

> [!NOTE]
> 既存の Synapse ワークスペース内でパイプライン機能を使用してデータを読み込む場合は、新しいデータ ファクトリの作成をスキップできます。  Azure Synapse では、Azure Data Factory の機能が、そのパイプライン機能に埋め込まれています。

1. 左側のメニューで、 **[リソースの作成]**  >  **[データ + 分析]**  >  **[Data Factory]** の順に選択します。

2. **[新しいデータ ファクトリ]** ページで、次の項目の値を指定します。

    * **Name**:名前に「*LoadSQLDWDemo*」を入力します。 データ ファクトリの名前はグローバルに一意にする必要があります。 "データ ファクトリ名 'LoadSQLDWDemo' は利用できません" エラーが発生する場合は、データ ファクトリの別の名前を入力します。 たとえば、_**yourname**_**ADFTutorialDataFactory** という名前を使用できます。 データ ファクトリをもう一度作成してみます。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事をご覧ください。
    * **サブスクリプション**:データ ファクトリを作成する Azure サブスクリプションを選択します。 
    * **リソース グループ**:ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** オプションを選択し、リソース グループの名前を入力します。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。  
    * **バージョン**: **[V2]** を選択します。
    * **[場所]** :データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストアは、他の場所やリージョンにあってもかまいません。 このようなデータ ストアには、Azure Data Lake Store、Azure Storage、Azure SQL Database などがあります。

3. **［作成］** を選択します
4. 作成が完了したら、データ ファクトリに移動します。 次の画像のように **[データ ファクトリ]** ホーム ページが表示されます。

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="[Open Azure Data Factory Studio] タイルを含む、Azure Data Factory のホーム ページ。":::

   **[Open Azure Data Factory Studio]\(Azure Data Factory Studio を開く\)** タイルで **[開く]** を選択して、別のタブでデータ統合アプリケーションを起動します。

## <a name="load-data-into-azure-synapse-analytics"></a>Azure Synapse Analytics にデータを読み込む

1. Azure Data Factory または Azure Synapse ワークスペースのホーム ページで、 **[取り込み]** タイルを選択してデータのコピー ツールを起動します。  次に、 **[組み込みコピー タスク]** を選択します。

2. **[プロパティ]** ページで、 **[タスクの種類]** に **[組み込みコピー タスク]** を選択し、 **[次へ]** を選択します。

    :::image type="content" source="./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png" alt-text="[プロパティ] ページ":::

3. **[ソース データ ストア]** ページで、次の手順を実行します。
    >[!TIP]
    >ソース データ ストアの認証の種類として、このチュートリアルでは "*SQL 認証*" を使用しますが、サポートされている他の認証方法 ("*サービス プリンシパル*"、"*マネージド ID*") を必要に応じて選ぶこともできます。 詳細については、[この記事](./connector-azure-sql-database.md#linked-service-properties)の対応するセクションを参照してください。
    >さらに、データ ストアのシークレットを安全に格納するために、Azure Key Vault の使用をお勧めします。 詳細については、[この記事](./store-credentials-in-key-vault.md)を参照してください。

    1. **[+ 新しい接続]** を選択します。

    1. ギャラリーから **[Azure SQL Database]** を選択し、 **[続行]** を選択します。 検索ボックスに「SQL」と入力して、コネクタをフィルター処理できます。

        :::image type="content" source="./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png" alt-text="Azure SQL DB の選択":::
    
    1. **[新しい接続 (Azure SQL Database)]** ページで、ドロップダウン リストからご利用のサーバー名と DB 名を選択し、ユーザー名とパスワードを指定します。 **[テスト接続]** を選択して設定を検証し、 **[作成]** を選択します。

        :::image type="content" source="./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png" alt-text="Azure SQL DB の構成":::
    
    1. **[ソース データ ストア]** ページの **[接続]** セクションで、新しく作成した接続をソースとして選択します。

    1. **[ソース テーブル]** セクションで、テーブルをフィルター処理するために「**SalesLT**」と入力します。 **[(すべて選択)]** ボックスを選択してコピーにすべてのテーブルを使用し、 **[次へ]** を選択します。

    :::image type="content" source="./media/load-azure-sql-data-warehouse/source-data-store-page.png" alt-text="[ソース データ ストア] ページの構成を示すスクリーンショット。":::

4. **[フィルターの適用]** ページで、設定を指定するか、 **[次へ]** を選択します。 このページの **[データのプレビュー]** ボタンを選択することで、データをプレビューし、入力データのスキーマを表示できます。 

    :::image type="content" source="./media/load-azure-sql-data-warehouse/apply-filter.png" alt-text=" [フィルターの適用] ページを示すスクリーンショット。":::

5. **[配布先データ ストア]** ページで、次の手順を実行します。
    >[!TIP]
    >配布先データ ストアの認証の種類として、このチュートリアルでは "*SQL 認証*" を使用しますが、サポートされている他の認証方法 ("*サービス プリンシパル*"、"*マネージド ID*") を必要に応じて選ぶこともできます。 詳細については、[この記事](./connector-azure-sql-data-warehouse.md#linked-service-properties)の対応するセクションを参照してください。
    >さらに、データ ストアのシークレットを安全に格納するために、Azure Key Vault の使用をお勧めします。 詳細については、[この記事](./store-credentials-in-key-vault.md)を参照してください。

    1. **[+ 新しい接続]** を選択して、接続を追加します。

    1. ギャラリーから **[Azure Synapse Analytics]** を選択し、 **[続行]** を選択します。

        :::image type="content" source="./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png" alt-text="Azure Synapse Analytics を選択":::

    1. **[新しい接続 (Azure Synapse Analytics)]** ページで、ドロップダウン リストからご利用のサーバー名と DB 名を選択し、ユーザー名とパスワードを指定します。 **[テスト接続]** を選択して設定を検証し、 **[作成]** を選択します。

        :::image type="content" source="./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png" alt-text="Azure Synapse Analytics を構成":::

    1. **[Destination data store]\(コピー先データ ストア\)** ページの **[接続]** セクションで、新しく作成した接続をシンクとして選択します。

6. **[テーブル マッピング]** セクションで、内容を確認し、 **[次へ]** を選択します。 インテリジェント テーブル マッピングが表示されます。 ソース テーブルは、テーブル名に基づくコピー先テーブルにマップされます。 コピー先にソース テーブルが存在しない場合、既定ではサービスによって同名のコピー先テーブルが作成されます。 既存のコピー先テーブルにソース テーブルをマップすることもできます。

   :::image type="content" source="./media/load-azure-sql-data-warehouse/destination-data-store-page.png" alt-text="[Destination data store]\(コピー先データ ストア\) ページの構成を示すスクリーンショット。":::

1. **[列マッピング]** ページで、内容を確認し、 **[次へ]** を選択します。 インテリジェント テーブル マッピングは、列名に基づきます。 サービスによってテーブルが自動的に作成されるときに、コピー元とコピー先のストア間に互換性がない場合は、データ型の変換が行われます。 コピー元とコピー先の列の間でサポートされていないデータ型変換がある場合、対応するテーブルの横にエラー メッセージが表示されます。

    :::image type="content" source="./media/load-azure-sql-data-warehouse/schema-mapping.png" alt-text="[列マッピング] ページ":::

1. **[設定]** ページで、次の手順を完了します。

    1. **[タスク名]** フィールドに **[CopyFromSQLToSQLDW]** を指定します。
    1. **[Staging settings]\(ステージングの設定\)** セクションで、 **[+ 新規]** を選択してステージング ストレージを新規作成します。 このストレージは、PolyBase を使用して Azure Synapse Analytics に読み込む前に、データをステージングするために使用されます。 コピーの完了後、Azure Blob Storage 内の暫定データは自動的にクリーンアップされます。

    1. **[New Linked Service]\(新しいリンクされたサービス\)** ページで、ストレージ アカウントを選択し、 **[作成]** を選択してリンクされたサービスをデプロイします。

    1. **[使用型の既定]** オプションを選択解除し、 **[次へ]** を選択します。

    :::image type="content" source="./media/load-azure-sql-data-warehouse/configure-polybase.png" alt-text="PolyBase の構成":::

8. **[サマリー]** ページで設定を確認し、 **[次へ]** を選択します。

9. **[Deployment]\(デプロイ\)** ページで **[監視]** を選択してパイプライン (タスク) を監視します。 

    :::image type="content" source="./media/load-azure-sql-data-warehouse/deployment-complete-page.png" alt-text=" デプロイ ページを示すスクリーンショット。":::
 
10. 左側の **[監視]** タブが自動的に選択されたことがわかります。 パイプラインの実行が正常に完了したら、 **[パイプライン名]** 列の下の **[CopyFromSQLToSQLDW]** リンクを選択してアクティビティの実行の詳細を表示するか、パイプラインを再実行します。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
    :::image type="content" source="./media/load-azure-sql-data-warehouse/pipeline-monitoring.png" alt-text="パイプラインの実行を監視する":::    

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
    :::image type="content" source="./media/load-azure-sql-data-warehouse/pipeline-monitoring-synapse.png" alt-text="パイプラインの実行を監視する":::   

--- 

12. パイプラインの実行ビューに戻るには、上部の **[すべてのパイプラインの実行]** リンクを選択します。 **[最新の情報に更新]** を選択して、一覧を更新します。

    :::image type="content" source="./media/load-azure-sql-data-warehouse/activity-monitoring.png" alt-text="アクティビティの実行を監視する":::

1. 各コピー アクティビティの実行の詳細を監視するには、アクティビティの実行ビューの **[アクティビティ名]** の下の **[詳細]** リンク (眼鏡のアイコン) を選択します。 ソースからシンクにコピーされるデータの量、データのスループット、実行ステップと対応する期間、使用される構成などの詳細を監視できます。

    :::image type="content" source="./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png" alt-text="最初のアクティビティの実行の詳細の監視":::

    :::image type="content" source="./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png" alt-text="2 回目のアクティビティの実行の詳細の監視":::

## <a name="next-steps"></a>次のステップ

次の資料に進んで、Azure Synapse Analytics のサポートを確認します。

> [!div class="nextstepaction"]
>[Azure Synapse Analytics コネクタ](connector-azure-sql-data-warehouse.md)
