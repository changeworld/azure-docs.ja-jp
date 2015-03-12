<properties 
	pageTitle="比率検定での相違 | Azure" 
	description="比率検定での相違" 
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


#比率検定での相違




2 つの比率は統計的に異なりますか。ユーザーが 2 つのムービーを比較して、一方のムービーが他方のムービーと比べて "いいね" の比率が大幅に高いかどうかを判断したいとします。大規模なサンプルでは、比率 0.50 と比率 0.51 の違いは統計的にきわめて大きい可能性があります。一方、小規模なサンプルではその比率が実際に違いとなるかどうかを判断するには十分なデータと言えない可能性があります。 

この [Web サービス]( https://datamarket.azure.com/dataset/aml_labs/prop_test)は、ユーザーが入力した 2 つの比較グループに対する成功の数と試行回数の合計に基づいて、2 つの比率の仮説検定を実行します。シナリオでは、この Web サービスはムービー比較アプリから呼び出すことができ、ムービー評価に基づいて、あるムービーが他のムービーと比較して実際に "いいね" と思われている場合が多いかどうかをユーザーに示します。

>この Web サービスは、モバイル アプリ、Web サイト、ローカル コンピューターなどからユーザーが使用できますが、この Web サービスのもう 1 つの目的は、Azure ML を使用して R コード上に Web サービスを作成する方法の例を示すことです。数行の R コードを記述し、Azure ML Studio 内でボタンを何回かクリックするだけで、R コードで実験を作成し、Web サービスとして発行できます。この Web サービスは Azure Marketplace に発行され、Web サービスの作成者がインフラストラクチャを設定することなく、世界中のユーザーやデバイスで使用されます。


##Web サービスの使用

このサービスは 4 つの引数を使用し、比率の仮説検定を行います。

入力引数は、次のとおりです。

* Successes1: サンプル 1 での成功イベントの数
* Successes2: サンプル 2 での成功イベントの数
* Total1: サンプル 1 のサイズ
* Total2: サンプル 2 のサイズ

サービスの出力は、カイ二乗統計量、df、p 値、サンプル 1 と 2 の比率、および信頼区間限界を含む仮説検定の結果です。

>Microsoft Azure Marketplace でホストされているこのサービスは、OData サービスです。これらは、POST または GET メソッドによって呼び出すことができます。 

自動でサービスを使用するための複数の方法があります ([ここ](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx )にアプリケーション例があります)。

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

Azure ML 内で、2 つの "R スクリプトの実行" で新しい空白の実験が作成されました。1 番目のモジュールではデータ スキーマを定義し、2 番目のモジュールでは R 内で prop.test コマンドを使用して、2 つの比率に対して仮説検定を実行します。 


###実験フロー:

![Experiment flow][2]


####モジュール 1:
	####Schema definition  
	data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
	maml.mapOutputPort("data.set"); #send data to output port
	dataset1 = maml.mapInputPort(1) #read data from input port
	

####モジュール 2:

	test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

	result=data.frame(
	result=ifelse(test$p.value<0.05,"The proportions are different!",
	"The proportions aren't different statistically."),
	ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
	pvalue=round(test$p.value,4),
	proportion1=round(test$estimate[1],4),
	proportion2=round(test$estimate[2],4),
	confintlow=round(test$conf.int[1],4),
	confinthigh=round(test$conf.int[2],4)) 

	maml.mapOutputPort("result"); #output port
	

##制限事項 

これは、2 つの比率の相違をテストするきわめて簡単な例です。上のコード例からわかるように、エラーのキャッチは実装されません。サービスは、すべての変数が連続していると想定しています。

##FAQ
Web サービスの使用、または Marketplace への発行に関するよく寄せられる質問については、[ここ](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq)を参照してください。

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png

<!--HONumber=46--> 
