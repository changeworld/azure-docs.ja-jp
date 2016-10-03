<properties
	pageTitle="Azure Stream Analytics と Azure Machine Learning を使用したセンチメント分析 | Microsoft Azure"
	description="Stream Analytics ジョブで、ユーザー定義関数および Machine Learning を使用する方法"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"
/>


<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="08/08/2016" 
	ms.author="jeffstok"
/>

# Azure Stream Analytics と Azure Machine Learning を使用したセンチメント分析 #

この記事は、単純な Azure Stream Analytics ジョブを Azure Machine Learning とすばやく統合できるように設計されています。ここでは、Cortana Intelligence ギャラリーのセンチメント分析 Machine Learning モデルを利用して、ストリーミング テキスト データを分析し、リアルタイムでセンチメント スコアを決定します。ストリーミング Twitter データに関するリアルタイムのセンチメント分析、サポート スタッフと顧客とのチャット レコード分析、フォーラムやブログ、ビデオに対するコメントの評価などのシナリオに加えて、その他多くのリアルタイムの予測スコアリング シナリオを理解するには、この記事の情報が役立ちます。

この記事では、次の図に示すような、Azure Blob Storage への入力として、テキストを含む CSV ファイルの例を提供します。このジョブは、BLOB ストアのサンプル テキスト データに対して、ユーザー定義関数 (UDF) としてセンチメント分析モデルを適用します。最終的な結果は、同じ BLOB ストア内の別の CSV ファイルに出力されます。

![Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)

次の図に、この構成を示します。さらに現実的なシナリオとしては、Blob Storage を Azure Event Hub 入力からのストリーミング Twitter データに置き換えることができます。また、集計センチメントの [Microsoft Power BI](https://powerbi.microsoft.com/) リアルタイム視覚化を構築することもできます。

![Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)

## 前提条件

この記事で紹介されているタスクを完了するための前提条件は次のとおりです。

-	有効な Azure サブスクリプション
-	ある程度のデータが入力された CSV ファイル。図 1 に示すようなファイルを [GitHub](https://github.com/jeffstokes72/azure-stream-analytics-repository/blob/master/sampleinputs.csv) からダウンロードすることも、独自のファイルを作成することもできます。この記事では、GitHub のダウンロードで提供されるファイルを使用することを前提としています。

大まかに言えば、この記事で説明するタスクを完了するには、以下の手順を実行します。

1.	CSV の入力ファイルを Azure Blob Storage にアップロードします。
2.	Cortana Intelligence ギャラリーのセンチメント分析モデルを Azure Machine Learning ワークスペースに追加します。
3.	このモデルを Web サービスとして Machine Learning ワークスペースにデプロイします。
4.	この Web サービスを、テキスト入力のセンチメントを決定する関数として呼び出す Stream Analytics ジョブを作成します。
5.	Stream Analytics ジョブを開始し、出力を確認します。

## CSV の入力ファイルを Blob Storage にアップロードする

この手順では、GitHub からダウンロードできるものとして既に指定されているファイルなど、すべての CSV ファイルを使用できます。[Azure ストレージ エクスプローラー](http://storageexplorer.com/)や Visual Studio を使用してファイルをアップロードすることも、カスタム コードも使用することもできます。Visual Studio に基づく例を使用します。

1.	Visual Studio で、**[Azure]**、**[ストレージ]**、**[Attach External Storage (外部ストレージのアタッチ)]** の順にクリックします。**[アカウント名]** と **[アカウント キー]** を入力します。

    ![Stream Analytics Machine Learning, サーバー エクスプローラー](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)

2.	手順 1 でアタッチしたストレージを展開し、**[BLOB コンテナーの作成]** を選択して論理名を入力します。コンテナーを作成した後に、開いてその内容を表示します。(この時点では空です)。

    ![Stream Analytics Machine Learning, BLOB の作成](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)

3.	CSV ファイルをアップロードするために、**[Upload Blob (BLOB のアップロード)]** をクリックし、**ローカル ディスクのファイル** をクリックします。

## Cortana Intelligence ギャラリーからセンチメント分析モデルを追加する

1.	Cortana Intelligence ギャラリーから[予測センチメント分析モデル](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1)をダウンロードします。
2.	Machine Learning Studio で **[Studio で開く]** をクリックします。

    ![Stream Analytics Machine Learning, Machine Learning Studio を開く](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)

3.	サインインしてワークスペースを開きます。最適な場所を選択します。
4.	ページの下部にある **[実行]** をクリックします。
5.	プロセスが正常に実行されたら、**[Web サービスのデプロイ]** をクリックします。
6.	センチメント分析モデルを使用できるようになりました。検証するには、**[テスト]** ボタンをクリックし、「大好き Microsoft。」などのテキスト入力を行います。 テストにより、次のような応答が返されます。

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`

![Stream Analytics Machine Learning, 分析データ](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)

**[アプリ]** 列で、**Excel 2010 以前のブック**のリンクをクリックして、後で Stream Analytics ジョブを設定する際に必要になる API キーと URL を取得します(この手順は、別の Azure アカウントのワークスペースから Machine Learning モデルを利用する場合にのみ必要です。この記事では、このシナリオに対応する場合を想定しています)。

以下のように、ダウンロードした Excel ファイルの Web サービス URL とアクセス キーを書き留めます。

![Stream Analytics Machine Learning, 概要](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)

## Machine Learning モデルを使用する Stream Analytics ジョブを作成する

1.	[Azure ポータル](https://manage.windowsazure.com)にアクセスします。
2.	**[新規]**、**[データ サービス]**、**[Stream Analytics]**、**[簡易作成]** の順にクリックします。**[ジョブ名]** にジョブの名前を入力し、**[リージョン]** にジョブの適切なリージョンを入力して、**[地域の監視ストレージ アカウント]** でアカウントを選択します。
3.	ジョブが作成された後、**[入力]** タブで **[入力の追加]** をクリックします。

    ![Stream Analytics Machine Learning, Machine Learning 入力の追加](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)

4.	**[入力の追加]** ウィザードの最初のページで、**[データ ストリーム]** をクリックし、**[次へ]** をクリックします。次のページで、入力として **[BLOB 記憶域]** をクリックして、**[次へ]** をクリックします。
5.	ウィザードの **[BLOB ストレージの設定]** ページで、データをアップロードしたときに定義したストレージ アカウント BLOB コンテナーを指定します。**[次へ]** をクリックします。**[イベントのシリアル化の形式]** として **[CSV]** をクリックします。**[シリアル化の設定]** ページのその他の項目は既定値のままにします。**[OK]** をクリックします。
6.	**[出力]** タブで **[出力の追加]** をクリックします。

    ![Stream Analytics Machine Learning, 出力の追加](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)

7.	**[BLOB 記憶域]** をクリックして、コンテナーを除き同じパラメーターを入力します。**[入力]** の値は、**CSV** ファイルがアップロードされた “test” というコンテナーから読み取るように構成されました。**[出力]** に「testoutput」と入力します。コンテナーの名前は別々の名前にする必要があります。このコンテナーが存在することを確認します。
8.	**[次へ]** をクリックし、出力の **[シリアル化の設定]** を構成します。**[入力]** と同様に、**[CSV]** を選択し、**[OK]** をクリックします。
9.	**[関数]** タブで **[Machine Learning 関数の追加]** をクリックします。

    ![Stream Analytics Machine Learning, Machine Learning 関数の追加](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)

10.	**[Machine Learning Web サービス設定]** ページで、Machine Learning ワークスペース、Web サービス、既定のエンドポイントを確認します。この記事では、URL を知っていて API キーを持っている場合は、ワークスペースに合わせた Web サービス構成に慣れるように、手動で設定を適用します。エンドポイントの **[URL]** と **[API キー]** を入力します。**[OK]** をクリックします。

    ![Stream Analytics Machine Learning, Machine Learning Web サービス](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)

11.	**[クエリ]** タブで、次のようにクエリを変更します。

 ```
 	WITH subquery AS (  
 		SELECT text, sentiment(text) as result from input  
  	)  
 
 	Select text, result.[Scored Labels]  
 	Into output  
 	From subquery  
 ```    
12.	**[保存]** をクリックしてクエリを保存します。

## Stream Analytics ジョブを開始し、出力を確認します

1.	ジョブのページの下部にある **[開始]** をクリックします。
2.	**[クエリの開始]** ダイアログで **[ユーザー設定時刻]** を選択し、CSV を Blob Storage にアップロードする前の時間を選択します。**[OK]** をクリックします。

    ![Stream Analytics Machine Learning, カスタム時間](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)

3.	CSV ファイルのアップロードに使用したツール (Visual Studio など) を使用して、Blob Storage に移動します。
4.	ジョブが開始されて数分後に、出力コンテナーが作成され、CSV ファイルがアップロードされます。
5.	CSV の既定のエディターでファイルを開きます。次のような内容が表示されます。

    ![Stream Analytics Machine Learning, CSV ビュー](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)

## まとめ

この記事では、ストリーミングのテキスト データを読み取り、そのデータにセンチメント分析をリアルタイム適用する Stream Analytics ジョブを作成する方法を示します。このすべての処理は、センチメント分析モデルを構築する場合の複雑な作業を心配することなく実行できます。これが Cortana Intelligence Suite の利点の 1 つです。

Azure Machine Learning 関数に関連するメトリックも表示できます。そのためには、**[監視]** タブをクリックします。関数に関連する 3 つのメトリックが表示されます。

- **[関数要求]** は、Machine Learning Web サービスに対して送信された要求数を示します。
- **[関数イベント]** は、要求内のイベントの数を示します。既定では、Machine Learning Web サービスへの各要求には、最大 1,000 件のイベントが含まれています。
- **[関数の要求に失敗しました]** は、Machine Learning Web サービスに対する要求が失敗した数を示します。

    ![Stream Analytics Machine Learning, Machine Learning モニター ビュー](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)

<!---HONumber=AcomDC_0921_2016-->