---
title: Azure Data Factory を使用して Azure SQL Database Edge からデータを同期する | Microsoft Docs
description: Azure SQL Database Edge と Azure Blob Storage の間でデータを同期する方法について説明します。
keywords: sql database edge,sql database edge からデータを同期する, sql database edge data factory
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 0e75da9516303bb4250b6847a4b381d07b3d7dad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509206"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-using-azure-data-factory"></a>チュートリアル:Azure Data Factory を使用して SQL Database Edge から Azure Blob Storage にデータを同期する

このチュートリアルでは、Azure Data Factory を使用し、Azure SQL Database Edge インスタンス内のテーブルから Azure Blob Storage にデータを増分同期します。

## <a name="before-you-begin"></a>開始する前に

Azure SQL Database Edge デプロイでデータベースまたはテーブルをまだ作成していない場合、次のいずれかの方法で 1 つ作成してください。

* [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) または [Azure Data Studio](/sql/azure-data-studio/download/) を使用して SQL Database Edge に接続し、データベースとテーブルを作成する SQL スクリプトを実行します。
* SQL Database Edge モジュールに直接接続し、[SQLCMD](/sql/tools/sqlcmd-utility/) を利用して SQL のデータベースとテーブルを作成します。 詳細については、[sqlcmd.exe を使用してデータベース エンジンを接続する方法](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/)に関するページを参照してください。
* SQLPackage.exe を使用して dacpac ファイルを SQL Database Edge コンテナーにデプロイします。 これは、モジュールで望ましいプロパティ構成の一環として SQLPackage ファイル URI を指定するか、SqlPackage.exe クライアント ツールを直接使用し、dacpac を SQL Database Edge にデプロイすることで自動化できます。

    sqlpackage をダウンロードするには、「[sqlpackage をダウンロードしてインストールする](/sql/tools/sqlpackage-download/)」を参照してください。 SqlPackage.exe には次のサンプル コマンドが用意されていますが、詳細については sqlpackage ドキュメントをご確認ください。

    **dacpac を作成する**:

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name> 
    ```

    **dacpac を適用する**:

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>基準値レベルを保存し、更新する SQL テーブルとプロシージャを作成する

基準値テーブルは、データが Azure ストレージと同期された最後のタイムスタンプを保存する目的で使用されます。 Transact-SQL (T-SQL) ストアド プロシージャは、同期のたびに基準値テーブルを更新する目的で使用されます。 

SQL Database Edge インスタンスで次のコマンドを実行します。

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-workflow"></a>Data Factory Workflow を作成する

このセクションでは、Azure SQL Database Edge 内のテーブルから Azure Blob Storage にデータを同期するために Azure Data Factory パイプラインを作成します。

### <a name="create-data-factory-using-the-data-factory-ui"></a>Data Factory UI を使用して Data Factory を作成する

こちらの[チュートリアル](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)の指示に基づいて Data Factory を作成します。

### <a name="create-a-data-factory-pipeline"></a>Data Factory パイプラインを作成する

1. Data Factory UI の **[開始]** ページで **[パイプラインを作成]** タイルを選択します。

    ![Data Factory - パイプラインの作成](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. パイプラインの **[プロパティ]** ウィンドウの **[全般]** ページで、名前として「**PeriodicSync**」と入力します。

3. 古い基準値を取得するための**検索**アクティビティを追加します。 **[アクティビティ]** ツールボックスで **[全般]** を展開し、パイプライン デザイナー画面に **[検索]** アクティビティをドラッグ アンド ドロップします。 アクティビティの名前を *OldWatermark* に変更します。

    ![古い基準値の検索](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. **[設定]** タブに切り替えて、 **[Source Dataset]\(ソース データセット\)** の **[+ 新規]** を選択します。 この手順では、watermarktable 内のデータを表すデータセットを作成します。 このテーブルには、前のコピー操作で使用されていた古い基準が含まれています。

5. **[新しいデータセット]** ウィンドウで **[Azure SQL Server]** を選択し、 **[続行]** を選択します。  

6. データセットの **[プロパティの設定]** ウィンドウで、[名前] に「*WatermarkDataset*」と入力します。

7. **[リンクされたサービス]** で **[新規]** を選択して、次の手順を実行します。

    1. **[名前]** に「*SQLDBEdgeLinkedService*」と入力します。

    2. **[サーバー名]** に SQL Database Edge サーバーの詳細を入力します。

    3. ドロップダウン リストから **[データベース名]** を入力します。

    4. **[ユーザー名]** と **[パスワード]** を入力します。

    5. SQL Database Edge インスタンスへの接続をテストするために、 **[テスト接続]** を選択します。

    6. **作成** を選択します。

    ![リンクされたサービスを作成する](media/tutorial-sync-data-factory/create-linked-service.png)

    7. **[OK]** を選択します。

8. **[設定]** タブで **[編集]** を選択します。

9. **[接続]** タブで、 **[テーブル]** に *[dbo].[watermarktable]* を選択します。 テーブル内のデータをプレビューする場合、 **[データのプレビュー]** を選択します。

10. 上部のパイプライン タブを選択するか、左側のツリー ビューでパイプラインの名前を選択し、パイプライン エディターに切り替えます。 **[検索]** アクティビティのプロパティ ウィンドウで、 **[Source Dataset]\(ソース データセット\)** フィールドで **[WatermarkDataset]** が選択されていることを確認します。

11. **[アクティビティ]** ツールボックスで **[全般]** を展開し、パイプライン デザイナー画面にもう 1 つの **[検索]** アクティビティをドラッグ アンド ドロップし、プロパティ ウィンドウの **[全般]** タブで名前を「**NewWatermark**」に設定します。 この検索アクティビティは、ターゲットにコピーされるソース データを持つテーブルから新しい基準値を取得します。

12. 2 つ目の **[検索]** アクティビティのプロパティ ウィンドウで、 **[設定]** タブに切り替え、 **[新規]** を選択し、新しい基準値を含むソース テーブルを指すデータセットを作成します。

13. **[新しいデータセット]** ウィンドウで、SQL Database Edge インスタンスを選択し、 **[続行]** を選択します。

    1. **[プロパティの設定]** ウィンドウで、 **[名前]** に「**SourceDataset**」と入力します。 [リンクされたサービス] で *[SQLDBEdgeLinkedService]* を選択します。

    2. [テーブル] には、***同期するテーブル***を選択します。 チュートリアルの後半で説明しますが、このデータセットのクエリも指定できます。 クエリは、この手順で指定するテーブルで優先されます。

    3. **[OK]** を選択します。

14. 上部のパイプライン タブを選択するか、左側のツリー ビューでパイプラインの名前を選択し、パイプライン エディターに切り替えます。 **[検索]** アクティビティのプロパティ ウィンドウで、 **[Source Dataset]\(ソース データセット\)** フィールドで **[SourceDataset]** が選択されていることを確認します。

15. **[クエリの使用]** フィールドで **[クエリ]** を選択したら、テーブル名を変更して次のクエリを入力します。テーブルからタイムスタンプの最大値のみを選択しています。 また、 **[First row only]\(先頭行のみ\)** チェック ボックスがオンになっていることを確認してください。

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![クエリの選択](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. **[アクティビティ]** ツールボックスで **[Move & Transform]\(移動と変換\)** を展開し、[アクティビティ] ツールボックスから **[コピー]** アクティビティをドラッグ アンド ドロップして、名前を「**IncrementalCopy**」に設定します。

17. **検索**アクティビティにアタッチされている**緑のボタン**を**コピー** アクティビティにドラッグして、両方の**検索**アクティビティを**コピー** アクティビティに接続します。 コピー アクティビティの境界線の色が青に変わったら、マウス ボタンを離します。

18. **[コピー アクティビティ]** を選択し、 **[プロパティ]** ウィンドウにアクティビティのプロパティが表示されることを確認します。

19. **[プロパティ]** ウィンドウで **[ソース]** タブに切り替え、以下の手順を実行します。

    1. **[Source Dataset]\(ソース データセット\)** フィールドで **[SourceDataset]** を選択します。

    2. **[クエリの使用]** フィールドで **[クエリ]** を選択します。

    3. **[クエリ]** フィールドに SQL クエリを入力します。 下のサンプル クエリ

    4. SQL クエリ:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. **[シンク]** タブに切り替えて、 **[Sink Dataset]\(シンク データセット\)** フィールドの **[+ 新規]** を選択します。

21. このチュートリアルでは、シンク データ ストアの種類は **Azure Blob Storage** です。 **[新しいデータセット]** ウィンドウで **[Azure Blob Storage]** を選択し、 **[続行]** を選択します。

22. **[形式の選択]** ウィンドウでデータの形式の種類を選択し、 **[続行]** を選択します。

23. **[プロパティの設定]** ウィンドウで、[名前] に「**SinkDataset**」と入力します。 [リンクされたサービス] で **[+ 新規]** を選択します。 この手順では、**Azure Blob Storage** への接続 (リンクされたサービス) を作成します。

24. **[New Linked Service (Azure Blob storage)]** \(新しいリンクされたサービス (Azure Blob Storage)\) ウィンドウで、次の手順を実行します。

    1. [名前] に「*AzureStorageLinkedService*」と入力します。

    2. **[ストレージ アカウント名]** に自分の Azure サブスクリプションの Azure Storage アカウントを選択します。

    3. **接続**をテストし、 **[完了]** を選択します。

25. **[プロパティの設定]** ウィンドウで、 **[リンクされたサービス]** に *[AzureStorageLinkedService]* が選択されていることを確認します。 次に **[作成]** を選択し、 **[OK]** を選択します。

26. **[シンク]** タブで、 **[編集]** を選択します。

27. *SinkDataset* の **[接続]** タブに移動して、次の手順を実行します。

    1. **[ファイル パス]** フィールドに「*asdedatasync/incrementalcopy*」と入力します。**adftutorial** は BLOB コンテナー名であり、**incrementalcopy** はフォルダー名です。 このコンテナーが存在しない場合は作成するか、既存のコンテナーの名前を設定してください。 Azure Data Factory は、出力フォルダー *incrementalcopy* が存在しない場合は、自動的に作成します。 **[ファイル パス]** の **[参照]** ボタンを使用して、BLOB コンテナー内のフォルダーに移動することもできます。

    2. **[ファイル パス]** フィールドの**ファイル**部分で **[動的なコンテンツの追加 [Alt+P]]** を選択し、*表示されたウィンドウで「@CONCAT('Incremental-', pipeline().RunId, '.txt')* 」と入力します。 続けて、 **[完了]** を選択します。 ファイル名は、式を使用して動的に生成されます。 各パイプラインの実行には、一意の ID があります。 コピー アクティビティは、実行 ID を使用して、ファイル名を生成します。

28. 上部のパイプライン タブを選択するか、左側のツリー ビューでパイプラインの名前を選択し、**パイプライン** エディターに切り替えます。

29. **[アクティビティ]** ツールボックスで **[General]\(一般\)** を展開し、 **[アクティビティ]** ツールボックスからパイプライン デザイナー画面に **[ストアド プロシージャ]** アクティビティをドラッグ アンド ドロップします。 **[コピー]** アクティビティの緑 (成功) の出力を **[ストアド プロシージャ]** アクティビティに**接続**します。

30. パイプライン デザイナーで **[ストアド プロシージャ アクティビティ]** を選択し、その名前を *SPtoUpdateWatermarkActivity* に変更します。

31. **[SQL Account]\(SQL アカウント\)** タブに切り替えて、 **[リンクされたサービス]** で *[SQLDBEdgeLinkedService]* を選択します。

32. **[ストアド プロシージャ]** タブに切り替えて、次の手順を実行します。

    1. **[ストアド プロシージャ名]** に *[dbo].[usp_write_watermark]* を選択します。

    2. ストアド プロシージャのパラメーターの値を指定するには、[Import parameter]\(インポート パラメーター\) を選択し、各パラメーターに次の値を入力します。

    |名前|種類|値|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|string|@{activity('OldWaterMark').output.firstRow.TableName}|

33. パイプライン設定を検証するには、ツール バーの **[検証]** を選択します。 検証エラーがないことを確認します。 **>>** を選択して **[Pipeline Validation Report]\(パイプライン検証レポート\)** ウィンドウを閉じます。

34. **[すべて公開]** ボタンを選択して、エンティティ (リンクされたサービス、データセット、およびパイプライン) を Azure Data Factory サービスに発行します。 発行が成功したというメッセージが表示されるまで待機します。

## <a name="trigger-a-pipeline-on-schedule"></a>スケジュールに基づいてパイプラインをトリガーする

1. パイプライン ツール バーで **[トリガーの追加]** を選択し、 **[New/Edit]\(新規作成/編集\)** を選択し、 **[+ 新規]** を選択します。

2. トリガーに *HourlySync* と名前を付け、 **[種類]** に [スケジュール] を選択し、 **[繰り返し]** に [1 時間ごと] を選択します。

3. **[OK]** を選択します。

4. **[すべて公開]** を選択します。

5. **[Trigger Now]\(今すぐトリガー\)** を選択します。

6. 左側で **[監視]** タブに切り替えます。 手動トリガーによってトリガーされたパイプラインの実行の状態を確認できます。 一覧を更新するには、 **[最新の情報に更新]** ボタンを選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルの Azure Data Factory パイプラインでは、SQL Database Edge インスタンス上のテーブルから Azure Blob Storage 内のある場所に 1 時間に 1 回の頻度でデータがコピーされます。 より多くのシナリオで Data Factory を使用する方法については、これらの[チュートリアル](../data-factory/tutorial-copy-data-portal.md)を参照してください。
