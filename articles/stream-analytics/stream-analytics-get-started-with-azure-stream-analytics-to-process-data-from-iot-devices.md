<properties
    pageTitle="Azure Stream Analytics の使用を開始して IoT デバイスからのデータを処理する | Microsoft Azure"
    description="IoT センサー タグと、Stream Analytics によるデータ ストリームとリアルタイムのデータ処理"
    keywords="IoT ソリューション, IoT の概要"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Azure Stream Analytics の使用を開始して IoT デバイスからのデータを処理する

このチュートリアルでは、モノのインターネット (IoT) デバイスからデータを収集するストリーム処理ロジックの作成方法について学習します。 実際のモノのインターネット (IoT) ユース ケースを使用して、迅速で経済的なソリューションを構築する方法を紹介します。

## <a name="prerequisites"></a>前提条件

-   [Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/)
-    [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>シナリオ

Contoso は工業オートメーションの領域で活動する会社で、自社の製造工程を完全に自動化しています。 この工場の機械には、リアルタイムでデータのストリームを生成することのできるセンサーがあります。 このシナリオにおいて、生産現場マネージャーは、センサー データからリアルタイムの詳細情報を取得し、パターンを見つけて、それらに対処したいと考えています。 センサー データに対して Stream Analytics クエリ言語 (SAQL) を使用し、データの受信ストリームから興味深いパターンを検出します。

ここに示すデータは、Texas Instruments 社のセンサー タグ デバイスから生成されています。

![Texas Instruments のセンサー タグ](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

データのペイロードは JSON 形式で、次のようになります。


    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  

実際のシナリオでは、何百ものこのようなセンサーがストリームとしてイベントを生成することになります。 ゲートウェイ デバイスがコードを実行し、これらのイベントを [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) または [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) にプッシュできれば理想的です。 Stream Analytics ジョブでそれらのイベントを Event Hubs から取り込み、そのストリームに対してリアルタイム分析クエリを実行することになると思われます。 結果はその後、いずれかの[サポートされている出力](stream-analytics-define-outputs.md)に送信することになります。

このガイドでは使いやすさを考えて、現実のセンサー タグ デバイスからキャプチャしたサンプル データ ファイルを用意しています。 このサンプル データに対してクエリを実行し、結果を確認できます。 以降のチュートリアルでは、各自のジョブを入力と出力に関連付け、それらを Azure サービスにデプロイする方法を学習します。

## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

1. [Azure Portal](http://manage.windowsazure.com) で **[STREAM ANALYTICS]** をクリックし、ページの左下隅にある **[新規]** をクリックして、新しい分析ジョブを作成します。

    ![Create a new Stream Analytics job](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

2. **[簡易作成]**をクリックします。

3. **[地域の監視ストレージ アカウント]** の設定で **[新しいストレージ アカウントの作成]** をクリックし、一意の名前を選択します。 このアカウントは、今後実行するすべてのジョブの監視情報を保存するために、Azure Stream Analytics によって使用されます。

    > [AZURE.NOTE] このストレージ アカウントは、1 リージョンあたり 1 回のみ作成します。 このストレージは、そのリージョン内に作成されるすべての Stream Analytics ジョブで共有されます。

4. ページ下部の **[Stream Analytics ジョブの作成]** をクリックします。

    ![Storage account configuration](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.jpg)

## <a name="azure-stream-analytics-query"></a>Azure Stream Analytics クエリ

**[クエリ]** タブをクリックし、クエリ エディターに移動します。 **[クエリ]** タブには、入力イベント データに対して変換を実行する T-SQL クエリが表示されます。

## <a name="archive-your-raw-data"></a>生データのアーカイブ

クエリの最も単純な形式は、すべての入力データを指定された出力にアーカイブするパススルー クエリです。

![Archive job query](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

ここでは、[GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) からサンプル データ ファイルをコンピューター内の場所にダウンロードします。 PassThrough.txt ファイルからクエリを貼り付けます。 **[テスト]** ボタンをクリックし、ダウンロードした場所から HelloWorldASA-InputStream.json というデータ ファイルを選択します。

![Test button in Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

![Test input stream](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

次のスクリーンショットに示すように、ブラウザーでクエリの結果を確認できます。

![Test results](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

## <a name="filter-the-data-based-on-a-condition"></a>条件に基づいたデータのフィルター処理

条件に基づいて結果をフィルター処理しましょう。 "sensorA" から取得したイベントの結果のみを表示したいとします。 クエリは Filtering.txt ファイルにあります。

![データ ストリームのフィルタリング](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

このクエリは文字列値を比較するものであるため、大文字小文字が区別されることに注意してください。 **[再実行]** ボタンをクリックして、クエリを実行します。 1,860 のイベントのうち、389 行だけが返されるはずです。

![Second output results from query test](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

## <a name="alert-to-trigger-a-business-workflow"></a>ビジネス ワークフローをトリガーするアラート

クエリについて、もう少し詳しく説明します。 あらゆる種類のセンサーを対象に、30 秒間隔で平均温度を監視し、平均温度が 100 度を超える場合にのみ結果を表示するのであれば、 次のクエリを記述し、**[再実行]** をクリックして、結果を確認します。 このクエリは ThresholdAlerting.txt ファイルにあります。

![30 秒間隔のフィルター クエリ](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

ご覧のように、結果に含まれるのは 245 行のみで、平均温度が 100 度を超えるセンサーの名前が一覧表示されます。 このクエリでは、センサー名である **dspl** 別に、30 秒の**タンブリング ウィンドウ**でイベントのストリームをグループ化しています。 一時的なクエリでは、時間の進み方を指定する必要があります。 ここでは、一時的な計算すべてに時間を関連付けるため、**TIMESTAMP BY** 句を使用して **OUTPUTTIME** 列を指定しました。 詳細については、MSDN の[時間管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)と[ウィンドウ関数](https://msdn.microsoft.com/library/azure/dn835019.aspx)に関する記事を参照してください。

![Temp over 100](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

## <a name="detect-absence-of-events"></a>Detect absence of events

入力イベントがないことを検出するためのクエリは、どのように記述すればよいのでしょうか。 センサーが最後にデータを送信してから 1 分間イベントを送信しなかったタイミングを見つけましょう。 このクエリは AbsenseOfEvent.txt ファイルにあります。

![Detect absence of events](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-12.png)

ここでは、同じデータ ストリームに対して **LEFT OUTER** JOIN を使用しています (自己結合)。 **INNER** JOIN では、一致が見つかった場合にのみ結果が返されます。  これに対して、**LEFT OUTER** JOIN では、結合の左側のイベントに一致するデータがない場合、その右側の列がすべて NULL となった行が返されます。 この手法は、イベントの欠落を見つけるためにきわめて便利です。 [JOIN](https://msdn.microsoft.com/library/azure/dn835026.aspx) の詳細については、MSDN ドキュメントを参照してください。

![結果の結合](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-13.png)

## <a name="conclusion"></a>まとめ

このチュートリアルの目的は、Stream Analytics クエリ言語を使ったさまざまなクエリを記述し、その結果をブラウザーで確認する方法を紹介することです。 ただし、これはほんの導入部に過ぎません。 Stream Analytics でできることは、まだまだたくさんあります。 Stream Analytics は多様な入出力に対応していることに加え、Azure Machine Learning の関数も利用できることから、データ ストリームを分析するうえで強力な手段となっています。 Stream Analytics についてもっと詳しく知りたい方は、まず [学習マップ](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/)を参照してください。 クエリの作成方法の詳細については、[一般的なクエリ パターン](./stream-analytics-stream-analytics-query-patterns.md)に関する記事を参照してください。



<!--HONumber=Oct16_HO2-->


