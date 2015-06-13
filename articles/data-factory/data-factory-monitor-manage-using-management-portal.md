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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Azure プレビュー ポータルを使用した Azure Data Factory の監視
この記事では、Azure プレビュー ポータルを使用して、監視し、Azure のデータのファクトリを管理するためのさまざまなシナリオについて説明します。

## <a name="AllDataFactories"></a> Azure サブスクリプション内のすべてのデータのファクトリを表示します。

- サインイン、 [Azure プレビュー ポータル][azure-preview-portal]です。
- クリックして **参照** ハブをクリックして左 **データ ファクトリ**です。  

	![ハブの参照データのファクトリを -> します。][image-data-factory-browse-datafactories]

	表示されない場合は、 **データ ファクトリ**, 、] をクリックして **すべて** 順にクリック **データ factorries** で、 **参照** ブレードします。

	![ハブの参照はすべて -> します。][image-data-factory-browse-everything]

- 内のすべてのデータ ファクトリが表示されます、 **データ ファクトリ** ブレードします。

	![データのファクトリ ブレード][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> データのファクトリに関する詳細を表示

Data Factory の詳細を表示するには、次のいずれかを実行します。


- 内のデータのファクトリをクリックして、 **データ ファクトリ** ブレードの前に示したします。
- データのファクトリのリンクをクリックして、 **スタート ボード**です。**スタート ボード** は、ブレードをするには、Azure プレビュー ポータルでのログインを参照してください。選択した場合 **スタート ボードに追加** 中 (既定のオプション) データのファクトリを作成するのには、データのファクトリが表示されます、次の図のようにスタート ボードにリンクします。この例で **ADFTutorialDataFactory**, 、**ADFTutorialDataFactoryDF** と **LogProcessingFactory** データ工場出荷時のリンク上にある、 **スタート ボード**です。


![スタート ボードからのデータのファクトリ][image-data-factory-datafactory-from-startboard]

どちらの方法が表示されます、 **データ ファクトリ** ブレードで、次の図のように、選択したデータのファクトリ。

 ![データの工場出荷時のホーム ページ][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> データの出荷時のダイアグラム ビューを表示します。
 **データ ファクトリ** データのファクトリに対するブレードをクリックして **ダイアグラム** タイルに、データのファクトリのダイアグラム ビューを参照してください。

![データの工場出荷時のダイアグラム ビュー][image-data-factory-diagram-view]
 
### ダイアグラム ビューを表示、パイプラインを開く
パイプラインのすべてのアクティビティを表示するには、ダイアグラム ビューで、パイプラインを右クリックして **開くパイプライン**です。アクティビティの入力呼び出し力のデータセットと、パイプライン内のアクティビティが表示されます。 ![開いているパイプライン](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

クリックして **データ ファクトリ** 、ダイアグラム ビューに戻るには、左上隅にある階層リンク内です。ダイアグラム ビューには、すべてのパイプラインが表示されます。この例では 1 つのパイプラインのみ作成しました。

## <a name="DataFactoryLinkedServices"></a> データのファクトリに関連付けられているサービスを表示します。
 **データ ファクトリ** データのファクトリに対するブレードをクリックして **関連付けられているサービス** タイルをリストに関連付けられているすべてのサービスを参照してください。

![関連付けられているサービスのブレード][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> リンクされているサービスに関する詳細を表示
 **関連付けられているサービス** ブレードで、これに関する詳細情報を表示する、一覧からサービスをリンクをクリックします。

![リンクされているサービスのブレード][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> データのファクトリでのデータセットのビュー 
 **データ ファクトリ** データのファクトリに対するブレードをクリックして **データセット** タイルに、データのファクトリ内のすべてのテーブルを参照してください。

![データ セットのブレード][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> データセットに関する詳細を表示
[データセット] ブレードのデータセットの一覧からデータセットをクリックすると、そのデータセットに関する詳細が表示されます。テーブルとはスキーマを持つ四角形のデータセットです。テーブルは、現時点でサポートされている唯一の種類のデータセットです。

![テーブルのブレード][image-data-factory-table]

 **テーブル** 上、両方のブレード **スライスを最近更新された** と **最近スライスが失敗しました** リストはによって並べ替えられます、 **最終更新時刻**です。次の状況では、スライスの更新時間が変更されます。

-  スライスのステータスは、手動で更新するなどを使用して、 **セット AzureDataFactorySliceStatus** (または) をクリックして **実行** 上、 **スライス** スライスのブレードします。
-  スライスは、実行のための状態を変更 (など、実行の開始、実行を終了し、失敗した、実行が成功したが終了したなど)。
 
	
代わりに、スライスの開始時刻から終了時刻によって並べ替えられたデータのスライスを表示する] をクリックして **(スライスの時刻) でのデータ スライス** 並べて表示します。
 
![データのスライスでスライスの時間][DataSlicesBySliceTime]

リストまたはのタイトルをクリックして **しています.(省略記号)** より大規模な一覧のスライスを参照してください。

![テーブルのすべてのスライス][image-data-factory-all-slices]

 **データ スライス** ブレードで] をクリックして、 **フィルター** 表示するときに、 **フィルター** できるブレード **フィルター** スライスすることを確認する特定のスライスを参照してください。クリックすると、次のいずれかのようなブレードが表示されます **フィルター** 上、 **データ スライス** スライスでブレード **更新時間によって並べ替えられます**です。

![ブレードをフィルター処理します。][image-data-factory-filter-blade]

 **フィルター** ブレードにはフィルター処理することができますに基づいて **最後に更新された時間** と **状態をスライス**です。次の表は、スライスのすべてのステータスとその説明の一覧です。
 
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

クリックすると **フィルター** 上で、 **データ スライス** ブレード スライスを使用 **スライス時間によって並べ替えられます**, 、別の種類が表示されます **フィルター** ブレードします。

![2 のブレードをフィルター処理します。][image-data-factory-filter-blade-2]


起動したときに、 **フィルター** ブレードで、 **に** フィールドが自動的に返されるレコードの数を制限するには、(切り上げ) の最新の時刻に設定します。 **から** フィールドはも自動的に設定します。変更することができます、 **から** 日付をクリックして、 **カレンダー** ボタンをクリックします。 **に** 日が変更されるは、変更すると自動的に、 **から** 日付。

クリックして **前**/* * 期間/次の期間を表示するボタン * * [次へは、以前のスライスします。時間範囲 **前** と **[次へ]** ボタンが基のスライスの頻度と間隔の次の表に示すように設定します。

頻度 | 間隔の値の範囲 | 結果として得られる時間のチャンク
----------| -------------------- | --------------------
1 分 | 1-4 | 6 時間
1 分 | 5 ～ 29 | 1 日
1 分 | 30 ～ 180 | 7 日間
1 分 | 180 + | 28 日 (おおよそですカレンダー月)。
1 時間 | 1-3 | 7 日間
1 時間 | 4-11 | 28 日 (おおよそですカレンダー月)。
1 時間 | 12 72 | 182 日 (おおよそです。6 か月)
1 時間 | 73 + | 1 年間
1 日 | 1 ～ 6 | 1 年間
1 日 | 7 ～ 20 | 5 年間
1 日 | 21 + | 10 年間
週 | 1-3 | 5 年間
週 | 4 + | 10 年間
月 | 任意 | 10 年間
 
定義する場合など **頻度** として **時間** と **間隔** の **2**, 、[、 **[次へ]**/* * ボタン * * 前、時間の範囲を移動する **7 日間** いずれかの方向です。このロジックは、すべてのスライス/最近のスライス/問題のあるスライスの表示に関係なく、[フィルター] ブレードに適用されます。




## <a name="DataFactorySlice"></a> スライスに関する詳細を表示
いずれかの一覧のスライスをクリックしてスライスで、 **テーブル** ブレードまたは **データ スライス** ブレードをそのスライスの詳細を参照してください。

![データ スライス][image-data-factory-dataslice]

スライスではない場合、 **準備ができて** 状態、準備ができていないし、現在のスライスでの実行をブロックしているアップ ストリームのスライスにわかり、 **準備ができていない上流のスライス** ] ボックスの一覧です。

### <a name="DataFactoryActivtyRuns"></a> ビューのスライスのすべての活動が実行されます。
1 つのスライスに複数の実行がある場合があります。たとえば、スライスが失敗すると、サービスは数回再試行することがあります。また、すべての再試行が失敗したスライスを再実行することもできます。活動が実行されるすべてが表示、* * 下部にある一覧でブレード データ スライス * *。

## <a name="DataFactoryActivtyRunDetails"></a> 実行アクティビティに関する詳細を表示
実行の実行の一覧からアクティビティをクリックして、 **データ スライス** ブレードを活動の実行に関する詳細を参照してください。

![アクティビティの実行の詳細][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> 操作のレンズで、過去 1 週間のイベント
 **データ ファクトリ** データのファクトリに対するブレード (またはホーム ページ) をクリックして **過去 1 週間のイベント** で **Operations** のレンズを過去 1 週間のイベントを参照してください。これによって、過去 1 週間にその Data Factory で実行された操作の概要を確認できます。また、データの移動/処理で発生したエラーのトラブルシューティングにも役立ちます。

![工場出荷時のデータ イベント][image-data-factory-events]


## 関連項目

記事 | 説明
------ | ---------------
[モニターと管理 Azure のデータを使用してファクトリ PowerShell][monitor-manage-using-powershell] | この記事は、Azure PowerShell コマンドレットを使用して、Azure のデータのファクトリを監視する方法について説明します。 


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

<!---HONumber=GIT-SubDir--> 