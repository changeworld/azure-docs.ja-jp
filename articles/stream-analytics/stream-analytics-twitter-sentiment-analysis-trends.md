---
title: Stream Analytics でのリアルタイム Twitter センチメント分析 | Microsoft Docs
description: リアルタイム Twitter センチメント分析で Stream Analytics を使用する方法について説明します。 イベントの生成からライブ ダッシュボード上でのデータ操作までの手順。
keywords: リアルタイム Twitter 傾向分析、センチメント分析、ソーシャル メディア分析、傾向分析の例
services: stream-analytics
documentationcenter: ''
author: jeffstokes72
manager: jhubbard
editor: cgronlun

ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/26/2016
ms.author: jeffstok

---
# <a name="social-media-analysis:-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>ソーシャル メディア分析: Azure Stream Analytics での Twitter センチメントのリアルタイム分析
Azure Event Hubs に Twitter イベントをリアルタイム入力することで、ソーシャル メディア分析のためのセンチメント分析ソリューションを構築する方法について説明します。 Stream Analytics クエリを記述してデータを分析します。 後で精読するために結果を保存するか、ダッシュボードを利用し、[Power BI](https://powerbi.com/)で洞察をリアルタイム表示します。

ソーシャル メディア分析ツールは、組織がソーシャル メディアへの大量投稿に含まれるトレンディング トピック、重要なテーマや考え方を理解するのに役立ちます。 センチメント分析 (*意見マイニング*ともいう) ではソーシャル メディア分析ツールを使用して、製品やアイデアに対する考え方を特定します。 リアルタイム Twitter 傾向分析が優れた例となります。ハッシュタグ サブスクリプション モデルにより、特定のキーワードに耳を澄ませ、フィードに基づいてセンチメント分析を展開できます。

## <a name="scenario:-sentiment-analysis-in-real-time"></a>シナリオ: リアルタイムのセンチメント分析
ニュース メディア Web サイトを持つ会社は、その閲覧者に直接関係するサイト コンテンツを際立たせることで、競合他社より優位に立つことに着目しています。 そのため、会社は、Twitter データのリアルタイム センチメント分析を実行することで、閲覧者に関連するトピックのソーシャル メディア分析を利用します。 特に、Twitter のトレンディング トピックをリアルタイムで特定するには、主要なトピックのツイートの量とセンチメントに関するリアルタイム分析が必要です。 そのため、基本的に、このソーシャル メディア フィードに基づいたセンチメント分析エンジンが必要となります。

## <a name="prerequisites"></a>前提条件
* Twitter アカウントと [OAuth アクセス トークン](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* [TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) 
* 省略可能: [GitHub](https://aka.ms/azure-stream-analytics-twitterclient) の Twitter クライアントのソース コード

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>イベント ハブ入力とコンシューマー グループの作成
サンプル アプリケーションでは、イベントを生成し、それらを Event Hubs インスタンス (略して、イベント ハブ) にプッシュします。 Service Bus Event Hubs は、Stream Analytics に推奨されるイベント取り込み方法です。 Event Hubs のドキュメントについては、[Service Bus のドキュメント](/documentation/services/service-bus/)を参照してください。

イベント ハブを作成するには、次の手順に従います。

1. Azure ポータルで **[新規]**  >  **[APP SERVICES]** > **[SERVICE BUS]**  >  **[イベント ハブ]**  >  **[簡易作成]** をクリックし、名前、リージョン、新規または既存の名前空間を指定します。  
2. ベスト プラクティスとして、各 Stream Analytics ジョブは、単一の Event Hubs コンシューマー グループから読み取ります。 後で、コンシューマー グループを作成するプロセスを説明します。 コンシューマー グループの詳細については、「[Azure Event Hubs の概要](https://msdn.microsoft.com/library/azure/dn836025.aspx)」を参照してください。 コンシューマー グループを作成するには、新たに作成されたイベント ハブに移動し、**[コンシューマー グループ]** タブをクリックし、ページ下部の **[作成]** をクリックして、コンシューマー グループの名前を指定します。
3. イベント ハブへのアクセスを許可するには、共有アクセス ポリシーを作成する必要があります。 イベント ハブの **[構成]** タブをクリックします。
4. **[共有アクセス ポリシー]** で、**[管理]** アクセス許可を持つ新しいポリシーを作成します。
   
   ![管理アクセス許可のあるポリシーを作成できる [共有アクセス ポリシー]。](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)
5. ページの下部にある **[保存]** をクリックします。
6. **[ダッシュボード]** に移動し、ページ下部の **[接続情報]** をクリックし、接続情報をコピーして保存します  (検索アイコンの下のコピー アイコンを使用します)。

## <a name="configure-and-start-the-twitter-client-application"></a>Twitter クライアント アプリケーションの構成および開始
パラメーター化された一連のトピックに関するツイート イベントを収集するために、 [Twitter のストリーミング API](https://dev.twitter.com/streaming/overview) を介して Twitter データに接続するクライアント アプリケーションが提供されています。 [Sentiment140](http://help.sentiment140.com/) オープン ソース ツールを使用して、各ツイートにセンチメント値を代入します。

* 0 = 否定
* 2 = 中立
* 4 = 肯定

すると、ツイート イベントがイベント ハブにプッシュされます。  

アプリケーションを設定するには、次の手順に従います。

1. [TwitterClient ソリューションをダウンロード](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip)します。
2. TwitterClient.exe.config を開き、oauth_consumer_key、oauth_consumer_secret、oauth_token、oauth_token_secret を、自身の値を設定した Twitter トークンに置き換えます。  
   
   [OAuth アクセス トークンを生成する手順](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  
   
   トークンを生成するには、空のアプリケーションを作成する必要がある点に注意してください。  
3. TwitterClient.exe.config 内の EventHubConnectionString 値と EventHubName 値をイベント ハブの接続文字列と名前に置き換えます。 先ほどコピーした接続文字列は、イベント ハブの接続文字列と名前の両方を示しています。それぞれを適切なフィールドに入力するようにしてください。 たとえば、次の接続文字列の場合を考えてみましょう。
   
       Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub
   
   TwitterClient.exe.config ファイルには、次の例のような設定が含まれている必要があります。
   
       add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
       add key="EventHubName" value="yourhub"
   
   EventHubName の値には "EntityPath=" というテキストが含まれて__いない__ことに注意してください。
4. *省略可能:* 検索するキーワードを調整します。  既定で、このアプリケーションでは "Azure、Skype、XBox、Microsoft、シアトル" が検索されます。  必要な場合は、TwitterClient.exe.config の **twitter_keywords** の値を調整できます。
5. TwitterClient.exe を実行し、アプリケーションを起動します。 **CreatedAt**、**Topic**、**SentimentScore** の値が設定されたツイート イベントがイベント ハブに送信されていることがわかります。
   
   ![センチメント分析: イベント ハブに送信される SentimentScore 値。](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成
Twitter からのリアルタイムのツイート イベントのストリーミングが行われているため、リアルタイムでこれらのイベントを分析するための Stream Analytics ジョブを設定できます。

### <a name="provision-a-stream-analytics-job"></a>Stream Analytics のジョブの準備
1. [Azure ポータル](https://manage.windowsazure.com/)で、**[新規]**  >  **[データ サービス]**  >  **[STREAM ANALYTICS]**  >  **[簡易作成]** をクリックします。
2. 次の値を指定してから、 **[Stream Analytics ジョブの作成]**をクリックします。
   
   * **[ジョブ名]**: ジョブ名を入力します。
   * **[リージョン]**: ジョブを実行するリージョンを選択します。 パフォーマンスを向上させ、リージョン間のデータ転送が有料にならないように、同じリージョンにジョブとイベント ハブを配置することを検討します。
   * **[ストレージ アカウント]**: このリージョン内で実行されているすべての Stream Analytics ジョブの監視データを格納するために使用する Azure ストレージ アカウントを選択します。 既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成することができます。
3. Stream Analytics ジョブを一覧表示するには、左側のウィンドウにある **[Stream Analytics]** をクリックします。  
   ![Stream Analytics サービス アイコン](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)
   
   新しいジョブが **[作成済み]**の状態で表示されます。 ページの下部にある **[開始]** ボタンが無効になっていることがわかります。 ジョブを開始する前に、ジョブの入力、出力、クエリなどを構成する必要があります。

### <a name="specify-job-input"></a>ジョブの入力の指定
1. Stream Analytics ジョブで、ページ上部の **[入力]** をクリックしてから、**[入力の追加]** をクリックします。 表示されたダイアログ ボックスには、入力を設定する手順がいくつか表示されます。
2. **[データ ストリーム]**を選択し、右側のボタンをクリックします。
3. **[イベント ハブ]**を選択してから、右側のボタンをクリックします。
4. 3 ページ目で、次の値を入力または選択します。
   
   * **[入力のエイリアス]**: *TwitterStream* など、このジョブの入力のフレンドリ名を入力します。 後でクエリでこの名前を使用します。
     **[イベント ハブ]**: 作成したイベント ハブが Stream Analytics ジョブと同じサブスクリプションにある場合は、イベント ハブがある名前空間を選択します。
     
       イベント ハブが別のサブスクリプションにある場合、**[別のサブスクリプションのイベント ハブを使用]** をクリックしてから、**[Service Bus 名前空間]**、**[イベント ハブ名]**、**[イベント ハブ ポリシー名]**、**[イベント ハブ ポリシー キー]**、**[イベント ハブ パーティション数]** の情報を手動で入力します。
   * **[イベント ハブ名]**: イベント ハブの名前を選択します。
   * **[イベント ハブ ポリシー名]**: このチュートリアルで前に作成したイベント ハブのポリシーを選択します。
   * **[イベント ハブ コンシューマー グループ]**: このチュートリアルで前に作成したコンシューマー グループを入力します。
5. 右側のボタンをクリックします。
6. 次の値を指定します。
   
   * **[イベント シリアライザー形式]**: JSON
   * **[エンコード]**: UTF8
7. **チェック** ボタンをクリックしてこのソースを追加し、Stream Analytics がイベント ハブに正常に接続できることを確認します。

### <a name="specify-job-query"></a>ジョブのクエリの指定
Stream Analytics は、変換を記述するための単純な宣言型のクエリのモデルをサポートします。 言語に関する詳細については、 [Azure Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)を参照してください。  このチュートリアルでは、Twitter データに対するいくつかのクエリを作成してテストすることができます。

#### <a name="sample-data-input"></a>サンプル データの入力
実際のジョブ データに対するクエリを検証するには、**サンプル データ**機能を使用して、ストリームからイベントを抽出し、テスト用のイベントの .JSON ファイルを作成できます。

1. イベント ハブの入力を選択して、ページ下部の **[サンプル データ]** をクリックします。
2. 開いたダイアログ ボックスで、データ収集を開始する **[開始時間]** を指定し、使用する追加のデータ量に応じて **[期間]** を指定します。
3. **[詳細]** をクリックし、**ここをクリック** リンクをクリックして、生成された .JSON ファイルをダウンロードして保存します。

#### <a name="pass-through-query"></a>パススルー クエリ
最初に、イベント内のすべてのフィールドを示す単純なパススルー クエリを実行します。

1. Stream Analytics ジョブ ページの上部にある **[クエリ]** をクリックします。
2. コード エディターで、最初のクエリ テンプレートを次で置き換えます。
   
       SELECT * FROM TwitterStream
   
   入力ソースの名前が前に指定した入力の名前と必ず一致するようにします。
3. クエリ エディターの下の **[テスト]** をクリックします。
4. サンプル .JSON ファイルを参照します。
5. **チェック** ボタンをクリックして、クエリ定義の下の結果を確認します。
   
   ![クエリ定義の下に表示される結果](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic:-tumbling-window-with-aggregation"></a>トピック別のツイート数: 集計を含むタンブリング ウィンドウ
トピック間でメンション数を比較するには、[TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) を使用して、5 秒ごとにトピック別のメンション数を取得します。

1. コード エディターでクエリを次のように変更します。
   
       SELECT System.Timestamp as Time, Topic, COUNT(*)
       FROM TwitterStream TIMESTAMP BY CreatedAt
       GROUP BY TUMBLINGWINDOW(s, 5), Topic
   
   このクエリでは、 **TIMESTAMP BY** キーワードを使用して、一時的な計算に使用されるペイロードのタイムスタンプ フィールドを指定しています。 このフィールドが指定されていない場合は、各イベントがイベント ハブに到着した時間を使用してウィンドウ化操作が実行されます。  詳細については、「[Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)」(Stream Analytics クエリ言語リファレンス) で「Arrival Time Vs Application Time」(到着時間とアプリケーション時間の比較) を参照してください。
   
   このクエリは、**System.Timestamp** プロパティを使用して、各期間の終わりのタイムスタンプにもアクセスします。
2. クエリ エディターの下の **[再実行]** をクリックして、クエリの結果を表示します。

#### <a name="identify-trending-topics:-sliding-window"></a>トレンディング トピックの特定: スライディング ウィンドウ
トレンディング トピックを特定するには、一定期間にメンションのしきい値を超えるトピックを検索します。 このチュートリアルでは、[SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx) を使用して、直近の 5 秒間に 20 回を超えてメンションされたトピックをチェックします。

1. コード エディターでクエリを次のように変更します。SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions     FROM TwitterStream TIMESTAMP BY CreatedAt     GROUP BY SLIDINGWINDOW(s, 5), topic     HAVING COUNT(*) > 20
2. クエリ エディターの下の **[再実行]** をクリックして、クエリの結果を表示します。
   
   ![スライディング ウィンドウのクエリ出力](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment:-tumbling-window-with-aggregation"></a>メンションとセンチメントの数: 集計を含むタンブリング ウィンドウ
テストする最後のクエリでは、**TumblingWindow** を使用して、5 秒ごとに各トピックのメンション数と、センチメント スコアの平均値、最小値、最大値、標準偏差を取得します。

1. コード エディターでクエリを次のように変更します。
   
       SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
       Max(SentimentScore), STDEV(SentimentScore)
       FROM TwitterStream TIMESTAMP BY CreatedAt
       GROUP BY TUMBLINGWINDOW(s, 5), Topic
2. クエリ エディターの下の **[再実行]** をクリックして、クエリの結果を表示します。
3. これは、ダッシュボード用に使用されるクエリです。  ページの下部にある **[保存]** をクリックします。

## <a name="create-output-sink"></a>出力シンクの作成
イベント ストリーム、イベントを取り込むためのイベント ハブ入力、ストリームに対して変換を実行するためのクエリを定義したところで、最後に、ジョブの出力シンクを定義します。  ジョブ クエリから Azure BLOB ストレージに集計済みツイート イベントを書き込みます。  また、結果は、具体的なアプリケーションのニーズに応じて、Azure SQL Database、Azure テーブル ストレージ、またはイベント ハブにプッシュできます。

BLOB ストレージ用のコンテナーがまだない場合は、次の手順に従って作成します。

1. 既存のストレージ アカウントを使用するか、新しいストレージ アカウントを作成します。新しいストレージ アカウントを作成するには、**[新規]**  >  **[データ サービス]**  >  **[ストレージ]**  >  **[簡易作成]** の順にクリックして、画面の指示に従います。
2. ストレージ アカウントを選択し、ページ上部にある **[コンテナー]** をクリックし、**[追加]** をクリックします。
3. コンテナーの **[名前]** を指定し、その **[アクセス]** を**[パブリック BLOB]** に設定します。

## <a name="specify-job-output"></a>ジョブの出力の指定
1. Stream Analytics ジョブで、ページ上部の **[出力]** をクリックし、**[出力の追加]** をクリックします。 表示されたダイアログ ボックスには、出力を設定する手順がいくつか表示されます。
2. **[BLOB ストレージ]**を選択し、右側のボタンをクリックします。
3. 3 ページ目で、次の値を入力または選択します。
   
   * **[出力のエイリアス]**: このジョブの出力のフレンドリ名を入力します。
   * **[サブスクリプション]**: 作成した Blob Storage が Stream Analytics ジョブと同じサブスクリプションにある場合は、**[現在のサブスクリプションのストレージ アカウントを使用]** を選択します。 ストレージが別のサブスクリプションにある場合は、**[別のサブスクリプションのストレージ アカウントを使用]** を選択し、**[ストレージ アカウント]**、**[ストレージ アカウント キー]**、**[コンテナー]** の情報を手動で入力します。
   * **[ストレージ アカウント]**: ストレージ アカウントの名前を選択します。
   * **[コンテナー]**: コンテナーの名前を選択します。
   * **[ファイル名プレフィックス]**: BLOB 出力の書き込み時に使用するファイルのプレフィックスを入力します。
4. 右側のボタンをクリックします。
5. 次の値を指定します。
   * **[イベント シリアライザー形式]**: JSON
   * **[エンコード]**: UTF8
6. **チェック** ボタンをクリックしてこのソースを追加し、Stream Analytics からストレージ アカウントに正常に接続できることを確認します。

## <a name="start-job"></a>ジョブの開始
ジョブの入力、クエリ、および出力がすべて指定されたため、いつでも Stream Analytics ジョブを開始できます。

1. ジョブ **ダッシュボード**から、ページの下部にある **[開始]** をクリックします。
2. 開いたダイアログ ボックスで、 **[ジョブ開始時刻]** を選択し、ダイアログ ボックスの下部にある**チェック** ボタンをクリックします。 ジョブの状態が **[開始中]** に変わりすぐに **[実行中]** に変わります。

## <a name="view-output-for-sentiment-analysis"></a>センチメント分析の出力表示
ジョブが実行され、リアルタイム Twitter ストリームが処理されたら、センチメント分析の出力方法を選択します。 [Azure Storage エクスプローラー](https://azurestorageexplorer.codeplex.com/)や [Azure エクスプローラー](http://www.cerebrata.com/products/azure-explorer/introduction)などのツールを使用して、ジョブの出力をリアルタイムに表示できます。 ここで、[Power BI](https://powerbi.com/) を使用して、次のスクリーン ショットのように、カスタマイズされたダッシュ ボードを含めて、アプリケーションを拡張できます。

![ソーシャル メディア分析: Power BI ダッシュ ボードでの Stream Analytics センチメント分析 (意見マイニング) 出力。](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>サポートを受ける
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--HONumber=Oct16_HO2-->


