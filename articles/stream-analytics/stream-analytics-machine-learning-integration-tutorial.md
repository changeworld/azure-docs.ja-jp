<properties 
	pageTitle="チュートリアル: Azure Stream Analytics と Azure Machine Learning の統合 | Microsoft Azure" 
	description="Stream Analytics ジョブで UDF と Machine Learning を利用する方法"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="12/30/2015" 
	ms.author="jeffstok"
/>

# チュートリアル: Stream Analytics と Machine Learning の統合の概要 #

このチュートリアルは、単純な Stream Analytics ジョブを Machine Learning とすばやく統合できるように設計されています。ここでは、Cortana Analytics ギャラリーのセンチメント分析 Machine Learning モデルを利用して、ストリーミング テキスト データを分析し、リアルタイムでセンチメント スコアを決定します。ストリーミング Twitter データに関するリアルタイムのセンチメント分析、サポート スタッフによるユーザー チャット レコード分析、フォーラム/ブログ/ビデオに対するコメントなどのリアルタイムの予測スコアリング シナリオを理解するには、このチュートリアルがお勧めです。
  
このチュートリアルでは、テキストが入力されたサンプル CSV ファイル (下の図 1 を参照してください) が Azure BLOB ストアの入力として用意されています。このジョブは、BLOB ストアのサンプル テキスト データに対して、ユーザー定義関数 (UDF) としてセンチメント分析モデルを適用します。最終的な結果は、同じ Azure BLOB ストア内の別の CSV ファイルに出力されます。この構成の内容を以下の図 2 に示します。さらに現実的なシナリオとしては、この BLOB ストアの入力を Azure Event Hub 入力からのストリーミング Twitter データと置き換えることができます。また、集計センチメントの [Power BI](https://powerbi.microsoft.com/) リアルタイム視覚化を構築することもできます。この記事の今後の版には、このような拡張機能が追加される予定です。

図 1:

![Stream Analytics Machine Learning チュートリアル: 図 1](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)

図 2:

![Stream Analytics Machine Learning チュートリアル: 図 2](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)

## 前提条件

この記事の前提条件は次のとおりです。

1.	有効な Azure サブスクリプション
2.	ある程度のデータが入力された CSV ファイル。図 2 のファイルは、[GitHub](https://github.com/jeffstokes72/azure-stream-analytics-repository/blob/master/sampleinputs.csv) からダウンロードできますが、自分で作成することもできます。このチュートリアルは、ダウンロードしたファイルを使用している前提で説明しています。

概要としては次の手順を実行します。

1.	CSV の入力ファイルを Blob Storage にアップロードします
2.	Cortana Analytics ギャラリーのセンチメント分析モデルを Machine Learning ワークスペースに追加します
3.	このモデルを Web サービスとして Azure Machine Learning ワークスペースにデプロイします
4.	この Web サービスを、テキスト入力のセンチメントを決定する関数として呼び出す Stream Analytics ジョブを作成します
5.	Stream Analytics ジョブを開始し、出力を確認します 


## CSV の入力ファイルを Blob Storage にアップロードします

この手順では、概要で説明したものなど、任意の CSV ファイルを使用できます。ファイルをアップロードするには、[Azure ストレージ エクスプローラー](http://storageexplorer.com/)や Visual Studio だけでなく、カスタム コードも使用できます。このチュートリアルでは、Visual Studio の例を紹介しています。

1.	Azure を展開し、**[ストレージ]** を右クリックします。**[外部ストレージのアタッチ]** を選択し、**[アカウント名]** と **[アカウント キー]** を指定します。  

    ![Stream Analytics Machine Learning チュートリアル: サーバー エクスプローラー](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)

2.	アタッチしたストレージを展開し、**[BLOB コンテナーの作成]** を選択して論理名を指定します。賛成されたら、コンテナーをダブルクリックして内容を確認します (この時点では空です)。

    ![Stream Analytics Machine Learning チュートリアル: BLOB の作成](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)

3.	**[BLOB のアップロード]** アイコンをクリックして CSV ファイルをアップロードし、**ローカル ディスクからファイル**を選択します。

## Cortana Analytics ギャラリーからセンチメント分析モデルを追加する

1.	Cortana Analytics ギャラリーで、[予測センチメント分析モデル](https://gallery.cortanaanalytics.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1)をダウンロードします。  
2.	Studio で **[開く]** をクリックします。  

    ![Stream Analytics Machine Learning チュートリアル: Machine Learning Studio を開く](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)

3.	サインインしてワークスペースを開きます。最適な場所を選択します。
4.	Studio 下部にある **[実行]** をクリックします。  
5.	正常に実行されたら、**[Web サービスのデプロイ]** をクリックします。
6.	これで、センチメント分析モデルを使用できるようになりました。確認するには、**[テスト]** ボタンをクリックし、“I love Microsoft” などのテキストを入力します。結果として、次のような結果が表示されます。

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`

![Stream Analytics Machine Learning チュートリアル: 分析データ](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)

**Excel 2010 以前**のブックのリンクをクリックして、後で Stream Analytics ジョブを設定する際に必要になる API キーと URL を取得します(この手順は、別の Azure アカウントのワークスペースから Machine Learning モデルを利用する場合にのみ必要です。このチュートリアルでは、このシナリオに対応する場合を想定しています)。

![Stream Analytics Machine Learning チュートリアル: 分析実験](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-experiement.png)

以下のように、ダウンロードした Excel の Web サービス URL とアクセス キーを書き留めます。

![Stream Analytics Machine Learning チュートリアル: 概要](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)

## Machine Learning モデルを使用する Stream Analytics ジョブを作成します。

1.	[Microsoft Azure 管理ポータル](https://manage.windowsazure.com)に移動します。  
2.	**[新規]**、**[Data Services]**、**[Stream Analytics]**、**[簡易作成]** の順にクリックします。**[ジョブ名]**、ジョブに適した **[リージョン]** を入力し、**[地域の監視ストレージ アカウント]** を選択します。    
3.	ジョブが作成されたら、**[入力]** タブに移動し、**[入力の追加]** をクリックします。  

    ![Stream Analytics Machine Learning チュートリアル: Machine Learning 入力のデータ入力](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)

4.	**[入力の追加]** ウィザード ウィンドウの最初のページで、**[データ ストリーム]** を選択し、[次へ] をクリックします。2 ページ目で、入力として **[Blob Storage]** を選択し、**[次へ]** をクリックします。
5.	ウィザードの **[BLOB ストレージの設定]** ページで、データをアップロードしたときに定義したストレージ アカウント BLOB コンテナーを指定します。**[次へ]** をクリックします。**[イベントのシリアル化の形式]** として **[CSV]** を選択します。その他の **[シリアル化の設定]** の項目は既定値のままにします。**[OK]** をクリックします。  
6.	**[出力]** タブに移動し、**[出力の追加]** をクリックします。  

    ![Stream Analytics Machine Learning チュートリアル: 出力の追加](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)

7.	**[Blob Storage]** を選択し、コンテナーを除いて同じパラメーターを指定します。**[入力]** は、**CSV** ファイルがアップロードされた “test” というコンテナーから読み取るように構成されました。**[出力]** に “testoutput” と入力します。コンテナーは重複しない名前にする必要があります。また、そのコンテナーが存在していることを確認します。
8.	**[次へ]** をクリックし、出力の **[シリアル化の設定]** を構成します。入力と同様に、**[CSV]** を選択し、**[OK]** をクリックします。
9.	**[関数]** タブに移動し、**[Machine Learning 関数の追加]** をクリックします。  

    ![Stream Analytics Machine Learning チュートリアル: Machine Learning 関数の追加](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)

10.	**[Machine Learning Web サービス設定]** ページで、Machine Learning ワークスペース、Web サービス、既定のエンドポイントを確認します。このチュートリアルでは、URLを知っていてキーを持っている場合は、ワークスペースに合わせた Web サービス構成に慣れるように、手動で設定を適用します。エンドポイントの **[URL]** と **[API キー]** を指定します。次に、 **[OK]** をクリックします

    ![Stream Analytics Machine Learning チュートリアル: Machine Learning Web サービス](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)

11.	**[クエリ]** タブに移動し、次のようにクエリを変更します。

```
	WITH subquery AS (  
		SELECT text, sentiment(text) as result from input  
	)  
	  
	Select text, result.[Score]  
	Into output  
	From subquery  
```

12. **[保存]** をクリックしてクエリを保存します。    

## Stream Analytics ジョブを開始し、出力を確認します

1.	ジョブの下部にある **[開始]** をクリックします。 
2.	**[クエリの開始]** ダイアログで **[ユーザー設定時刻]** を選択し、CSV を Blob Storage にアップロードする前の時間を選択します。**[OK]** をクリックします。  

    ![Stream Analytics Machine Learning チュートリアル: カスタム時間](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)

3.	CSV ファイルのアップロード時に使用したツールを使用して、Blob Storage に移動します。このチュートリアルでは、Visual Studio を使用しました。
4.	ジョブが開始されて数分後に、出力コンテナーが作成され、CSV ファイルがアップロードされます。  
5.	ファイルをダブルクリックすると、既定の CSV エディターが開き、次のような内容が表示されます。  

    ![Stream Analytics Machine Learning チュートリアル: csv ビュー](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)

## まとめ

このチュートリアルでは、リアルタイムでストリーミング テキスト データを読み取り、そのデータに対してセンチメント分析を適用する Stream Analytics ジョブを作成しました。このすべての処理は、センチメント分析モデルを構築する場合の複雑な作業を心配することなく実行できます。これが Cortana Analytics スイートの利点の 1 つです。

Azure Machine Learning 関数に関連するメトリックも確認できます。**[監視]** タブをクリックします。関数に関連する 3 つのメトリックがあります。
  
- [関数要求] は、Machine Learning Web サービスに対する要求数を示します。  
- [関数イベント] は、要求のイベント数を示します。既定では、ML Web サービスに対する各要求に最大 1,000 イベントが含まれます。  
- [関数要求の失敗] は、Machine Learning Web サービスに対する要求が失敗した数を示します。  

    ![Stream Analytics Machine Learning チュートリアル: ML 監視ビュー](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)

<!---HONumber=AcomDC_0107_2016-->