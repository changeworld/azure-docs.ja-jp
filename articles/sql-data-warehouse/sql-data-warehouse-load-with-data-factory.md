---

title: "Azure SQL Data Warehouse - Data Factory へのデータの読み込み | Microsoft Docs"
description: "このチュートリアルでは、Azure Data Factory を使用して Azure SQL Data Warehouse にデータを読み込み、データ ソースとして SQL Server データベースを使用します。"
services: sql-data-warehouse
documentationcenter: NA
author: linda33wj
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: jingwang;kevin;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5c8b3ef69cd2bc663d6ee744c2351da9c22adb94
ms.openlocfilehash: 24b9e5c2f423f7ef8bb29ab18359318f93c1d88c


---

# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>Data Factory による SQL Data Warehouse へのデータの読み込み

Azure Data Factory を使用して、[サポートされているソース データ ストア](../data-factory/data-factory-data-movement-activities.md#supported-data-stores-and-formats)からデータを Azure SQL Data Warehouse に読み込むことができます。 たとえば、Data Factory を使用して、Azure SQL Database や Oracle データベースのデータを SQL Data Warehouse に読み込めます。 この記事のチュートリアルでは、オンプレミスの SQL Server データベースから SQL Data Warehouse にデータを読み込む方法について説明します。

**Time estimate**: このチュートリアルでは、前提条件が満たされると、完了までに約 10 ～ 15 分かかります。

## <a name="prerequisites"></a>前提条件

- SQL Data Warehouse にコピーされるデータを含むテーブルを持つ SQL Server データベース。  

- Azure ストレージ アカウントが必要です。 [無料の Azure アカウントを作成する](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)か、[Visual Studio サブスクライバーの特典を有効にする](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)ことができます。 

- オンラインの SQL Data Warehouse が必要です。 データ ウェアハウスがまだない場合は、[Azure SQL Data Warehouse の作成](sql-data-warehouse-get-started-provision.md)方法に関するトピックを参照してください。 最大のパフォーマンスを引き出すため、ストレージ アカウントとデータ ウェアハウスは同じ Azure リージョンに配置してください。

- 1 つまたは複数のテーブル スキーマを作成して、受信データを受信するデータ ウェアハウスを準備します。 [SQL Data Warehouse 移行ユーティリティ](sql-data-warehouse-migrate-migration-utility.md) を使用して、スキーマのスクリプトを作成できます。 

## <a name="configure-a-new-data-factory"></a>新しいデータ ファクトリ ページを構成する
1. [Azure Portal][] にログインします。
2. データ ウェアハウスを見つけて、クリックして開きます。 
3. **[プロパティ]** ブレードで、**[データの読み込み] > [Azure Data Factory]** をクリックします。

    ![データの読み込みウィザードを起動する](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)
4. Azure サブスクリプションのデータ ファクトリがない場合は、ブラウザーの別のタブに **[新しいデータ ファクトリ]**ダイアログ ボックスが表示されます。 必要な情報を入力し、**[作成]** をクリックします。 データ ファクトリを作成した後、**[新しいデータ ファクトリ]** ダイアログ ボックスが閉じ、**[Select Data Factory (データ ファクトリの選択)]** ダイアログ ボックスが表示されます。

    Azure サブスクリプションに既に&1; つ以上のデータ ファクトリがある場合は、**[Select Data Factory (データ ファクトリの選択)]** ダイアログ ボックスが表示されます。 このダイアログボックスでは、既存のデータ ファクトリを選択するか、**[Data Factory の新規作成]** をクリックして新しいデータ ファクトリを作成します。 
5. **[Select Data Factory (データ ファクトリの選択)]** ダイアログ ボックスでは、**[データの読み込み]** オプションが既定で選択されています。 **[次へ]** をクリックして、データ読み込みタスクの作成を開始します。 

## <a name="configure-the-data-factory-properties"></a>データ ファクトリのプロパティを構成する
データ ファクトリを作成したら、次にデータの読み込みのスケジュールを構成します。 

1. **[プロパティ]** を選択し、必要な情報を入力します。
2. **[タスク名]** には、「**DWLoadData-fromSQLServer**」と入力します。
3. **[次へ]**をクリックします。

    ![読み込みのスケジュールを構成する](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-data-factory-source-and-gateway"></a>データ ファクトリのソースとゲートウェイを構成する
ここで、データを読み込むオンプレミスの SQL Server データベースをデータ ファクトリに指示します。

1. **[ソース]** をクリックします。
2. サポートされているソース データのストア カタログから **[SQL Server]** を選択して、**[次へ]** をクリックします。

    ![SQL Server のソースを選択する](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

3. **[Specify the on-premises SQL Server database (オンプレミスの SQL Server データベースを指定)]** ダイアログが表示されます。 必須フィールドに必要事項を以下のとおり入力します。

    - **[接続名]**: 接続の新しい名前を指定します。
    - **[サーバー名]**: オンプレミスの SQL Server の名前。
    - **[データベース名]**: SQL Server データベース。
    - **[資格情報の暗号化]**: なし。 
    - **[認証の種類]**: 使用している認証の種類を選択します。
    - **[ユーザー名]** と **[パスワード]**: データをコピーする権限を持っているユーザーのユーザー名とパスワードを入力します。

4. 最後のフィールドでは、ゲートウェイの名前が要求されます。 ソース データ ストアがオンプレミスまたは Azure IaaS 仮想マシンにある場合は、ゲートウェイが必要です。 ゲートウェイが既にある既存のデータ ファクトリを使用している場合は、ドロップダウン リストからゲートウェイを選択して再利用できます。 **[ゲートウェイの作成]** リンクをクリックしてデータ管理ゲートウェイを作成します。  

    > [!NOTE]
    > ゲートウェイは、データ ファクトリと 1 対 1 の関係にあります。 別のデータ ファクトリからは使用できませんが、同じデータ ファクトリでは複数のデータ読み込みタスクで使用できます。 ゲートウェイは、データ読み込みタスクを実行するときに複数のデータ ストアへの接続に使用できます。
    > 
    > ゲートウェイの詳細については、「[Data Management Gateway](../data-factory/data-factory-data-management-gateway.md)」に関する記事をご覧ください。 

5. **[ゲートウェイの作成]** ダイアログ ボックスが表示されます。 [名前] に「**GatewayForDWLoading**」と入力して、**[作成]** をクリックします。

6. **[Configure Gateway (ゲートウェイの構成)]** ダイアログ ボックスが表示されます。  
    ![[Launch Express setup (高速セットアップの起動)]](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

7. **[Launch express setup on this computer (このコンピューターで高速セットアップを起動)]** をクリックして、お使いのマシン (ポータルへのアクセスに使用しているマシン) にデータ管理ゲートウェイをダウンロード、インストール、登録します。 マシンがデータ ストアに接続できない場合は、データ ストアに接続できるマシンに手動で[ゲートウェイをダウンロードしてインストール](https://www.microsoft.com/download/details.aspx?id=39717)し、キーを使用して登録します。 進行状況は、ポップアップ ウィンドウに表示されます。

    > [!NOTE]
    > 高速セットアップは、Microsoft Edge および Internet Explorer でネイティブに動作します。 Google Chrome を使用している場合は、最初に Chrome Web ストアから ClickOnce 拡張機能をインストールしてください。 

8. ゲートウェイ セットアップが完了するまで待ちます。 ゲートウェイが正常に登録されてオンラインになると、ポップアップ ウィンドウが閉じてゲートウェイ フィールドに新しいゲートウェイが表示されます。 **[次へ]**にログインします。

9. 次に、データのコピー元のテーブルを選択します。 キーワードを使用してテーブルをフィルター処理することができます。 下部のパネルで、データとテーブルのスキーマをプレビューすることができます。 選択が完了したら、**[次へ]** をクリックします。

    ![テーブルを選択する](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>コピー先の SQL Data Warehouse を構成する

1. **[コピー先]** をクリックします。 SQL Data Warehouse 接続情報は自動的に入力されます。
2. 入力されたユーザー名のパスワードを入力します。 **[次へ]** をクリックします。

    ![コピー先を構成する](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

3. 類似した名前に基づいてコピー元テーブルをコピー先テーブルにマップする、インテリジェント テーブル マッピングが表示されます。 Ad
4.  任意のテーブルおよびその他のマッピングは、例から学習することにより自動的にマップされます。 
5. 確認して、**[次へ]** をクリックします。

    ![テーブルをマップする](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

6. スキーマ マッピングを確認し、エラー メッセージを検索します。 インテリジェント マッピングは、列名に基づきます。 コピー元とコピー先の列の間でサポートされていないデータ型変換がある場合、対応するテーブルの横にエラー メッセージが表示されます。

    ![スキーマをマップする](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

7. **[次へ]**にログインします。

## <a name="configure-the-performance-settings"></a>パフォーマンスの設定を構成する
パフォーマンスの構成では、データのステージングに使用する Azure ストレージ アカウントを、SQL Data Warehouse に読み込む前に構成します。

1. **[パフォーマンス]** をクリックします。 
2. 既存の Azure ストレージ アカウントを選択し、**[次へ]** をクリックします。

    ![ステージング BLOB の構成](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>概要情報を確認してパイプラインをデプロイする

1. **[概要]** をクリックして情報を確認します。
2. **[完了]** ボタンをクリックしてパイプラインをデプロイします。

    ![データ ファクトリのデプロイ](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>データ読み込みの進行状況を監視する

デプロイが完了すると、**[デプロイ]** オプションが左側のメニューに表示されます。 

1. **[デプロイ]** をクリックします。
2. データ読み込みの進行状況を監視するには、**[Click here to monitor copy pipeline (コピーのパイプラインを監視するにはここをクリック)]** というリンクをクリックします。

    ![パイプラインを監視する](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

3. **[リソース エクスプローラー]** で、パイプラインのノードを展開して [**DWLoadData-fromSQL] をクリックします。
4. サーバー** このチュートリアルで作成した、データを読み込むパイプラインです。

    ![パイプラインの表示](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

5. アクティビティにマップされるテーブルごとの詳細なステータスを表示するには、パイプラインをクリックします。

    ![テーブルの利用状況の表示](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

6. さらに、アクティビティをクリックすると、データのサイズ、行、スループットなどのデータ読み込みの詳細が右側のパネルに表示されます。

    ![テーブルの利用状況詳細の表示](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

7. この監視ビューを後で起動するには、SQL Data Warehouse に移動して **[データの読み込み > Azure Data Factory]** をクリックし、ファクトリを選択して **[Monitor existing loading tasks (既存の読み込み中タスクを監視)]** を選択します。

## <a name="next-steps"></a>次のステップ

SQL Data Warehouse にデータベースを移行するには、[移行の概要](sql-data-warehouse-overview-migrate.md)に関する記事を参照してください。

Azure Data Factory とそのデータ移動機能の詳細については、次の記事をご覧ください。 

- [Azure Data Factory の概要](../data-factory/data-factory-introduction.md)
- [コピー アクティビティを使用したデータの移動](../data-factory/data-factory-data-movement-activities.md)
- [Azure Data Factory を使用した Azure SQL Data Warehouse との間でのデータの移動](../data-factory/data-factory-azure-sql-data-warehouse-connector.md)

SQL Data Warehouse のデータを探索するには、次の記事をご覧ください。 

- [Visual Studio と SSDT を使用して SQL Data Warehouse に接続する](sql-data-warehouse-query-visual-studio.md) 
- [Power BI を使用したビジュアル データ](sql-data-warehouse-get-started-visualize-with-power-bi.md)

<!-- Azure references -->
[Azure Portal]: https://portal.azure.com 


<!--HONumber=Dec16_HO2-->


