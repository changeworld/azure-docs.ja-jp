<properties 
	pageTitle="Machine Learning アプリケーション: 異常検出サービス | Microsoft Azure" 
	description="異常検出 API は、Microsoft Azure Machine Learning を使用して作成される例の 1 つで、時系列に従った一定の間隔での数値を含む時系列データの異常を検出します。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm"
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="reference" 
	ms.tgt_pltfrm="na" 
	ms.workload="multiple" 
	ms.date="07/28/2015" 
	ms.author="luisca"/>


# Machine Learning 異常検出サービス#

##概要

異常検出 API は、Azure Machine Learning を使用して作成される例の 1 つで、時系列に従った一定の間隔での数値を含む時系列データの異常を検出します。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

この異常検出サービスは、次のようなさまざまな種類の時系列データの異常を検出できます。

1. 正と負の値の傾向: たとえば、コンピューティングのメモリ使用量を監視する場合、上昇傾向はメモリ リークを示します。

2. 動的な範囲の値の増加: たとえば、サービスによってスローされる例外を監視する場合、動的な範囲の値の増加は、サービスの正常性が不安定になっていることを示す可能性があります。

3. スパイクと DIP: たとえば、サービスへのログインの失敗の数や、電子商取引サイトのチェックアウトの数を監視している場合、スパイクや DIP は異常な動作を示している可能性があります。


これらの検出は、時間の経過による値の変化を追跡し、値の継続的な変化を報告します。これらはアドホックなしきい値の調整を必要とせず、スコアを使用して誤検知率を制御できます。異常検出 API は、KPI、検索回数やクリック数などの使用状況のメトリック、メモリ、CPU、ファイルの読み取りなどのパフォーマンス カウンターを一定期間追跡することにより、サービスの監視のようないくつかのシナリオで役に立ちます。

##API の定義

サービスは、REST ベースの API を HTTPS 経由で提供しますが、これは Web アプリケーションやモバイル アプリケーション、R、Python、Excel などを含むさまざまな方法で使用できます。データの異常検出 Web サービスを実行し、結果を視覚化できる [Azure Web アプリケーション](http://anomalydetection-aml.azurewebsites.net/)を用意しています。

このサービスに対する時系列データを REST API 呼び出しによって送信することもでき、前述の 3 つの異常の種類の組み合わせを実行します。サービスは AzureML Machine Learning プラットフォーム上で動作し、ビジネス ニーズに合わせてサイズをシームレスに調整し、99.95% の SLA を提供しています。

以下の図は、前述のフレームワークを使用して時系列で検出される異常の例を示しています。時系列は、2 つの明確なレベルの変化と 3 つのスパイクがあります。赤い点はレベルの変化が検出された時を示し、赤い上矢印は検出されたスパイクを示しています。


![][1]

##入力

API は 2 つの入力パラメーターを取得します。

1. "data" は入力された時系列を t1=v1;t2=v2;... という形式で表します。 

 
	サンプル データ:
		
		"9/21/2014 11:05:00 AM=3;9/21/2014 11:10:00 AM=9.09;9/21/2014 11:15:00 AM=0;"

2. "params"は "SpikeDetector.TukeyThresh=3; SpikeDetector.ZscoreThresh=3" に設定され、スパイク探知機の感度を制御します。値が大きいとスパイクをより多くキャッチします。

	上記の入力パラメーターを使用したサンプル URL:

		https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v1/Score?data=%279%2F21%2F2014%2011%3A05%3A00%20AM%3D3%3B9%2F21%2F2014%2011%3A10%3A00%20AM%3D9.09%3B9%2F21%2F2014%2011%3A15%3A00%20AM%3D0%3B%27&params=%27SpikeDetector.TukeyThresh%3D3%3B%20SpikeDetector.ZscoreThresh%3D3%27



###出力###

API は時系列データでこれらの検出を実行し、時系列の各ポイントで異常のスコアを返します。この出力は、[https://adresultparser.codeplex.com/](https://adresultparser.codeplex.com/) で示すシンプルなパーサーを使用して解析できます。これは、API に接続し、出力を解析する方法を表すサンプル コードを示します。生成されるアラートは、ダッシュボード上で視覚化でき、処置を取ることができる人間の専門家に渡すことができます。

上記のサンプル入力のサンプル出力:

	"Time,Data,TSpike,ZSpike,Martingale values,Alert indicator,Martingale values(2),Alert indicator(2),;9/21/2014 11:05:00 AM,3,0,0,-0.687952590518378,0,-0.687952590518378,0,;9/21/2014 11:10:00 AM,9.09,0,0,-1.07030497733224,0,-0.884548154298423,0,;9/21/2014 11:15:00 AM,0,0,0,-1.05186237440962,0,-1.173800281031,0,;"

以下の表を示しています。

時刻|データ|Tspike|Zspike|マーティンゲイル値|アラート インジケーター|マーティンゲイル値 (2)|アラート インジケーター (2)
---|---|---|---|---|---|---|---
9/21/2014 11:05|3|0|0|-0.687952591|0|-0.687952591|0|   
9/21/2014 11:10|9\.09|0|0|-1.070304977|0|-0.884548154|0|    
9/21/2014 11:15|0|0|0|-1.051862374|0|-1.1738002814|0|   
   

[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection.jpg

 

 

<!---HONumber=Oct15_HO3-->