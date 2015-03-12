<properties 
	pageTitle="Azure Stream Analytics の使用 | Azure" 
	description="Azure Stream Analytics を使用して、Azure Service Bus Event Hub 内のイベントを処理して変換し、Azure SQL Database にその結果を格納します。" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="2/17/2015" 
	ms.author="jgao" />


# Azure Stream Analytics の使用

Azure Stream Analytics は、待機時間の短縮、高可用性、クラウド内のデータのストリーミング データに対する拡張性の高い複雑なイベント処理を実現する、十分に管理されたサービスです。詳細については、「[Azure Stream Analytics の概要][stream.analytics.introduction]」および「[Azure Stream Analytics documentation のドキュメント][stream.analytics.documentation]」を参照してください。

このチュートリアルでは、Stream Analytics を手軽に使い始められるように、[Azure Service Bus Event Hub][azure.event.hubs.documentation] からデバイスの温度の記録データを取得し、データを処理して、[Azure SQL データベース][azure.sql.database.documentation]に結果を出力する方法を示します。次の図は、入力を処理して出力するまでのイベントのフローを示しています。
  
![Azure Stream Analytics get started flow][img.get.started.flowchart]

##イベント ハブのサンプル データの生成
このチュートリアルでは、MSDN CodeGallery で提供されているコード サンプルの Service Bus Event Hubs Getting Started アプリケーションを利用して、新しいイベント ハブの作成、サンプル デバイスの温度の記録の生成、およびイベント ハブへのデバイスの記録データの送信を行います。

###Service Bus 名前空間の作成
サンプル アプリケーションは、既存の Service Bus 名前空間にイベント ハブを作成します。既にプロビジョニングした Service Bus 名前空間を使用することや、次の手順に従って新しい名前空間を作成することができます。

1.	[Azure の管理ポータル][azure.management.portal]にサインインします。
2.	Service Bus のページの下部の **[作成]** をクリックし、手順に従って名前空間を作成します。種類に「**メッセージング**」を使用します。
3.	新しく作成した名前空間をクリックしてから、ページの下部にある **[接続情報]** をクリックします。
4.	接続文字列をコピーします。この情報は後で使用します。

###Azure のストレージ アカウントの作成

このサンプル アプリケーションでは、アプリケーションの状態を保持するために、Azure のストレージ アカウントまたはストレージ エミュレーターが必要です。既存のストレージ アカウントを使用するか、次の手順に従って作成します。 

1.	ポータルで、**[新規]**、**[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックして新しいストレージ アカウントを作成し、指示に従って操作します。
2.	新しく作成したストレージ アカウントを選択し、ページの下部にある **[アクセス キーの管理]** をクリックします。
3.	ストレージ アカウント名と、いずれかのアクセス キーをコピーします。

###イベント ハブのサンプル データの生成

1.	[Service Bus Event Hubs Getting Started.zip](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Event-Hub-286fd097) をワークステーションにダウンロードして解凍します。
2.	Visual Studio で **EventHubSample.sln** ソリューション ファイルを開きます。
3.	**app.config** を開きます。
4.	Service Bus とストレージ アカウントの接続文字列の両方を指定します。キー名は、**Microsoft.ServiceBus.ConnectionString** および **AzureStorageConnectionString** です。ストレージ アカウントの接続文字列は、次の形式になります。 	

		DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<yourAccountKey>;
5.	ソリューションをビルドします。
6.	bin フォルダーのアプリケーションを実行します。使用方法は次のとおりです。 

		BasicEventHubSample <eventhubname> <NumberOfMessagesToSend> <NumberOfPartitions> 

	次の例では、**16** のパーティションを持つ **devicereadings** と呼ばれる新しいイベント ハブを作成し、**200** のイベントをこのイベント ハブに送信します。 

		BasicEventHubSample devicereadings 200 16

 	![insert image here][img.stream.analytics.event.hub.client.output] 
 	

###イベント ハブの共有アクセス ポリシーの作成
名前空間の内部すべてへの接続に使用できる Service Bus 名前空間の共有アクセス ポリシーが既に存在しますが、セキュリティに関するベスト プラクティスでは、イベント ハブ専用の別のポリシーを作成します。

1.	ポータルの Service Bus のワークスペースで、Service Bus 名前空間の名前をクリックします。
2.	ページの上部にある **[イベント ハブ]** をクリックします。
3.	このチュートリアルでは、イベント ハブ **[devicereadings]** をクリックします。
4.	ページの上部にある **[構成]** をクリックします。
5.	共有アクセス ポリシーで、**管理**のアクセス許可を持つ新しいポリシーを作成します。

	![][img.stream.analytics.event.hub.shared.access.policy.config]
6.	ページの下部にある **[保存]** をクリックします。
7.	イベント ハブが、Stream Analytics のジョブと異なるサブスクリプションにある場合、後で使用するために接続情報をコピーして保存する必要があります。そのためには、**[ダッシュボード]** をクリックしてから、ページの下部にある **[接続情報]** をクリックし、接続文字列を保存します。


##出力データを格納するための Azure SQL データベースの準備
Azure Stream Analytics は、Azure SQL データベース、Azure BLOB ストレージ、および Azure イベント ハブにデータを出力できます。このチュートリアルでは、Azure SQL データベースに出力するジョブを定義します。詳細については、「Microsoft Azure SQL データベースの概要」を参照してください。

###Azure SQL データベースの作成
このチュートリアルで使用する Azure SQL データベースが既にある場合は、このセクションをスキップしてください。

1.	管理ポータルで、**[新規]**、**[データ サービス]**、**[SQL データベース]**、**[簡易作成]** の順にクリックします。既存または新しい SQL データベース サーバーのデータベース名を指定します。
2.	新しく作成したデータベースを選択します。
3.	**[ダッシュボード]** をクリックして、ページの右側のペインにある **[接続文字列を表示する]** をクリックしてから、**ADO.NET** 接続文字列をコピーします。この情報は後で使用します。  
4.	サーバー レベルのファイアウォール設定で必ずデータベースに接続できるようにします。これは、[サーバーの構成] タブの下に新しい IP ルールを追加することで実行できます。動的な IP の処理方法など、詳細については、[http://msdn.microsoft.com/library/azure/ee621782.aspx](http://msdn.microsoft.com/library/azure/ee621782.aspx) を参照してください。

###出力テーブルの作成
1.	Visual Studio または SQL Server Management Studio を開きます。
2.	Azure SQL データベースに接続します。
3.	データベースに 2 つのテーブルを作成するには、次の T-SQL ステートメントを使用します。

		CREATE TABLE [dbo].[PassthroughReadings] (
		    [DeviceId]      BIGINT NULL,
			[Temperature] BIGINT    NULL
		);

		GO
		CREATE CLUSTERED INDEX [PassthroughReadings]
		    ON [dbo].[PassthroughReadings]([DeviceId] ASC);
		GO

		CREATE TABLE [dbo].[AvgReadings] (
		    [WinStartTime]   DATETIME2 (6) NULL,
		    [WinEndTime]     DATETIME2 (6) NULL,
		    [DeviceId]      BIGINT NULL,
			[AvgTemperature] FLOAT (53)    NULL,
			[EventCount] BIGINT null
		);
		
		GO
		CREATE CLUSTERED INDEX [AvgReadings]
		    ON [dbo].[AvgReadings]([DeviceId] ASC);

	>[WACOM.NOTE] データを挿入するために、クラスター化インデックスがすべての SQL データベース テーブルで必要です。
	   
##Stream Analytics のジョブの作成

Azure Service Bus のイベント ハブ、Azure SQL データベース、および出力テーブルを作成すると、Stream Analytics のジョブを作成する準備が整います。

###Stream Analytics のジョブの準備
1.	管理ポータルで、**[新規]**、**[データ サービス]**、**[Stream Analytics]**、**[簡易作成]** の順にクリックします。 
2.	次の値を指定してから、**[Stream Analytics のジョブの作成]** をクリックします。

	- **ジョブ名**: ジョブ名を入力します。たとえば、**DeviceTemperatures** にします。
	- **リージョン**: ジョブを実行するリージョンを選択します。Azure Stream Analytics は現在、プレビュー期間の 2 つのリージョンでのみ利用できます。詳細については、「[Azure Stream Analytics limitations and known issues (Azure Stream Analytics の制限事項と既知の問題)][stream.analytics.limitations]」を参照してください。パフォーマンスの確実な向上のために同じリージョンにジョブとイベント ハブを配置し、リージョン間のデータ転送が有料にならないように検討します。
	- **ストレージ アカウント**: このリージョン内で実行されているすべての Stream Analytics のジョブの監視データを格納するために使用するストレージ アカウントを選択します。既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成することができます。
	
3.	Stream Analytics のジョブの一覧を表示するには、左側のウィンドウにある **[Stream Analytics]** をクリックします。

	![][img.stream.analytics.portal.button]
 
	新しいジョブが **NOT STARTED** の状態で表示されます。ページの下部にある **[開始]** ボタンが無効であることに注意してください。ジョブを開始する前に、ジョブの入力、出力、クエリなどを構成する必要があります。 

###ジョブの入力の指定

1.	ジョブの名前をクリックします。
2.	このページの先頭の **[入力]** をクリックしてから、**[入力の追加]** をクリックします。表示されたダイアログ ボックスには、入力をセットアップする手順がいくつか表示されます。
3.	**[データ ストリーム]** を選択し、右側のボタンをクリックします。
4.	**[イベント ハブ]** を選択してから、右側のボタンをクリックします。
5.	3 ページ目で、次の値を入力または選択します。 

	- **入力のエイリアス**: このジョブの入力のフレンドリ名を入力します。後でクエリでこの名前を使用します。
	- **イベント ハブ**: 作成したイベント ハブが Stream Analytics のジョブと同じサブスクリプションにある場合は、イベント ハブがある名前空間を選択します。

		イベント ハブが他のサブスクリプションにある場合、**[別のサブスクリプションのイベント ハブを使用する]** を選択し、**SERVICE BUS 名前空間**、**イベント ハブ名**、**イベント ハブのポリシー名**、**イベント ハブのポリシー キー**、および**イベント ハブのパーティションの数**を手動で入力します。  

		>[WACOM.NOTE] このサンプルは、既定のパーティションの数の 16 を使用します。
		
	- **イベント ハブ名**: 作成した Azure のイベント ハブの名前を選択します。このチュートリアルでは **devicereadings** を使用します。
	- **イベント ハブ ポリシー名**: このチュートリアルで前に作成したイベント ハブのポリシーを選択します。
 
	![][img.stream.analytics.config.input]

6.	右側のボタンをクリックします。
7.	次の値を指定します。

	- **イベントのシリアル化形式:**: JSON
	- **エンコード**: UTF8

8.	チェック ボタンをクリックしてこのソースを追加し、Stream Analytics からイベント ハブに正常に接続できることを確認します。

###ジョブの出力の指定
1.	ページの上部にある **[出力]** をクリックしてから、**[出力の追加]** をクリックします。
2.	**[SQL データベース]** を選択し、右側のボタンをクリックします。
3.	次の値を入力または選択します。データベースの ADO.NET 接続文字列を使用して次のフィールドを入力します。

	- **SQL データベース**: このチュートリアルで前に作成した SQL データベースを選択します。同じサブスクリプションにある場合、ドロップダウン メニューからデータベースを選択します。同じサブスクリプションにない場合は、サーバー名とデータベースのフィールドに手動で入力してください。 
	- **ユーザー名**: SQL データベースのログイン名を入力します。
	- **パスワード**: SQL データベースのログイン パスワードを入力します。
	- **テーブル**: 出力の送信先のテーブルを指定します。ここでは、**PassthroughReadings** を使用します。

	![][img.stream.analytics.config.output]

4.	チェック ボタンをクリックして出力を作成し、指定したとおりに Stream Analytics が SQL データベースに正常に接続できることを確認します。

###ジョブのクエリの指定
Stream Analytics は、変換を記述するための単純な宣言型のクエリのモデルをサポートします。言語に関する詳細については、「Azure Stream Analytics のクエリ言語のリファレンス」を参照してください。  

このチュートリアルは、デバイスの温度の記録を SQL データベース テーブルに出力する簡単なパススルー クエリから始めます。

1.	ページの上部にある **[クエリ]** をクリックします。
2.	次の内容をコード エディターに追加します。

		SELECT DeviceId, Temperature FROM input
入力ソースの名前が前に指定した入力の名前と必ず一致するようにします。
3.	ページの下部にある **[保存]** をクリックし、**[はい]** をクリックして確定します。

##ジョブの開始
既定では、Stream Analytics のジョブは、開始されると受信イベントの読み取りを開始します。イベント ハブに処理対象の既存のデータが含まれているため、この履歴データを使用するジョブを構成する必要があります。  

1.	ページの上部にある **[構成]** をクリックします。
2.	**[出力の開始]** の値を **[ユーザー設定の時刻]** に変更して開始時刻を指定します。開始時刻は BasicEventHubSample を実行した時刻より前にしてください。  
3.	ページの下部にある **[保存]** をクリックし、**[はい]** をクリックして確定します。
3.	ページの上部にある **[ダッシュボード]**、ページの下部にある **[開始]** の順にクリックしてから、**[はい]** をクリックして確定します。**[概要]** ウィンドウの **[状態]** が **[開始中]** に変わります。開始プロセスが完了し、**[実行中]** 状態になるまで数分かかる場合があります。   


##ジョブの出力の表示

1.	Visual Studio または SQL Server Management Studio で、SQL データベースに接続して次のクエリを実行します。 

		SELECT * FROM PassthroughReadings

2.	イベント ハブから、記録イベントに対応するレコードが表示されます。   

	![][img.stream.analytics.job.output1]

	BasicEventHubSample アプリケーションを再実行して新しいイベントを生成し、SELECT * クエリを再実行して、リアルタイムで出力に反映されるのを参照することができます。
	
	存在しないか、予期しない出力による問題が発生した場合は、ダッシュボード ページの右側のウィンドウでリンクされているジョブの操作ログが表示されます。

##ジョブの停止、更新、および再開
ここで、より興味深いクエリをデータに対して実行しましょう。
1.	**[ダッシュボード]** または **[モニター]** ページで **[停止]** をクリックします。
2.	**[クエリ]** ページで、既存のクエリを次のクエリに置き換えて **[保存]** をクリックします。

		SELECT DateAdd(second,-5,System.TimeStamp) as WinStartTime, system.TimeStamp as WinEndTime, DeviceId, Avg(Temperature) as AvgTemperature, Count(*) as EventCount 
		FROM input
		GROUP BY TumblingWindow(second, 5), DeviceId

	この新しいクエリでは、イベントがイベント ハブにプッシュされた時間をタイムスタンプとして使用し、5 秒ごとの平均温度の記録と 5 秒の間に発生したイベントの数を検索して表示します。
3.	**[出力]** ページで、**[編集]** をクリックします。出力テーブルを PassthroughReadings から AvgReadings に変更し、チェック アイコンをクリックします。

4.	**[ダッシュボード]** ページで **[開始]** をクリックします。

##ジョブの出力の表示

1.	Visual Studio または SQL Server Management Studio で、SQL データベースに接続して次のクエリを実行します。

		SELECT * from AvgReadings

2.	各デバイスの平均温度とイベントの数を示す 5 秒間隔のレコードが表示されます。 

	![][img.stream.analytics.job.output2]
 
3.	 実行中のジョブによって処理されたイベントを表示し続けるには、BasicEventHubSample アプリケーションを再実行します。







##<a name="nextsteps"></a>次のステップ
このチュートリアルでは、Stream Analytics を使用して天候データを処理する方法を学習しました。詳細については、次の記事を参照してください。


- [Azure Stream Analytics の概要][stream.analytics.introduction]
- [Azure Stream Analytics 開発者ガイド][stream.analytics.developer.guide]
- [Azure Stream Analytics ジョブのスケーリング][stream.analytics.scale]
- [Azure Stream Analytics の制限事項と既知の問題][stream.analytics.limitations]
- [Azure Stream Analytics クエリ言語リファレンス][stream.analytics.query.language.reference]
- [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)][stream.analytics.rest.api.reference]




[img.stream.analytics.event.hub.client.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHClientOuput.png
[img.stream.analytics.event.hub.shared.access.policy.config]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHSharedAccessPolicyConfig.png
[img.stream.analytics.job.output2]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput2.png
[img.stream.analytics.job.output1]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput1.png
[img.stream.analytics.config.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureOutput.png
[img.stream.analytics.config.input]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureInput.png



[img.get.started.flowchart]: ./media/stream-analytics-get-started/StreamAnalytics.get.started.flowchart.png
[img.job.quick.create]: ./media/stream-analytics-get-started/StreamAnalytics.quick.create.png
[img.stream.analytics.portal.button]: ./media/stream-analytics-get-started/StreamAnalyticsPortalButton.png
[img.event.hub.policy.configure]: ./media/stream-analytics-get-started/StreamAnalytics.Event.Hub.policy.png
[img.create.table]: ./media/stream-analytics-get-started/StreamAnalytics.create.table.png
[img.stream.analytics.job.output]: ./media/stream-analytics-get-started/StreamAnalytics.job.output.png
[img.stream.analytics.operation.logs]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.png
[img.stream.analytics.operation.log.details]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.details.png


[azure.sql.database.firewall]: http://msdn.microsoft.com/library/azure/ee621782.aspx
[azure.event.hubs.documentation]: http://azure.microsoft.com/services/event-hubs/
[azure.sql.database.documentation]: http://azure.microsoft.com/services/sql-database/

[sql.database.introduction]: http://azure.microsoft.com/services/sql-database/
[event.hubs.introduction]: http://azure.microsoft.com/services/event-hubs/
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.sdk.net]: ../dotnet-sdk/

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.scale]: ../stream-analytics-scale-jobs/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093




[azure.management.portal]: https://manage.windowsazure.com


<!--HONumber=46--> 
