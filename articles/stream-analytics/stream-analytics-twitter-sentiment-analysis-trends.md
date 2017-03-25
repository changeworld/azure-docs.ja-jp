---
title: "Stream Analytics でのリアルタイム Twitter 感情分析 | Microsoft Docs"
description: "リアルタイム Twitter センチメント分析で Stream Analytics を使用する方法について説明します。 イベントの生成からライブ ダッシュボード上でのデータ操作までの手順。"
keywords: "リアルタイム Twitter 傾向分析、センチメント分析、ソーシャル メディア分析、傾向分析の例"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 42068691-074b-4c3b-a527-acafa484fda2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/09/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 09860b34bf4b1664e8d82af0e049cfd1a2d8defa
ms.lasthandoff: 03/10/2017

---

# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure Stream Analytics でのリアルタイム Twitter 感情分析

Azure Event Hubs に Twitter イベントをリアルタイム入力することで、ソーシャル メディア分析のためのセンチメント分析ソリューションを構築する方法について説明します。 Azure Stream Analytics クエリを記述してデータを分析します。 後で精読するために結果を保存するか、ダッシュボードを利用し、[Power BI](https://powerbi.com/) で洞察をリアルタイム表示します。

ソーシャル メディア分析ツールは、組織がソーシャル メディアへの大量投稿に含まれるトレンディング トピック、重要なテーマや考え方を理解するのに役立ちます。 センチメント分析 (*意見マイニング*ともいう) ではソーシャル メディア分析ツールを使用して、製品やアイデアに対する考え方を特定します。 リアルタイム Twitter 傾向分析が優れた例となります。ハッシュタグ サブスクリプション モデルにより、特定のキーワードをリッスンし、フィードに基づいて感情分析を展開できます。

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>シナリオ: リアルタイムのソーシャル メディア感情分析

ニュース メディア Web サイトを持つ会社は、その閲覧者に直接関係するサイト コンテンツを際立たせることで、競合他社より優位に立つことに着目しています。 そのため、会社は、Twitter データのリアルタイム センチメント分析を実行することで、閲覧者に関連するトピックのソーシャル メディア分析を利用します。 特に、Twitter のトレンディング トピックをリアルタイムで特定するには、主要なトピックのツイートの量と感情に関するリアルタイム分析が必要です。 そのため、基本的に、このソーシャル メディア フィードに基づいたセンチメント分析エンジンが必要となります。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション
* Twitter アカウントと [OAuth アクセス トークン](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* GitHub の [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) ファイル
* 省略可能: [GitHub](https://aka.ms/azure-stream-analytics-twitterclient) の Twitter クライアントのソース コード

## <a name="create-an-event-hub-input"></a>イベント ハブへの入力の作成

サンプル アプリケーションでは、イベントを生成し、それらを Event Hubs インスタンス (略して、イベント ハブ) にプッシュします。 Service Bus Event Hubs は、Stream Analytics に推奨されるイベント取り込み方法です。 詳細については、[Azure Service Bus のドキュメント](/azure/service-bus/)を参照してください。

### <a name="use-the-following-steps-to-create-an-event-hub"></a>イベント ハブを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で、**[新規]**をクリックし、"*Event Hubs*" と入力します。検索結果から **[Event Hubs]** を選択します。 **[Create]**をクリックします。
2. イベント ハブの名前を指定し、リソース グループを作成します。 ここでは、それぞれ、`socialtwitter-eh` と `socialtwitter-rg` を指定しています。 アカウントをダッシュボードにピン留めするためのチェック ボックスをオンにし、**[作成]** ボタンをクリックします。
3. デプロイが完了したら、イベント ハブをクリックし、**[エンティティ]** の下の**[Event Hubs]** をクリックします。
4. **[+ Event Hub]** ボタンをクリックして、イベント ハブを作成します。 もう一度名前を指定し (ここでは `socialtwitter-eh`)、**[作成]** をクリックします。
5. イベント ハブへのアクセスを許可するには、共有アクセス ポリシーを作成する必要があります。 イベント ハブをクリックし、**[設定]** の下の **[共有アクセス ポリシー]** をクリックします。
6. **[共有アクセス ポリシー]** で、**[+ 追加]** をクリックして、**[管理]** アクセス許可を持つ新しいポリシーを作成します。 ポリシーの名前を入力し、**[管理]** をオンにし、**[作成]** をクリックします。
7. 新しいポリシーを作成したら、そのポリシーをクリックし、**[接続文字列 – 主キー]** エンティティのコピー ボタンをクリックします。 この接続文字列は、この演習の別の手順で必要です。 ダッシュボードに戻ります。

![共有アクセス ポリシー エンドポイント](./media/stream-analytics-twitter-sentiment-analysis-trends/keysandendpoints.png)

## <a name="configure-and-start-the-twitter-client-application"></a>Twitter クライアント アプリケーションの構成および開始

パラメーター化された一連のトピックに関するツイート イベントを収集するために、 [Twitter のストリーミング API](https://dev.twitter.com/streaming/overview) を介して Twitter データに接続するクライアント アプリケーションが提供されています。 [Sentiment140](http://help.sentiment140.com/) オープン ソース ツールを使用して、各ツイートにセンチメント値を代入します。

* 0 = 否定
* 2 = 中立
* 4 = 肯定

すると、ツイート イベントがイベント ハブにプッシュされます。  

### <a name="follow-these-steps-to-set-up-the-application"></a>アプリケーションを設定するには、次の手順に従います。

1. [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) をダウンロードして解凍します。
2. `TwitterWPFClient.exe` アプリケーションを実行し、Twitter API キーとシークレット、Twitter アクセス トークンとシークレット、および Azure イベント ハブ情報を入力します。 最後に、それに基づいて感情を決定するキーワードを定義します。 複数の単語を指定 (カンマを使用して複数の値を定義) した場合に、いずれかの単語と一致させるには、コントロールを [Match ANY (いずれかと一致)] に切り替える必要があります。

   [OAuth アクセス トークンを生成する手順](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

   トークンを生成するには、空のアプリケーションを作成する必要があります。  

3. TwitterWpfClient.exe.config 内の EventHubConnectionString 値と EventHubName 値を、イベント ハブの接続文字列と名前に置き換えます。 先ほどコピーした接続文字列は、イベント ハブの接続文字列と名前の両方を示しています。それぞれを適切なフィールドに入力するようにしてください。 たとえば、次の接続文字列の場合を考えてみましょう。  
   
   `Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub`
   
   TwitterClient.exe.config ファイルには、次の例のような設定が含まれている必要があります。
   
   ```
     add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
     add key="EventHubName" value="yourhub"
   ```
   
   EventHubName の値には "EntityPath=" というテキストが含まれて**いない**ことに注意してください。
   
   Twitter と Azure の接続情報の値は、クライアントに直接入力することもできます。 "EntityPath=" は使用されないという、同じロジックが適用されます。
   
   ![wpfclient](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

4. *省略可能:* 検索するキーワードを調整します。  既定として、このアプリケーションは、いくつかのゲームに関係するキーワードを検索します。  必要に応じて、TwitterWpfClient.exe.config の **twitter_keywords** の値を調整できます。
5. TwitterWpfClient.exe を実行し、緑のスタート ボタンをクリックしてソーシャル センチメントを収集します。 **CreatedAt**、**Topic**、**SentimentScore** の値が設定されたツイート イベントがイベント ハブに送信されていることがわかります。

## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

Twitter からのリアルタイムのツイート イベントのストリーミングが行われているため、リアルタイムでこれらのイベントを分析するための Stream Analytics ジョブを設定できます。

### <a name="provision-a-stream-analytics-job"></a>Stream Analytics のジョブの準備

[Azure Portal](https://portal.azure.com/) で、**[新規]** をクリックし、「**Stream Analytics**」と入力し、Stream Analytics タイルをクリックします。 次の値を指定し、**[作成]** をクリックします。

   * **[ジョブ名]**: ジョブ名を入力します。
   * **リソース グループ**: [Use Existing (既存のものを使用)] オプションから、この演習で先ほど作成したリソース グループを選択します。
   * **[ストレージ アカウント]**: このリージョン内で実行されているすべての Stream Analytics ジョブの監視データを格納するために使用する Azure ストレージ アカウントを選択します。 既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成することができます。   

![新しいジョブ](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

ジョブが作成されると、Azure Portal で開きます。

## <a name="specify-the-job-input"></a>ジョブの入力の指定

Stream Analytics ジョブで、[ジョブ トポロジ] のジョブ ウィンドウの中央にある **[入力]** をクリックし、**[追加]** をクリックします。 次に示す情報の入力を求められます。 ほとんどは既定値で十分ですが、以下については、入力された情報で定義されます。
  
   * **[入力のエイリアス]**: このジョブの入力のフレンドリ名を入力します (`TwitterStream` など)。 この名前は、後でクエリで使用します。
   * **[イベント ハブ名]**: イベント ハブの名前を選択します。
   * **[イベント ハブ ポリシー名]**: このチュートリアルで前に作成したイベント ハブのポリシーを選択します。

**[Create]** ボタンをクリックします。

## <a name="specify-job-query"></a>ジョブのクエリの指定

Stream Analytics は、変換を記述するための単純な宣言型のクエリのモデルをサポートします。 言語に関する詳細については、 [Azure Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)を参照してください。  このチュートリアルでは、Twitter データに対するいくつかのクエリを作成してテストすることができます。

トピック間のメンション数を比較するには、[タンブリング ウィンドウ](https://msdn.microsoft.com/library/azure/dn835055.aspx)を使用して、5 秒ごとにトピック別のメンション数を取得します。

コード エディターで、クエリを次のコードに変更し、**[保存]** をクリックします。

```
SELECT System.Timestamp as Time, Topic, COUNT(*)
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TUMBLINGWINDOW(s, 5), Topic
```   

このクエリでは、 **TIMESTAMP BY** キーワードを使用して、一時的な計算に使用されるペイロードのタイムスタンプ フィールドを指定しています。 このフィールドが指定されていない場合は、各イベントがイベント ハブに到着した時間を使用してウィンドウ化操作が実行されます。  詳細については、「[Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)」(Stream Analytics クエリ言語リファレンス) の「Arrival Time Vs Application Time」(到着時間とアプリケーション時間) を参照してください。

このクエリは、**System.Timestamp** プロパティを使用して、各期間の終わりのタイムスタンプにもアクセスします。

![クエリ ボックス](./media/stream-analytics-twitter-sentiment-analysis-trends/querybox.png)

## <a name="create-output-sink"></a>出力シンクの作成

イベント ストリーム、イベントを取り込むためのイベント ハブ入力、ストリームに対して変換を実行するためのクエリを定義したところで、最後に、ジョブの出力シンクを定義します。  ジョブ クエリから Azure BLOB ストレージに集計済みツイート イベントを書き込みます。  また、結果は、具体的なアプリケーションのニーズに応じて、Azure SQL Database、Azure テーブル ストレージ、またはイベント ハブにプッシュできます。

## <a name="specify-job-output"></a>ジョブの出力の指定

Stream Analytics ジョブで、**[ジョブ トポロジ]** の **[出力]** をクリックし、**[追加]** をクリックします。 ウィンドウで、次の値を入力するか選択します。
   
   * **[出力のエイリアス]**: このジョブの出力のフレンドリ名を入力します。 このデモでは、`Output` を使用します。
   * **シンク**: [BLOB ストレージ] を選択します。
   * **ストレージ アカウント**: [新しいストレージ アカウントを作成する] を選択します。
    * **ストレージ アカウント**: 新しいストレージ アカウントの名前を指定します (この例では `socialtwitter`)。
    * **コンテナー**: 新しいコンテナーの名前をしています (この例では `socialtwitter`)。

他のエントリは既定値のままにします。 最後に、**[作成]** をクリックします。

## <a name="start-the-job"></a>ジョブの開始

ジョブの入力、クエリ、および出力がすべて指定されたため、いつでも Stream Analytics ジョブを開始できます。

ジョブの概要ウィンドウで、ページの上部にある **[開始]** をクリックします。

開いたダイアログ ボックスで、 **[ジョブ開始時刻]** を選択し、ダイアログ ボックスの下部にある**チェック** ボタンをクリックします。 ジョブの状態が **[開始中]** に変わりすぐに **[実行中]** に変わります。

![ジョブを実行中](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>センチメント分析の出力表示

ジョブが実行され、リアルタイム Twitter ストリームが処理されたら、センチメント分析の出力方法を選択します。 [Azure Storage エクスプローラー](https://http://storageexplorer.com/)や [Azure エクスプローラー](http://www.cerebrata.com/products/azure-explorer/introduction)などのツールを使用して、ジョブの出力をリアルタイムに表示できます。 ここで、[Power BI](https://powerbi.com/) を使用して、次のスクリーン ショットのように、カスタマイズされたダッシュ ボードを含めて、アプリケーションを拡張できます。

![PowerBI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)

## <a name="another-query-of-interest-in-this-scenario"></a>このシナリオに関係する別のクエリ

このシナリオのために作成した別のサンプル クエリは、[スライディング ウィンドウ](https://msdn.microsoft.com/library/azure/dn835051.aspx)に基づいています。 トレンディング トピックを特定するには、一定期間にメンションのしきい値を超えるトピックを検索します。 このチュートリアルでは、直近の 5 秒間に 20 回を超えてメンションされたトピックをチェックします。

```
SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY SLIDINGWINDOW(s, 5), topic
HAVING COUNT(*) > 20
```

## <a name="table-of-the-field-headers"></a>フィールドのヘッダーの表

情報の完全な開示のため、この演習で使用できるフィールド ラベルを次の表に示します。 クエリ エディターで自由にお試しください。

JSON プロパティ | 定義
--- | ---
CreatedAt | ツイートが作成された時刻
Topic | 指定したキーワードと一致するトピック
SentimentScore | Sentiment140 のセンチメント スコア
Author | ツイートを送信した Twitter ハンドル
Text | ツイートの全文


## <a name="get-support"></a>サポートを受ける
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


