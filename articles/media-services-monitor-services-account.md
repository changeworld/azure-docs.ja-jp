<properties pageTitle="Media Services アカウントの監視 - Azure" description="Azure における Media Services アカウントの監視の構成方法について説明します。" services="media-services" documentationCenter="" authors="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako"/>





<h1><a id="monitormediaservicesaccount"></a>Media Services アカウントを監視する方法</h1>
Azure メディア サービスのダッシュボードには、使用状況のメトリックおよびアカウント情報が表示され、それをメディア サービス アカウントの管理に利用できます。

監視できるのは、キューに格納されたエンコード ジョブの数、失敗したエンコード タスク、エンコーダーからの入出力データによって表されるアクティブなエンコード ジョブ、およびメディア サービス アカウントに関連付けられた BLOB ストレージの使用状況です。さらに、顧客に対してコンテンツをストリーミング配信している場合は、さまざまなストリーミング メトリックも取得できます。データの監視期間は、過去 6 時間、24 時間、または 7 日間から選択できます。
 
>[AZURE.NOTE] Azure 管理ポータルでストレージ データを監視すると、それに応じて追加のコストがかかります。詳細については、[ストレージの分析と課金に関するページ](http://go.microsoft.com/fwlink/?LinkId=256667)を参照してください。

<h2><a id="configuremonitoring"></a>方法:Media Services アカウントを監視する</h2>

1. [管理ポータル](http://go.microsoft.com/fwlink/?LinkID=256666)で、**[メディア サービス]** をクリックし、目的の Media Services アカウント名をクリックしてダッシュボードを開きます。 

	![MediaServices_Dashboard][dashboard]

2. エンコード ジョブまたはデータを監視するには、Media Services に対するエンコード ジョブの送信を開始するか、Azure メディア オンデマンド ストリーミングを使用して顧客に対するコンテンツのストリーミング配信を開始します。約 1 時間後に、ダッシュボードに監視データが表示されるようになります。

<h2><a id="configuringstorage"></a>方法:BLOB ストレージの使用状況を監視する (省略可能)</h2>
1. **[概要]** で、**ストレージ アカウント**の名前をクリックします。
2. ストレージ アカウントのページで、**[構成ページ]** リンクをクリックして、BLOB、テーブル、キューの各サービスの **[監視]** 設定まで下図のようにスクロールします。

	>[AZURE.NOTE] BLOB は、Media Services で唯一サポートされるストレージの種類です。

	![StorageOptions][storage_options_scoped]

3. **[監視]** で、BLOB の監視レベルおよびデータ保有ポリシーを設定します。

-  監視レベルを設定するには、以下のいずれかを選択します。

      **[最小]** - 受信/送信、空き時間情報、遅延時間、成功のパーセンテージなどのメトリックを収集して、BLOB、テーブル、キューのサービスごとに集計します。

      **[詳細]** - 最小レベルのメトリックに加えて、Azure Storage サービス API のストレージ操作ごとに同じメトリックを収集します。詳細メトリックにより、アプリケーションの操作中に発生する問題を詳しく分析できます。 

      **[オフ]** - 監視しません。既存の監視データは、保有期間が経過するまで残ります。

- データ保有ポリシーを設定するには、**[保有期間 (日)]** ボックスに、データを保有する日数を 1 ～ 365 日の範囲で入力します。保有ポリシーを設定しない場合は、「0」(ゼロ) を入力します。保有ポリシーがない場合、監視データを削除する責任はユーザーが負います。使用しない古い分析データがコストをかけずに自動的に削除されるように、アカウントのストレージ分析データをどの程度の期間保持するかに基づいて、データ保有ポリシーを設定することをお勧めします。

4. 監視の構成が完了したら、**[保存]** をクリックします。
メディア サービスのメトリックと同様に、約 1 時間後に、ダッシュボードに監視データが表示されるようになります。
メトリックは、ストレージ アカウントの $MetricsTransactionsBlob、$MetricsTransactionsTable、$MetricsTransactionsQueue、$MetricsCapacityBlob という名前の 4 つのテーブルに保存されます。詳細については、[ストレージ分析のメトリックに関するページ](http://go.microsoft.com/fwlink/?LinkId=256668)を参照してください。


<!-- Images -->
[dashboard]: ./media/media-services-monitor-services-account/media-services-dashboard.png
[storage_options_scoped]: ./media/media-services-monitor-services-account/storagemonitoringoptions_scoped.png



<!--HONumber=42-->
