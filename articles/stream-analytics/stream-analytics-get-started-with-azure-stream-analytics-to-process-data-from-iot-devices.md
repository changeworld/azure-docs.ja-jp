<properties
	pageTitle="Azure Stream Analytics の使用を開始して IoT デバイスからのデータを処理する | Stream Analytics"
	description="IoT センサー タグと、Stream Analytics によるデータ ストリームとリアルタイムのデータ処理"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="05/03/2016"
	ms.author="jeffstok"
/>

# Azure Stream Analytics の使用を開始して IoT デバイスからのデータを処理する

このチュートリアルでは、モノのインターネット (IoT) デバイスからデータを収集するロジックを処理するストリームの作成について学習します。実際のモノのインターネット (IoT) ユース ケースを使用して、迅速で経済的なソリューションを構築する方法を紹介します。

## 前提条件

-   [Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/)
-   [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/GettingStarted) からダウンロード可能なサンプル クエリとデータ ファイル

## シナリオ

Contoso は工業オートメーション スペースの製造会社で、自社の製造工程を完全に自動化しています。この工場の機械には、リアルタイムでデータのストリームを生成するセンサーがあります。このシナリオにおいて、生産現場マネージャーは、センサー データからリアルタイムの詳細情報を取得し、パターンを見つけて、それらに対処したいと考えています。センサー データに対して Stream Analytics クエリ言語 (SAQL) を使用し、データの受信ストリームの興味深いパターンを検出します。

ここに示すデータは、Texas Instrument Sensor Tag デバイスから生成されています。

![Texas Instruments Sensor Tag](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

データのペイロードは、JSON 形式で、次のようになります。

    
	{
    	"time": "2016-01-26T20:47:53.0000000",  
	    "dspl": "sensorE",  
    	"temp": 123,  
	    "hmdt": 34  
	}  
    
実際のシナリオでは、何百ものこのようなセンサーがストリームとしてイベントを生成することになります。理想的には、これらのイベントを [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) にプッシュするいくつかのコードを実行するゲートウェイ デバイスを設置することがあります。Stream Analytics ジョブは Event Hubs からのこれらのイベントを使用し、クエリとして表されるリアルタイムの分析を実行し、必要な出力に結果を送信します。

この概要ガイドでは、さまざまなクエリを実行し、それらの結果を表示できる実際の SensorTag デバイスからキャプチャされたサンプル データ ファイルを用意しています。以降のチュートリアルでは、各自のジョブを入力と出力に関連付け、それらを Azure サービスにデプロイする方法を学習します。

## Stream Analytics ジョブの作成

[Azure ポータル](http://manage.windowsazure.com)で Stream Analytics を開き、ページの左下隅にある **[新規]** をクリックして、新しい分析ジョブを作成します。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

**[簡易作成]** をクリックします。

**[地域の監視ストレージ アカウント]** の設定で、**[新しいストレージ アカウントの作成]** を選択し、一意の名前を付けます。このアカウントは、今後実行するすべてのジョブの監視情報を保存するために、Azure Stream Analytics によって使用されます。

> [AZURE.NOTE] このストレージ アカウントはリージョンあたり 1 回だけ作成する必要があり、このストレージはそのリージョンで作成されたすべての Stream Analytics ジョブで共有されます。

ページ下部の **[Stream Analytics ジョブの作成]** をクリックします。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.jpg)

## Azure Stream Analytics クエリ

[クエリ] タブをクリックし、クエリ エディターに移動します。[クエリ] タブには、入力データに対して変換を実行する SQL クエリが表示されます。

## 生データのアーカイブ

クエリの最も単純な形式は、すべての入力データを指定された出力にアーカイブするパススルーです。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

ここで、[GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/GettingStarted) からサンプル データ ファイルをコンピューター内の場所にダウンロードします。**PassThrough.txt** ファイルからクエリをコピーして貼り付けます。下の [テスト] ボタンをクリックし、ダウンロードした場所から **HelloWorldASA InputStream.json** という名前のデータ ファイルを選択します。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

次のブラウザーでクエリの結果を確認できます。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

## 条件に基づいたデータのクレンジング

条件に基づいて結果をフィルター処理しましょう。"SensorA" から取得されるイベントの結果のみを表示したいと考えます。クエリは **Filtering.txt** ファイルにあります。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

ここでは文字列値を比較し、その大文字と小文字が区別されることに注意してください。**[再実行]**ボタンをクリックして、クエリを実行します。クエリは、1860 イベントのうち 389 行だけを返すはずです。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

## ビジネス ワークフローをトリガーするアラート

ここでクエリにもう少し工夫を加えてみましょう。すべてのセンサーの種類で、30 秒間隔で平均温度を監視し、平均温度が 100 度を超える場合にのみ結果を表示する場合、次のクエリを記述し、[再実行] をクリックして、結果を確認します。このクエリは **ThresholdAlerting.txt** ファイルにあります。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

ご覧のように、結果には平均温度が 100 度を超えるセンサーの 245 行のみが含まれます。このクエリでは、センサー名である dspl 別に、30 秒の**タンブリング ウィンドウ**でイベントをグループ化しています。このような一時的なクエリを実行する場合、時間の進め方を示すことが必要です。**TIMESTAMP BY** 句を使用して、すべての一時的な計算に、時間を進める方法として、"time" 列を指定しました。詳細については、MSDN の[時間管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)と[ウィンドウ](https://msdn.microsoft.com/library/azure/dn835019.aspx)に関するトピックを参照してください。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

## パターンの欠落の検出

パターンの欠落を見つけるクエリはどのように記述すればよいでしょうか。 たとえば、センサーが最後にデータを送信し、次の 1 分間イベントを送信しなかったタイミングを見つけましょう。このクエリは **AbsenseOfEvent.txt** ファイルにあります。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-12.png)

ここでは、同じデータ ストリームに対して **LEFT OUTER JOIN** を使用しています (自己結合)。内部結合では、一致が見つかった場合にのみ結果が返されます。ただし、**LEFT OUTER** 結合では、結合の左側からのイベントが一致していない場合、右の行のすべての列に対して NULL を含む行が返されます。この手法は、イベントの欠落を見つけるためにきわめて便利です。[JOIN](https://msdn.microsoft.com/library/azure/dn835026.aspx) の詳細については MSDN ドキュメントを参照してください。

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-13.png)

## まとめ

このチュートリアルでは、基本的にさまざまな SAQL クエリの作成を開始し、ブラウザーで、データのさまざまなパターンの結果を確認します。ただし、これはほんの導入部に過ぎません。Stream Analytics でできることはたくさんあります。次に学ぶことは、Stream Analytics ジョブを入力と出力に関連付け、それを Azure にデプロイすることです。[学習マップ](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/) ガイドを使用して、Stream Analytics の詳細を探究することができます。クエリの作成の詳細については、[一般的なクエリ パターン](./stream-analytics-stream-analytics-query-patterns.md#query-example-detect-the-absence-of-events)に関する記事をご覧ください。

<!-----HONumber=AcomDC_0504_2016-->