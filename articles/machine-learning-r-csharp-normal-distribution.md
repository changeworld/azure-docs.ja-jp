<properties 
	pageTitle="正規分布の Web サービス スイート | Azure" 
	description="正規分布の Web サービス スイート" 
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

#正規分布の Web サービス スイート



正規分布のスイートは、一連のサンプル Web サービス ([正規分布ジェネレーター]( https://datamarket.azure.com/dataset/aml_labs/ndg7)、[変位値計算]( https://datamarket.azure.com/dataset/aml_labs/ndq5)、[確率計算]( https://datamarket.azure.com/dataset/aml_labs/ndp5)) で、具体的には正規分布を生成し処理する 3 つのサービスです。これらのサービスにより、任意の長さの正規分布シーケンスの生成、与えられた確率からの変位値の計算、および与えられた変位値からの確率の計算ができます。それぞれのサービスは、選択したサービスに基づいて異なる結果を出力します (以下の説明を参照してください)。正規分布スイートは、R の統計パッケージに含まれている R 関数 qnorm、rnorm、および pnorm に基づいています。

>この Web サービスは、モバイル アプリ、Web サイト、ローカル コンピューターなどからユーザーが使用できますが、この Web サービスのもう 1 つの目的は、Azure ML を使用して R コード上に Web サービスを作成する方法の例を示すことです。数行の R コードを記述し、Azure ML Studio 内でボタンを何回かクリックするだけで、R コードで実験を作成し、Web サービスとして発行できます。この Web サービスは Azure Marketplace に発行され、Web サービスの作成者がインフラストラクチャを設定することなく、世界中のユーザーやデバイスで使用されます。  
 

##Web サービスの使用
正規分布スイートには、次の 3 つのサービスが含まれています。

###正規分布の変位値計算:
このサービスでは、正規分布の 4 つの引数を使用し、関連付けられている変位値を計算します。

入力引数は、次のとおりです。

* p - 正規分布のイベントの 1 つの確率 
* Mean - 正規分布の平均
* SD - 正規分布の標準偏差 
* Side - L は分布の下部、U は分布の上部

サービスの出力は計算された変位値で、指定された確率に関連付けられています。

###正規分布の確率計算:
このサービスでは、正規分布の 4 つの引数を使用し、関連する確率を計算します。

入力引数は、次のとおりです。

* q - 正規分布でのイベントの 1 つの変位値 
* Mean - 正規分布の平均
* SD - 正規分布の標準偏差 
* Side - L は分布の下部、U は分布の上部

サービスの出力は計算された確率で、指定された変位値に関連付けられています。

###正規分布ジェネレーター
このサービスは、正規分布の 3 つの引数を使用し、正規分布の数値のランダム シーケンスを生成します。 
要求には、次の引数を指定する必要があります。

* n - 観察数 
* mean - 正規分布の平均
* sd - 正規分布の標準偏差 

サービスの出力は、mean と sd の引数に基づいた正規分布での長さ n のシーケンスです。

>Microsoft Azure Marketplace でホストされているこのサービスは、OData サービスです。これらは、POST または GET メソッドによって呼び出すことができます。 

自動でサービスを使用するための複数の方法があります (アプリケーション例: [ジェネレーター](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx)、
[確率計算](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx)、
[変位値計算](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx))。

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
>この Web サービスは、Azure ML を使用して作成されました。無料評価版の場合、実験を作成して[Web サービスを発行する](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/)入門ビデオに加えて、[azure.com/ml](http://azure.com/ml) も参照してください。 

Web サービスを作成した実験のスクリーン ショット、および実験内の各モジュールに対するコード例を以下に示します。

###正規分布の変位値計算:
実験フロー
![Experiment flow][2]
 
	#data schema with example data (replaced with data from web service)
	data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
	} else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
	}
	q = qnorm(param$p,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	q = 2* param$mean - q
	} else if (param$side =='L') {
	q = q
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(q)
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###正規分布の確率計算:
実験フロー
![Experiment flow][3]
 
 	#data schema with example data (replaced with data from web service)
	data.set=data.frame(q=-1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	prob = pnorm(param$q,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	prob = 1 - prob
	} else if (param$side =='B') {
	prob = ifelse(prob<=0.5,prob * 2, 1)
	} else if (param$side =='L') {
	prob = prob
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(prob)
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###正規分布ジェネレーター:
実験フロー
![Experiment flow][4]

	#data schema with example data (replaced with data from web service)
	data.set=data.frame(n=50,mean=0,sd=1);
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	dist = rnorm(param$n,mean=param$mean,sd=param$sd)

	output = as.data.frame(t(dist))

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");

##制限事項 

これらは、正規分布周辺のきわめて単純な例です。上のコード例からわかるように、エラーのキャッチはほとんど実装されていません。

##FAQ
Web サービスの使用、または Marketplace への発行に関するよく寄せられる質問については、[ここ](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq)を参照してください。

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png

<!--HONumber=46--> 
