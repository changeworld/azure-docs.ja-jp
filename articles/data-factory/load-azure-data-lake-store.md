---
title: Azure Data Lake Storage Gen1 にデータを読み込む
description: Azure Data Factory を使用して Azure Data Lake Storage Gen1 にデータをコピーします
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/05/2021
ms.openlocfilehash: 5a82f60bfbf97ebc0de6f1ff9f8214c995775bf2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819924"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Azure Data Factory を使用した Azure Data Lake Storage Gen1 へのデータの読み込み

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (旧称 Azure Data Lake Store) は、ビッグ データの分析ワークロードに対応するエンタープライズ規模のハイパースケール リポジトリです。 Azure Data Lake Storage Gen1 では、任意のサイズ、型、および取り込み速度のデータをキャプチャできます。 データは、運用分析や調査分析のために 1 か所でキャプチャされます。

Azure Data Factory は、フル マネージドのクラウドベースのデータ統合サービスです。 このサービスを使用して、既存のシステムのデータで Azure Data Lake を設定し、分析ソリューションを構築する際の時間を節約できます。

Azure Data Factory には、Data Lake Storage Gen1 にデータを読み込む際に次の利点があります。

* **簡単なセットアップ**: 直感的なウィザードが示す 5 つの手順に従うだけです。スクリプトは必要ありません。
* **豊富なデータ ストアのサポート**:オンプレミスとクラウドベースのデータ ストアの豊富なセットに対するサポートが組み込まれています。 詳しい一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
* **セキュリティとコンプライアンスへの準拠**:データは HTTPS または ExpressRoute 経由で転送されます。 グローバル サービスの存在により、データが地理的な境界を越えることはありません。
* **ハイ パフォーマンス**: 最大 1 GB/s の速度で Data Lake Storage Gen1 にデータを読み込みます。 詳しくは、[コピー アクティビティのパフォーマンス](copy-activity-performance.md)に関する記事をご覧ください。

この記事では、Data Factory のデータのコピー ツールを使用して "_Amazon S3 から Data Lake Storage Gen1 にデータを読み込む_" 方法を示します。 その他の種類のデータ ストアからデータをコピーする場合も、同様の手順で実行できます。

> [!NOTE]
> 詳しくは、「[Copy data to or from Data Lake Storage Gen1 by using Azure Data Factory](connector-azure-data-lake-store.md)」(Azure Data Factory を使用した Data Lake Storage Gen1 のデータのコピー) をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* Data Lake Storage Gen1 アカウント: Data Lake Storage Gen1 アカウントがない場合は、「[Data Lake Storage Gen1 アカウントを作成する](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account)」の手順を参照してください。
* Amazon S3: この記事では、Amazon S3 からデータをコピーする方法を示します。 同様の手順に従うことによって、その他のデータ ストアも使用できます。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. 左側のメニューで、 **[リソースの作成]**  >  **[分析]**  >  **[Data Factory]** の順に選択します。
   
   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png" alt-text="[新規] ペインでの Data Factory の選択":::

2. **[新しいデータ ファクトリ]** ページで、次の画像に示されているフィールドの値を指定します。 
      
   :::image type="content" source="./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png" alt-text="[新しいデータ ファクトリ] ページ":::
 
    * **Name**:Azure Data Factory のグローバルに一意の名前を入力します。 "データ ファクトリ名 \"LoadADLSG1Demo\" は利用できません" エラーが発生する場合は、データ ファクトリの別の名前を入力します。 たとえば、_**yourname**_**ADFTutorialDataFactory** という名前を使用できます。 データ ファクトリをもう一度作成してみます。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事をご覧ください。
    * **サブスクリプション**:データ ファクトリを作成する Azure サブスクリプションを選択します。 
    * **リソース グループ**:ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** オプションを選択し、リソース グループの名前を入力します。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。  
    * **バージョン**: **[V2]** を選択します。
    * **[場所]** :データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストアは、他の場所やリージョンにあってもかまいません。 このようなデータ ストアには、Azure Data Lake Storage Gen1、Azure Storage、Azure SQL Database などがあります。

3. **［作成］** を選択します
4. 作成が完了したら、データ ファクトリに移動します。 次の画像のように **[データ ファクトリ]** ホーム ページが表示されます。 
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="[Open Azure Data Factory Studio] タイルを含む、Azure Data Factory のホーム ページ。":::

   **[Open Azure Data Factory Studio]\(Azure Data Factory Studio を開く\)** タイルで **[開く]** を選択して、別のタブでデータ統合アプリケーションを起動します。

## <a name="load-data-into-data-lake-storage-gen1"></a>Data Lake Storage Gen1 にデータを読み込む

1. ホーム ページで、 **[取り込み]** タイルを選択し、データのコピー ツールを起動します。 

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="ADF のホーム ページを示すスクリーンショット。":::
2. **[プロパティ]** ページで、 **[タスク名]** フィールドに「**CopyFromAmazonS3ToADLS**」と指定し、 **[次へ]** を選択します。

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png" alt-text="[プロパティ] ページ":::
3. **[ソース データ ストア]** ページで、 **[+ 新しい接続の作成]** をクリックします。

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/source-data-store-page.png" alt-text="[ソース データ ストア] ページ":::
    
    **[Amazon S3]** を選択し、 **[続行]** を選択します。
    
    :::image type="content" source="./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png" alt-text="ソース データ ストアの S3 ページ":::
    
4. **[Amazon S3 接続の指定]** ページで、次の手順を実行します。 
   1. **[アクセス キー ID]** の値を指定します。
   2. **[シークレット アクセス キー]** の値を指定します。
   3. **[完了]** を選択します。
   
      :::image type="content" source="./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png" alt-text="[新規のリンクされたサービス] ウィンドウを示すスクリーンショット。ここでは、値を入力できます。":::
   
   4. 新しい接続が表示されます。 **[次へ]** を選択します。
   
   :::image type="content" source="./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png" alt-text="新しい接続を示すスクリーンショット。":::
   
5. **[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)** ページで、コピーするフォルダーとファイルを参照します。 フォルダーまたはファイルを選択し、 **[選択]** 、 **[次へ]** の順に選択します。

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/choose-input-folder.png" alt-text="入力ファイルまたはフォルダーの選択":::

6. **[Copy files recursively]\(ファイルを再帰的にコピー\)** オプションと **[バイナリ コピー]** (ファイルをそのままコピー) オプションをオンにして、コピーの動作を選択します。 **[次へ]** を選択します。

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/specify-binary-copy.png" alt-text="[入力ファイルまたはフォルダーの選択] を示すスクリーンショット。ここでは、[Copy file recursively]\(ファイルを再帰的にコピー\) と [Binary Copy]\(バイナリ コピー\) を選択できます。":::
    
7. **[配布先データ ストア]** ページで、 **[+ 新しい接続の作成]** をクリックし、 **[Azure Data Lake Storage Gen1]** を選択して、 **[続行]** を選択します。

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png" alt-text="[Destination data store]\(コピー先データ ストア\) ページ":::

8. **[New Linked Service (Azure Data Lake Storage Gen1)]\(新しいリンク サービス (Azure Data Lake Storage Gen1)\)** ページで、次の手順を実行します。 

   1. **[Data Lake Store アカウント名]** で、Data Lake Storage Gen1 アカウントを選択します。
   2. **[テナント]** を指定し、[完了] を選択します。
   3. **[次へ]** を選択します。
   
   > [!IMPORTANT]
   > このチュートリアルでは、Azuure リソースのマネージド ID を使用して、Data Lake Storage Gen1 アカウントを認証します。 [次の手順](connector-azure-data-lake-store.md#managed-identity)に従って、MSI に Data Lake Storage Gen1 のアクセス許可を適切に付与します。
   
   :::image type="content" source="./media/load-data-into-azure-data-lake-store/specify-adls.png" alt-text="Data Lake Storage Gen1 アカウントの指定":::
9. **[Choose the output file or folder]\(出力ファイルまたはフォルダーの選択\)** ページで、出力フォルダー名として「**copyfroms3**」と入力し、 **[次へ]** を選択します。 

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/specify-adls-path.png" alt-text="入力したフォルダーのパスを示すスクリーンショット。":::

10. **[設定]** ページで **[次へ]** を選択します。

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/copy-settings.png" alt-text="設定ページ":::
11. **[サマリー]** ページで設定を確認し、 **[次へ]** を選択します。

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/copy-summary.png" alt-text="[概要] ページ":::
12. **[Deployment]\(デプロイ\)** ページで **[監視]** を選択してパイプライン (タスク) を監視します。

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/deployment-page.png" alt-text="[Deployment]\(デプロイ\) ページ":::
13. 左側の **[監視]** タブが自動的に選択されたことがわかります。 **[アクション]** 列には、アクティビティの実行の詳細を表示するリンクとパイプラインを再実行するリンクが表示されます。

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png" alt-text="パイプラインの実行を監視する":::
14. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択します。 パイプライン内のアクティビティ (コピー アクティビティ) は 1 つだけなので、エントリは 1 つのみです。 パイプラインの実行ビューに戻るには、上部の **[パイプライン]** リンクを選択します。 **[最新の情報に更新]** を選択して、一覧を更新します。 

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png" alt-text="アクティビティの実行を監視する":::

15. 各コピー アクティビティの実行状況の詳細を監視するには、アクティビティ監視ビューの **[アクション]** の下の **[詳細]** リンクを選択します。 ソースからシンクにコピーされるデータの量、データのスループット、実行ステップと対応する期間、使用される構成などの詳細を監視することができます。

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png" alt-text="アクティビティの実行状況の詳細の監視":::

16. データが Data Lake Storage Gen1 アカウントにコピーされたことを確認します。 

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/adls-copy-result.png" alt-text="Data Lake Storage Gen1 出力の確認":::

## <a name="next-steps"></a>次のステップ

次の資料に進んで、Data Lake Storage Gen1 のサポートを確認します。 

> [!div class="nextstepaction"]
>[Azure Data Lake Storage Gen1 コネクタ](connector-azure-data-lake-store.md)
