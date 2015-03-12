<properties 
	pageTitle="Azure プレビュー ポータルを使用した Azure Data Factory の監視と管理" 
	description="Azure の管理ポータルを使用して、作成した Azure データ ファクトリを監視および管理する方法について説明します。" 
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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Azure プレビュー ポータルを使用した Azure Data Factory の監視

- [Azure サブスクリプション内のすべての Data Factory の表示](#AllDataFactories)
- [Data Factory に関する詳細の表示](#DataFactoryDetails)
- [Data Factory のダイアグラム ビューの表示](#DataFactoryDiagram)
- [Data Factory 内のリンクされたサービスの表示](#DataFactoryLinkedServices)
- [リンクされたサービスに関する詳細の表示](#DataFactoryLinkedService) 
- [Data Factory 内のデータセットの表示](#DataFactoryDatasets)
- [データセットに関する詳細の表示](#DataFactoryDataset)
- [スライスに関する詳細の表示](#DataFactorySlice) 
- [スライスに対するすべてのアクティビティの実行の表示](#DataFactoryActivtyRuns) 
- [アクティビティの実行に関する詳細の表示](#DataFactoryActivtyRunDetails)
- [操作レンズ - 過去 1 週間のイベント](#EventsInThePastweek)  
   


## <a name="AllDataFactories"></a> Azure サブスクリプション内のすべての Data Factory の表示

- [Azure プレビュー ポータル][azure-preview-portal]にサインインします。
- 左側にある **[参照]** ハブをクリックし、**[Data Factory]** をクリックします。  

	![BROWSE hub -> Data Factories][image-data-factory-browse-datafactories]

	**[Data Factory]** が表示されない場合は、**[参照]** ブレードの **[すべて]** をクリックしてから **[Data Factory]** をクリックします。

	![BROWSE hub -> Everything] [image-data-factory-browse-everything]

- **[Data Factory]** ブレードにすべての Data Factory が表示されます。

	![Data factories blade][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Data Factory に関する詳細の表示

Data Factory の詳細を表示するには、次のいずれかを実行します。 


- 上記の **[Data Factory]** ブレードで、いずれかの Data Factory をクリックします。
- **スタート画面**で Data Factory のリンクをクリックします。**スタート画面**は、Azure プレビュー ポータルにログインしたときに表示されるブレードです。Data Factory を作成する際に **[スタート画面に追加する]** (既定のオプション) を選択した場合は、次の画像にあるように、スタート画面に Data Factory が表示されます。この例では、**ADFTutorialDataFactory**、**ADFTutorialDataFactoryDF**、および **LogProcessingFactory** の Data Factory リンクが**スタート画面**に表示されます。


![Data factory from the Startboard][image-data-factory-datafactory-from-startboard]

どちらの場合も、次の図に示すように、選択した Data Factory の **[DATA FACTORY]** ブレードが表示されます。 

 ![Data Factory Home Page][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Data Factory のダイアグラム ビューの表示
目的の Data Factory の **[DATA FACTORY]** ブレードで、**[ダイアグラム]** タイルをクリックすると、その Data Factory のダイアグラム ビューが表示されます。 

![Data Factory Diagram View][image-data-factory-diagram-view]
 

## <a name="DataFactoryLinkedServices"></a> Data Factory 内のリンクされたサービスの表示
目的の Data Factory の **[DATA FACTORY]** ブレードで、**[リンクされたサービス]** タイルを表示すると、すべてのリンクされたサービスが一覧表示されます。 

![Linked Services Blade][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> リンクされたサービスに関する詳細の表示
**[リンクされたサービス]** ブレードで、一覧からリンクされたサービスをクリックすると、そのサービスに関する詳細が表示されます。 

![Linked Service Blade][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Data Factory 内のデータセットの表示 
目的の Data Factory の **[DATA FACTORY]** ブレードで、**[データセット]** タイルをクリックすると、その Data Factory のテーブルがすべて表示されます。

![Data Sets Blade][image-data-factory-datasets] 

## <a name="DataFactoryDataset"></a>  データセットに関する詳細の表示
[データセット] ブレードのデータセットの一覧からデータセットをクリックすると、そのデータセットに関する詳細が表示されます。テーブルとはスキーマを持つ四角形のデータセットです。テーブルは、現時点でサポートされている唯一の種類のデータセットです。 

![Table Blade][image-data-factory-table]

上図の **[テーブル]** ブレードに、**[最近のスライス]** と **[問題のあるスライス]** が表示されます。**[...] (省略記号)** をクリックすると、すべてのスライスが表示されます。 

![All Slices of a Table][image-data-factory-all-slices]

**[データ スライス]** ブレードで、[フィルター] ボタンをクリックして [フィルター] ブレードを表示すると、**[フィルター]** スライスに特定のスライスを表示して確認できます。

![Filter Blade][image-data-factory-filter-blade]


**[フィルター]** ブレードを起動すると、**[To]** フィールドは最近の時間 (概数) に自動的に設定され、返されるレコード数を制限します。**[From]** フィールドも自動的に設定されます。**[From]** の日付は、**[カレンダー]** ボタンをクリックして変更できます。**[To]** の日付は、**[From]** の日付を変更すると自動的に変更されます。 

**[前へ]**/**[次へ]** ボタンをクリックすると、前の期間/次の期間のスライスを表示できます。**[前へ]** および **[次へ]** ボタンの時間範囲は、次の表に示すように、スライスの頻度と間隔に基づいて設定されます。

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
日 | 21+ | 10 年
週 | 1 ～ 3 | 5 年
週 | 4+ | 10 年
月 | 任意 | 10 年
 
たとえば、**頻度**を**時間**、**間隔**を **2** に定義した場合、**[次へ]**/**[前へ]** ボタンをクリックすると、時間範囲はいずれかの方向に **7 日**移動します。このロジックは、すべてのスライス/最近のスライス/問題のあるスライスの表示に関係なく、[フィルター] ブレードに適用されます。

**[フィルター]** ブレードを使用すると、スライスの**ステータス**に基づいてスライスをフィルターします。次の表は、スライスのすべてのステータスとその説明の一覧です。
 
スライスのステータス | 説明
------------ | ------------
PendingExecution | データ処理がまだ開始されていません。
InProgress | データ処理は進行中です。
Ready | データ処理が完了し、データ スライスの準備が整っています。
Failed | スライス生成の実行が失敗しました。
Skip | スライスの処理をスキップします。
Retry | スライス生成の実行を再試行しています。
Timed Out | スライスのデータ処理がタイムアウトになりました。
PendingValidation | データ スライスが処理前の検証ポリシーに対する検証を待機しています。
RetryValidation | スライスの検証を再試行します。
FailedValidation | スライスの検証に失敗しました。
LongRetry | テーブル JSON で LongRetry が指定されている場合にスライスがこのステータスになります。スライスに対する通常の再試行は失敗します。
ValidationInProgress | (テーブル JSON で定義されているポリシーに基づいて) スライスの検証が実行されています。



## <a name="DataFactorySlice"></a> スライスに関する詳細の表示
**[テーブル]** ブレードまたは **[データ スライス]** ブレードのいずれかでスライス一覧のスライスをクリックすると、そのスライスに関する詳細が表示されます。 

![Data Slice][image-data-factory-dataslice]


### <a name="DataFactoryActivtyRuns"></a> スライスに対するすべてのアクティビティの実行の表示
1 つのスライスに複数の実行がある場合があります。たとえば、スライスが失敗すると、サービスは数回再試行することがあります。また、すべての再試行が失敗したスライスを再実行することもできます。**[データ スライス]** ブレードの下部の一覧に、アクティビティのすべての実行が表示されます。 

## <a name="DataFactoryActivtyRunDetails"></a>  アクティビティの実行に関する詳細の表示
**[データ スライス]** ブレードの実行の一覧からアクティビティの実行をクリックすると、そのアクティビティの実行に関する詳細が表示されます。 

![Activity Run Details][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> 操作レンズ - 過去 1 週間のイベント
目的の Data Factory の **[DATA FACTORY]** ブレード (またはホーム ページ) で、**[操作]** レンズの **[過去 1 週間のイベント]** をクリックすると、過去 1 週間のイベントが表示されます。これによって、過去 1 週間にその Data Factory で実行された操作の概要を確認できます。また、データの移動/処理で発生したエラーのトラブルシューティングにも役立ちます。 

![ Data Factory Events][image-data-factory-events]


## 関連項目

記事 | 説明
------ | ---------------
[Azure PowerShell を使用した Azure Data Factory の監視と管理][monitor-manage-using-powershell] | この記事では、Azure PowerShell コマンドレットを使用して Azure Data Factory を監視する方法について説明しています。
[パイプラインが内部設置型のデータを扱えるようにする][use-onpremises-datasources] | この記事には、内部設置型の SQL Server データベースから Azure BLOB にデータをコピーする方法を説明したチュートリアルが記載されています。
[Data Factory で Pig と Hive を使用する][use-pig-and-hive-with-data-factory] | この記事には、HDInsight アクティビティを使用して hive/pig スクリプトを実行し、入力データを処理して出力データを生成する方法を説明したチュートリアルが記載されています。
[チュートリアル:Data Factory を使用してログ ファイルの移動と処理を行う][adf-tutorial] | この記事には、Azure Data Factory を使用してログ ファイルのデータを洞察へと変換する現実に近いシナリオの実行方法について、詳細なチュートリアルが記載されています。
[Azure Data Factory パイプラインでカスタム アクティビティを使用する][use-custom-activities] | この記事には、カスタム アクティビティを作成してパイプラインで使用する詳細な手順のチュートリアルが記載されています。
[Data Factory のトラブルシューティング][troubleshoot] | この記事では、Azure Data Factory の問題のトラブルシューティングを行う方法について説明しています。
[Azure Data Factory Developer Reference (Azure Data Factory 開発者向けリファレンス)][developer-reference] | この開発者向けリファレンスには、コマンドレット、JSON スクリプト、関数などを対象とした包括的なリファレンスが記載されています。 
[Azure Data Factory Cmdlet Reference (Azure Data Factory コマンドレット リファレンス)][cmdlet-reference] | このリファレンスには、すべての **Data Factory コマンドレット**に関する詳細が記載されています。


[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-filter-blade]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade.png

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

<!--HONumber=35.2-->

<!--HONumber=46--> 
