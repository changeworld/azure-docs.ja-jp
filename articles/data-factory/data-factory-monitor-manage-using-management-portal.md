<properties 
	pageTitle="Azure プレビュー ポータルを使用した Azure Data Factory の監視と管理" 
	description="Azure 管理ポータルを使用して、作成した Azure データ ファクトリを監視および管理する方法について説明します。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Azure プレビュー ポータルを使用した Azure Data Factory の監視
この記事では、Azure プレビュー ポータルを使用した Azure Data Factory の監視および管理に関するさまざまなシナリオについて説明します。

## <a name="AllDataFactories"></a>Azure サブスクリプション内のすべての Data Factory の表示

- [Azure プレビュー ポータル][azure-preview-portal]にサインインします。
- 左側にある **[参照]** ハブをクリックし、**[Data Factory]** をクリックします。  

	![BROWSE hub -> Data Factories][image-data-factory-browse-datafactories]

	**[Data Factory]** が表示されない場合は、**[参照]** ブレードの **[すべて]** をクリックしてから **[Data Factory]** をクリックします。

	![BROWSE hub -> Everything][image-data-factory-browse-everything]

- **[Data Factory]** ブレードにすべての Data Factory が表示されます。

	![Data factories blade][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Data Factory に関する詳細の表示

Data Factory の詳細を表示するには、次のいずれかを実行します。


- 上記の **[Data Factory]** ブレードで、いずれかのデータ ファクトリをクリックします。
- **スタート画面**でデータ ファクトリのリンクをクリックします。**スタート画面**は、Azure プレビュー ポータルにログインしたときに表示されるブレードです。データ ファクトリを作成する際に **[スタート画面に追加する]** (既定のオプション) を選択した場合は、次の図に示すように、スタート画面にデータ ファクトリが表示されます。この例では、**ADFTutorialDataFactory**、**ADFTutorialDataFactoryDF**、**LogProcessingFactory** のデータ ファクトリ リンクが**スタート画面**に表示されます。


![Data factory from the Startboard][image-data-factory-datafactory-from-startboard]

どちらの場合も、次の図に示すように、選択したデータ ファクトリの **[Data Factory]** ブレードが表示されます。

 ![Data Factory Home Page][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> データ ファクトリのダイアグラム ビューの表示
目的のデータ ファクトリの **[Data Factory]** ブレードで、**[ダイアグラム]** タイルをクリックすると、そのデータ ファクトリのダイアグラム ビューが表示されます。

![Data Factory Diagram View][image-data-factory-diagram-view]
 
### ダイアグラム ビューでパイプラインを開く
パイプラインのすべてのアクティビティを表示するには、ダイアグラム ビューでパイプラインを右クリックし、**[パイプラインを開く]** をクリックします。パイプライン内のアクティビティに加えて、アクティビティの入力データセットおよび出力データセットが表示されます。![Open pipeline](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

左上にある階層リンクで **[Data Factory]** をクリックして、ダイアグラム ビューに戻ります。ダイアグラム ビューには、すべてのパイプラインが表示されます。この例では 1 つのパイプラインのみ作成しました。

## <a name="DataFactoryLinkedServices"></a> データ ファクトリ内のリンクされたサービスの表示
目的のデータ ファクトリの **[Data Factory]** ブレードで、**[リンクされたサービス]** タイルを表示すると、すべてのリンクされたサービスが一覧表示されます。

![Linked Services Blade][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> リンクされたサービスに関する詳細の表示
**[リンクされたサービス]** ブレードで、一覧からリンクされたサービスをクリックすると、そのサービスに関する詳細が表示されます。

![Linked Service Blade][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> データ ファクトリ内のデータセットの表示 
目的のデータ ファクトリの **[Data Factory]** ブレードで、**[データセット]** タイルをクリックすると、そのデータ ファクトリのテーブルがすべて表示されます。

![Data Sets Blade][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a>データセットに関する詳細の表示
[データセット] ブレードのデータセットの一覧からデータセットをクリックすると、そのデータセットに関する詳細が表示されます。テーブルとはスキーマを持つ四角形のデータセットです。テーブルは、現時点でサポートされている唯一の種類のデータセットです。

![Table Blade][image-data-factory-table]

上記の **[テーブル]** ブレードの **[最近更新したスライス]** と **[最近失敗したスライス]** の一覧は、どちらも **[最終更新時刻]** で並べ替えられます。次の状況では、スライスの更新時刻が変更されます。

-  **Set-AzureDataFactorySliceStatus** を使用したり、スライスの **[スライス]** ブレードで **[実行]** をクリックしたりすることで、スライスの状態を手動で更新した場合。
-  スライスの実行 (実行の開始、実行の終了と失敗、実行の終了と成功など) により、スライスの状態が変わります。
 
	
代わりに、スライスの開始時刻と終了時刻で並べ替えられたデータ スライスを表示するには、**[データ スライス (スライスの時刻別)]** タイルをクリックします。
 
![Data Slices by Slice Time][DataSlicesBySliceTime]

一覧のタイトルをクリックするか、**[...] (省略記号)** をクリックすると、さらに多くのスライスが一覧表示されます。

![All Slices of a Table][image-data-factory-all-slices]

**[データ スライス]** ブレードで、**[フィルター]** ボタンをクリックして **[フィルター]** ブレードを表示すると、**[フィルター]** スライスに特定のスライスを表示して確認できます。スライスが**更新時刻で並べ替えられている** **[データ スライス]** ブレードで **[フィルター]** をクリックすると、次のブレードによく似たブレードが表示されます。

![Filter Blade][image-data-factory-filter-blade]

**[フィルター]** ブレードでは、**最後に更新された時刻**と**スライスの状態**に基づいてフィルター処理することができます。次の表は、スライスのすべてのステータスとその説明の一覧です。
 
スライスの状態 | 説明
------------ | ------------
PendingExecution | データ処理がまだ開始されていません。
InProgress | データ処理は進行中です。
Ready | データ処理が完了し、データ スライスの準備が整っています。
Failed | スライス生成の実行が失敗しました。
Skip | スライスの処理をスキップします。
Retry | スライス生成の実行を再試行しています。
Timed Out | スライスのデータ処理がタイムアウトになりました。
PendingValidation | データ スライスが処理前の検証ポリシーに対する検証を待機しています。
RetryValidation | スライスの検証を再試行しています。
FailedValidation | スライスの検証に失敗しました。
LongRetry | テーブル JSON で LongRetry が指定されている場合にスライスがこのステータスになります。スライスに対する通常の再試行は失敗します。
ValidationInProgress | (テーブル JSON で定義されているポリシーに基づいて) スライスの検証が実行されています。

スライスが**更新時刻で並べ替えられている** **[データ スライス]** ブレードで **[フィルター]** をクリックすると、さまざまな種類の **[フィルター]** ブレードが表示されます。

![Filter Blade 2][image-data-factory-filter-blade-2]


**[フィルター]** ブレードを起動すると、**[To]** フィールドは最近の時間 (概数) に自動的に設定され、返されるレコード数を制限します。**[From]** フィールドも自動的に設定されます。**[From]** の日付は、**[カレンダー]** ボタンをクリックして変更できます。**[To]** の日付は、**[From]** の日付を変更すると自動的に変更されます。

**[前へ]**/**[次へ]** ボタンをクリックすると、前の期間/次の期間のスライスを表示できます。**[前へ]** と **[次へ]** ボタンの時間範囲は、次の表に示すように、スライスの頻度と間隔に基づいて設定されます。

頻度 | 間隔の値の範囲 | 結果の時間範囲
----------| -------------------- | --------------------
分 | 1 ～ 4 | 6 時間
分 | 5 ～ 29 | 1 日
分 | 30 ～ 180 | 7 日
分 | 180 + | 28 日 (1 か月の概算日数)
時間 | 1 ～ 3 | 7 日
時間 | 4 ～ 11 | 28 日 (1 か月の概算日数)
時間 | 12 ～ 72 | 182 日 (6 か月の概算日数)
時間 | 73 + | 1 年
日 | 1 ～ 6 | 1 年
日 | 7 ～ 20 | 5 年
日 | 21 + | 10 年
週 | 1 ～ 3 | 5 年
週 | 4 + | 10 年
月 | 任意 | 10 年
 
たとえば、**頻度**を**時間**、**間隔**を **2** に定義した場合、**[次へ]**/**[前へ]** ボタンをクリックすると、時間範囲はいずれかの方向に **7 日**移動します。このロジックは、すべてのスライス/最近のスライス/問題のあるスライスの表示に関係なく、[フィルター] ブレードに適用されます。




## <a name="DataFactorySlice"></a>スライスに関する詳細の表示
**[テーブル]** ブレードまたは **[データ スライス]** ブレードのいずれかでスライス一覧のスライスをクリックすると、そのスライスに関する詳細が表示されます。

![Data Slice][image-data-factory-dataslice]

スライスが **[準備完了]** 状態でない場合、現在のスライスの実行をブロックしている準備完了でない上位スライスが、**[準備完了でない上位スライス]** の一覧に表示されます。

### <a name="DataFactoryActivtyRuns"></a>スライスに対するすべてのアクティビティの実行の表示
1 つのスライスに複数の実行がある場合があります。たとえば、スライスが失敗すると、サービスは数回再試行することがあります。また、すべての再試行が失敗したスライスを再実行することもできます。**[データ スライス]** ブレードの下部の一覧に、アクティビティのすべての実行が表示されます。

## <a name="DataFactoryActivtyRunDetails"></a>アクティビティの実行に関する詳細の表示
**[データ スライス]** ブレードの実行の一覧からアクティビティの実行をクリックすると、そのアクティビティの実行に関する詳細が表示されます。

![Activity Run Details][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a>操作レンズ - 過去 1 週間のイベント
目的のデータ ファクトリの **[Data Factory]** ブレード (またはホーム ページ) で、**[操作]** レンズの **[過去 1 週間のイベント]** をクリックすると、過去 1 週間のイベントが表示されます。これによって、過去 1 週間にその Data Factory で実行された操作の概要を確認できます。また、データの移動/処理で発生したエラーのトラブルシューティングにも役立ちます。

![Data Factory Events][image-data-factory-events]


## 関連項目

記事 | 説明
------ | ---------------
[PowerShell を使用した Azure Data Factory の監視と管理][monitor-manage-using-powershell] | この記事は、Azure PowerShell コマンドレットを使用して、Azure Data Factory を監視する方法について説明します。 


[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-filter-blade]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade.png

[image-data-factory-filter-blade-2]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade2.png


[image-data-factory-browse-everything]: ./media/data-factory-monitor-manage-using-management-portal/BrowseEverything.png

[image-data-factory-browse-datafactories]: ./media/data-factory-monitor-manage-using-management-portal/BrowseDataFactories.png

[image-data-factory-datafactories-blade]: ./media/data-factory-monitor-manage-using-management-portal/DataFactories.png

[image-data-factory-datafactory-from-startboard]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryFromStartboard.png

[image-data-factory-datafactory-home-page]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryHomePage.png

[image-data-factory-diagram-view]: ./media/data-factory-monitor-manage-using-management-portal/DiagramView.png

[image-data-factory-linked-services]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServicesBlade.png

[image-data-factory-linked-service]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServiceBlade.png

[image-data-factory-datasets]: ./media/data-factory-monitor-manage-using-management-portal/Datasets.png

[image-data-factory-table]: ./media/data-factory-monitor-manage-using-management-portal/Table.png

[image-data-factory-all-slices]: ./media/data-factory-monitor-manage-using-management-portal/AllSlices.png

[image-data-factory-filter]: ./media/data-factory-monitor-manage-using-management-portal/Filter.png

[image-data-factory-dataslice]: ./media/data-factory-monitor-manage-using-management-portal/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-monitor-manage-using-management-portal/ActivityRunDetails.png

[image-data-factory-events]: ./media/data-factory-monitor-manage-using-management-portal/Events.png
[DataSlicesBySliceTime]: ./media/data-factory-monitor-manage-using-management-portal/DataSlicesBySliceTime.png

<!---HONumber=July15_HO4-->