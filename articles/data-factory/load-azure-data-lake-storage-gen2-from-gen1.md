---
title: Azure Data Factory を使用して Azure Data Lake Storage Gen1 から Gen2 にデータをコピーする
description: Azure Data Factory を使用して Azure Data Lake Storage Gen1 から Gen2 にデータをコピーします
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: d6e09ec1f070f9ee0f4162524e4bd80d1f81adc3
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560655"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Azure Data Factory を使用して Azure Data Lake Storage Gen1 から Gen2 にデータをコピーする

Azure Data Lake Storage Gen2 は、ビッグ データ分析専用の機能セットであり、[Azure BLOB ストレージ](../storage/blobs/storage-blobs-introduction.md)に組み込まれます。 ファイル システムとオブジェクト ストレージの両方のパラダイムを使用して、データと連携させることができます。

現在 Azure Data Lake Storage Gen1 を使用している場合、Azure Data Factory を使用して Azure Data Lake Storage Gen1 から Gen2 (プレビュー) にデータをコピーすることで、Gen2 の新機能を評価できます。

Azure Data Factory は、フル マネージドのクラウドベースのデータ統合サービスです。 このサービスを使用して、オンプレミスとクラウドベースのデータストアの豊富なセットからのデータをレイクに入力し、分析ソリューションをビルドする際の時間を節約できます。 サポートされるコネクタの詳細な一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。

Azure Data Factory では、スケール アウトしたマネージド データ移動ソリューションを提供しています。 ADF のスケールアウト アーキテクチャにより、高スループットでデータを取り込むことができます。 詳しくは、[コピー アクティビティのパフォーマンス](copy-activity-performance.md)に関する記事をご覧ください。

この記事では、Data Factory のデータのコピー ツールを使用して "_Azure Data Lake Storage Gen1_" から "_Azure Data Lake Storage Gen2_" にデータをコピーする方法を示します。 その他の種類のデータ ストアからデータをコピーする場合も、同様の手順で実行できます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* データを備えた Azure Data Lake Storage Gen1 アカウント。
* Data Lake Storage Gen2 が有効な Azure Storage アカウント:ストレージ アカウントがない場合、[作成します](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. 左側のメニューで、 **[リソースの作成]**  >  **[データ + 分析]**  >  **[Data Factory]** の順に選択します。
   
   ![[新規] ウィンドウでの [Data Factory] の選択](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **[新しいデータ ファクトリ]** ページで、次の画像に示されているフィールドの値を指定します。 
      
   ![[新しいデータ ファクトリ] ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **名前**:Azure Data Factory のグローバルに一意の名前を入力します。 "データ ファクトリ名 \"LoadADLSDemo\" は利用できません" エラーが発生する場合は、データ ファクトリの別の名前を入力します。 たとえば、 _**yourname**_ **ADFTutorialDataFactory** という名前を使用できます。 データ ファクトリをもう一度作成してみます。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事をご覧ください。
    * **サブスクリプション**:データ ファクトリを作成する Azure サブスクリプションを選択します。 
    * **リソース グループ**:ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** オプションを選択し、リソース グループの名前を入力します。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
    * **バージョン**: **[V2]** を選択します。
    * **場所**:データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストアは、他の場所やリージョンにあってもかまいません。 

3. **作成** を選択します。
4. 作成が完了したら、データ ファクトリに移動します。 次の画像のように **[データ ファクトリ]** ホーム ページが表示されます。 
   
   ![データ ファクトリのホーム ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

   **[作成と監視]** タイルを選択して、別のタブでデータ統合アプリケーションを起動します。

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 にデータを読み込む

1. **[Get started]\(開始\)** ページで、 **[データのコピー]** タイルを選択してデータのコピー ツールを起動します。 

   ![データのコピー ツールのタイル](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. **[プロパティ]** ページで、 **[タスク名]** フィールドに「**CopyFromADLSGen1ToGen2**」と指定し、 **[次へ]** を選択します。

    ![[プロパティ] ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. **[ソース データ ストア]** ページで、 **[+ 新しい接続の作成]** をクリックします。

    ![[ソース データ ストア] ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
    コネクタ ギャラリーから **[Azure Data Lake Storage Gen1]** を選択し、 **[続行]** を選択します。
    
    ![ソース データ ストアの [Azure Data Lake Storage Gen1] ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
4. **[Specify Azure Data Lake Storage Gen1 connection]\(Azure Data Lake Storage Gen1 の接続の指定\)** ページで、次の手順を実行します。
   1. アカウント名に対してお使いの Data Lake Storage Gen1 を選択し、 **[テナント]** を指定または確認します。
   2. **[テスト接続]** をクリックして設定を検証し、 **[完了]** を選択します。
   3. 新しい接続が作成されたことを確認します。 **[次へ]** を選択します。
   
   > [!IMPORTANT]
   > このチュートリアルでは、Azuure リソースのマネージド ID を使用して、Data Lake Storage Gen1 を認証します。 [次の手順](connector-azure-data-lake-store.md#managed-identity)に従って、MSI に Azure Data Lake Storage Gen1 のアクセス許可を適切に付与します。
   
   ![Azure Data Lake Storage Gen1 アカウントを指定する](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
5. **[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)** ページで、コピーするフォルダーとファイルを参照します。 フォルダーまたはファイルを選択し、 **[選択]** を選択します。

    ![入力ファイルまたはフォルダーの選択](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

6. **[Copy files recursively]\(ファイルを再帰的にコピー\)** オプションと **[バイナリ コピー]** オプションをオンにし、コピーの動作を指定します。 **[次へ]** を選択します。

    ![出力フォルダーの指定](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
7. **[配布先データ ストア]** ページで、 **[+ 新しい接続の作成]** をクリックし、 **[Azure Data Lake Storage Gen2]** を選択して、 **[続行]** を選択します。

    ![[Destination data store]\(コピー先データ ストア\) ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

8. **[Specify Azure Data Lake Storage Gen2 connection]\(Azure Data Lake Storage Gen2 の接続の指定\)** ページで、次の手順を実行します。

   1. [ストレージ アカウント名] ボックスの一覧から目的の Data Lake Storage Gen2 に対応するアカウント名を選択します。
   2. **[完了]** を選択して、接続を作成します。 次に、 **[次へ]** を選択します。
   
   ![Azure Data Lake Storage Gen2 アカウントを指定する](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

9. **[Choose the output file or folder]\(出力ファイルまたはフォルダーの選択\)** ページで、出力フォルダー名として「**copyfromadlsgen1**」と入力し、 **[次へ]** を選択します。 ADF は、対応する ADLS Gen2 ファイル システムとサブ フォルダーがない場合には、コピー中に作成します。

    ![出力フォルダーの指定](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

10. **[設定]** ページで、 **[次へ]** を選択して、既定の設定を使用します。

11. **[サマリー]** ページで設定を確認し、 **[次へ]** を選択します。

    ![概要ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
12. **[Deployment]\(デプロイ\)** ページで **[監視]** を選択してパイプラインを監視します。

    ![[Deployment]\(デプロイ\) ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
13. 左側の **[監視]** タブが自動的に選択されたことがわかります。 **[アクション]** 列には、アクティビティの実行の詳細を表示するリンクとパイプラインを再実行するリンクが表示されます。

    ![パイプラインの実行を監視する](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

14. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択します。 パイプライン内のアクティビティ (コピー アクティビティ) は 1 つだけなので、エントリは 1 つのみです。 パイプラインの実行ビューに戻るには、上部の **[パイプライン]** リンクを選択します。 **[最新の情報に更新]** を選択して、一覧を更新します。 

    ![アクティビティの実行を監視する](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

15. 各コピー アクティビティの実行状況の詳細を監視するには、アクティビティ監視ビューの **[アクション]** の下の **[詳細]** リンク (眼鏡のイメージ) を選択します。 ソースからシンクにコピーされるデータの量、データのスループット、実行ステップと対応する期間、使用される構成などの詳細を監視することができます。

    ![アクティビティの実行状況の詳細の監視](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

16. データが Data Lake Storage Gen2 アカウントにコピーされたことを確認します。

## <a name="best-practices"></a>ベスト プラクティス

Azure Data Lake Storage (ADLS) Gen1 から Gen2 からへのアップグレードの評価の概要については、「[ビッグ データ分析ソリューションを Azure Data Lake Storage Gen1 から Azure Data Lake Storage Gen2 にアップグレードする](../storage/blobs/data-lake-storage-upgrade.md)」をご覧ください。 以下のセクションでは、Gen1 から Gen2 へのデータのアップグレードに ADF を使用する場合のベスト プラクティスを示します。

### <a name="data-partition-for-historical-data-copy"></a>履歴データ コピーの場合のデータ パーティション

- ADLS Gen1 の合計データ サイズが **30 TB** より小さく、ファイルの数が **100 万個**より少ない場合は、1 回のコピー アクティビティの実行ですべてのデータをコピーできます。
- それより大きいサイズのデータをコピーする場合、またはバッチでのデータの移行を柔軟に管理し、各バッチを特定のタイミング ウィンドウ内で完了したい場合は、データをパーティションに分割することをお勧めします。その場合、予期しない問題のリスクを軽減することもできます。

エンド ツー エンドのソリューションを確認し、環境内でのコピーのスループットをテストするため、PoC (概念実証) を強くお勧めします。 PoC の主要な手順は次のとおりです。 

1. ADLS Gen1 から ADLS Gen2 に数 TB のデータをコピーする単一のコピー アクティビティを含む 1 つの ADF パイプラインを作成し、コピーのパフォーマンスのベースラインを取得します。最初は [データ統合ユニット (DIU)](copy-activity-performance.md#data-integration-units) を 128 にします。 
2. ステップ 1 で取得したコピー スループットに基づいて、データ移行全体に必要な推定時間を計算します。 
3. (省略可能) 制御テーブルを作成し、移行するファイルをパーティションに分割するファイル フィルターを定義します。 ファイルをパーティションに分割する方法は次のとおりです。 

    - フォルダー名またはワイルドカード フィルターを含むフォルダー名によるパーティション分割 (推奨) 
    - ファイルの最終変更日時によるパーティション分割 

### <a name="network-bandwidth-and-storage-io"></a>ネットワーク帯域幅とストレージ I/O 

データを ADLS Gen1 から読み取って ADLS Gen2 に書き込む ADF コピー ジョブのコンカレンシーを制御でき、移行中に ADLS Gen1 での通常業務の作業に影響が出ないよう、ストレージ I/O の使用量を管理できます。

### <a name="permissions"></a>アクセス許可 

Data factory の場合、[ADLS Gen1 コネクタ](connector-azure-data-lake-store.md)では、サービス プリンシパルとマネージド ID が Azure リソース認証に対してサポートされています。[ADLS Gen2 コネクタ](connector-azure-data-lake-storage.md)では、アカウント キー、サービス プリンシパル、マネージド ID が Azure リソース認証に対してサポートされています。 必要に応じてすべてのファイル/ACL を Data Factory でナビゲートおよびコピーできるようにするには、使用するアカウントに、ファイルのアクセス/読み取り/書き込みおよび ACL の設定 (行うことを選択した場合) を行うことができる十分に高いアクセス許可を付与します。 移行期間中にスーパーユーザー/所有者ロールとして付与することをお勧めします。 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Data Lake Storage Gen1 の ACL を保持する

Data Lake Storage Gen1 から Gen2 にアップグレードする際に ACL をデータ ファイルと一緒にレプリケートする必要がある場合は、「[Preserve ACLs from Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)」 (Data Lake Storage Gen1 の ACL を保持する) を参照してください。 

### <a name="incremental-copy"></a>増分コピー 

ADLS Gen1 から新規ファイルまたは更新されたファイルのみを読み込むには、いくつかの方法があります。

- 日時でパーティション分割されたフォルダーまたはファイル名により、新しいファイルまたは更新されたファイルを読み込みます (例: /2019/05/13/*)
- LastModifiedDate で新しいファイルまたは更新されたファイルを読み込みます
- サード パーティ製ツール/ソリューションで新しいファイルまたは更新されたファイルを識別した後、パラメーターまたはテーブル/ファイルを使用して、ADF パイプラインにファイルまたはフォルダー名を渡します。  

増分読み込みを行う適切な頻度は、ADLS Gen1 内のファイルの合計数と、毎回読み込まれる新しいファイルまたは更新されたファイルのボリュームに依存します。  

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [コピー アクティビティの概要](copy-activity-overview.md)
> [Azure Data Lake Storage Gen1 コネクタ](connector-azure-data-lake-store.md)
> [Azure Data Lake Storage Gen2 コネクタ](connector-azure-data-lake-storage.md)