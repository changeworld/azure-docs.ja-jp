<properties 
	pageTitle="予測 - ETS + STL  | Azure" 
	description="予測 - ETS + STL" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="jaymathe"/>  

#予測 - ETS + STL  
 
 



この [Web サービス]( https://datamarket.azure.com/dataset/aml_labs/demand_forecast)は、季節的な傾向の分解 (STL) と指数平滑法モデル (ETS) を実装し、ユーザーが指定した履歴データに基づく予測を生成します。今年、特定の製品の需要が増加するでしょうか。クリスマス シーズンの製品販売を予測して、効果的な在庫計画を策定できるでしょうか。予測モデルは、このような質問に答えることができます。これらのモデルは、過去のデータを指定すると、隠れた傾向や季節性を検証し、将来の傾向を予測します。  
 
>この Web サービスは、モバイル アプリ、Web サイト、ローカル コンピューターなどからユーザーが使用できますが、この Web サービスのもう 1 つの目的は、Azure ML を使用して R コード上に Web サービスを作成する方法の例を示すことです。数行の R コードを記述し、Azure ML Studio 内でボタンを何回かクリックするだけで、R コードで実験を作成し、Web サービスとして発行できます。この Web サービスは Azure Marketplace に発行され、Web サービスの作成者がインフラストラクチャを設定することなく、世界中のユーザーやデバイスで使用されます。  
 
##Web サービスの使用 

このサービスは、4 つの引数を使用し、予測を計算します。
入力引数は、次のとおりです。

* Frequency: 生データの頻度 (毎日/毎週/毎月/毎四半期/毎年) を示します。
* Horizon: 将来の予測時間枠
* Date: 時間の新しい時系列データを追加します。
* Value: 新しい時系列データの値を追加します。

このサービスの出力は、計算された予測値です。
 
入力例は以下のとおりです。 

* Frequency: 12
* Horizon:  12
* Date:1/15/2012;2/15/2012;3/15/2012;4/15/2012;5/15/2012;6/15/2012;7/15/2012;8/15/2012;9/15/2012;10/15/2012;11/15/2012;12/15/2012;
1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013;
1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* Value:  3.479;3.68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511;
3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509

>Microsoft Azure Marketplace でホストされているこのサービスは、OData サービスです。これらは、POST または GET メソッドによって呼び出すことができます。 

自動でサービスを使用するための複数の方法があります ([ここ](http://microsoftazuremachinelearning.azurewebsites.net/StlEtsForecasting.aspx )にアプリケーション例があります)。

###Web サービスを使用する C# コードを開始します。

	public class Input{
	public double Recency;
	public double Frequency;
	public double Monetary;
	public double Time;
	public double Class;
	}

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
        System.Diagnostics.Debug.WriteLine("AuthenticationHeaderValue" + new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray)));
        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
       
	void Main()
	{
  	var input = new Input(){Recency =1, Frequency=0,Monetary=0,Time=1, Class= 0};
	var json = JsonConvert.SerializeObject(input);
	var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
       
  	var httpClient = new HttpClient();
   	httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
   	httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
  	var query = httpClient.PostAsync(acitionUri,new StringContent(json));
  	var result = query.Result.Content;
  	var scoreResult = result.ReadAsStringAsync().Result;
  	scoreResult.Dump();
	}

##Web サービスの作成 

>この Web サービスは、Azure ML を使用して作成されました。無料評価版の場合、実験を作成して[Web サービスを発行する](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/)入門ビデオに加えて、[azure.com/ml](http://azure.com/ml) も参照してください。Web サービスを作成した実験のスクリーン ショット、および実験内の各モジュールに対するコード例を以下に示します。

Azure ML 内で、新しい空白の実験が作成されました。入力データのサンプルは、事前定義済みのデータ スキーマにアップロードされました。データ スキーマには "R スクリプトの実行" モジュールがリンクされ、R の 'stl'、'ets' および 'forecast' 関数を使用して、季節的傾向分解 (STL) と指数平滑法 (ETS) の予測モデルを生成します。 

###実験フロー:

![Experiment flow][2]

####モジュール 1:
 
	# 次に示す形式のデータの CSV ファイルを追加する。 
![Sample data][3]	

####モジュール 2:

	# data input
	data <- maml.mapInputPort(1) # class: data.frame
	library(forecast)
	
	# preprocessing
	colnames(data) <- c("frequency", "horizon", "dates", "values")
	dates <- strsplit(data$dates, ";")[[1]]
	values <- strsplit(data$values, ";")[[1]]
	
	dates <- as.Date(dates, format = '%m/%d/%Y')
	values <- as.numeric(values)
	
	# fit a time-series model
	train_ts<- ts(values, frequency=data$frequency)
	fit1 <- stl(train_ts,  s.window="periodic")
	train_model <- forecast(fit1, h = data$horizon, method = 'ets')
	plot(train_model)
	
	# produce forcasting
	train_pred <- round(train_model$mean,2)
	data.forecast <- as.data.frame(t(train_pred))
	colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
	
	# data output
	maml.mapOutputPort("data.forecast");

##制限事項 

これは、ETS + STL 予測のきわめて簡単な例です。上のコード例からわかるように、エラーのキャッチは実装されず、このサービスは、すべての変数が連続した正の値で、頻度を 1 より大きい整数とする必要があります。日付と値のベクターの長さは同じで、時系列の長さは頻度の 2 倍よりも大きい必要があります。日付変数の形式は、'/mm/dd/yyyy' とします。

##FAQ
Web サービスの使用、または Marketplace への発行に関するよく寄せられる質問については、[ここ](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq)を参照してください。

[1]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img1.png
[2]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img2.png
[3]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img3.png

<!--HONumber=46--> 
 