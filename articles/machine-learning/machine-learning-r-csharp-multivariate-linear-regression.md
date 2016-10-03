<properties 
	pageTitle="多変量線形回帰 | Microsoft Azure" 
	description="多変量線形回帰" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/14/2016" 
	ms.author="jaymathe"/>


#多変量線形回帰   
 

 
データセットを用意し、独立変数に基づいて各個人 (i) に対する従属変数 y を短時間で予測するとします。線形回帰は、このような予測に使用される一般的な統計手法です。ここでは、従属変数 y は連続値と見なされます。


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

単純なシナリオとしては、研究者が個人の身長 (x) に基づいてその体重 (y) を予測しようとする場合などがあります。より高度なシナリオであれば、個人の詳細情報 (体重、性別、人種など) からその体重を予測することもできます。この [Web サービス](https://datamarket.azure.com/dataset/aml_labs/multivariate_regression)は、データの線形回帰モデルに適合し、データ内の観測ごとに予測値 (y) を出力します。

>この Web サービスは、モバイル アプリ、Web サイト、ローカル コンピューターなどからユーザーが使用できます。この Web サービスのもう 1 つの目的は、Azure Machine Learning を使用して R コード上に Web サービスを作成する方法の例を示すことです。数行の R コードを記述し、Azure Machine Learning Studio 内でボタンを何回かクリックするだけで、R コードで実験を作成し、Web サービスとして発行できます。この Web サービスは Azure Marketplace に発行され、Web サービスの作成者がインフラストラクチャを設定することなく、世界中のユーザーやデバイスで使用されます。

##Web サービスの使用  
この Web サービスは、観察のすべての独立変数に基づいて、従属変数の予測値を示します。Web サービスでは、エンド ユーザーが、行をコンマ (,) で区切り、列をセミコロン (;) で区切った文字列として、データを入力する必要があります。Web サービスでは、一度に 1 行が入力され、最初の列は従属変数であると想定されます。データセットの例は、次のようになります。

![サンプル データ][1]

従属変数を指定しない観察の場合は、y に「NA」と入力する必要があります。上のデータセットへの入力データは、次の文字列になります。 “10;5;2,18;1;6,6;5.3;2.1,7;5;5,22;3;4,12;2;1,NA;3;4”.出力は、独立変数に基づく各行に対する予測値です。

>Azure Marketplace でホストされているこのサービスは、OData サービスです。これらは、POST や GET メソッドによって呼び出すことができます。

自動でサービスを使用するための複数の方法があります ([ここ](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx)にアプリケーション例があります)。

###Web サービスを使用する C# コードを開始します。

	public class Input
	{
	        public string value;
	}
	
	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { value = TextBox1.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
	    	var scoreResult = result.ReadAsStringAsync().Result;
	}




##Web サービスの作成  
>この Web サービスは、Azure Machine Learning を使用して作成されました。無料評価版の場合、実験を作成して [Web サービスを発行する](machine-learning-publish-a-machine-learning-web-service.md)入門ビデオに加えて、[azure.com/ml](http://azure.com/ml) もご覧ください。Web サービスを作成した実験のスクリーン ショット、および実験内の各モジュールに対するコード例を以下に示します。


Azure Machine Learning 内で、新しい空白の実験が作成され、ワークスペース上に 2 つの [R スクリプトの実行][execute-r-script]を取得しました。この Web サービスは、基になる R スクリプトで Azure Machine Learning の実験を実行します。つまり、この実験には、スキーマ定義とモデルのトレーニング + スコア付けの 2 つの部分があります。最初のモジュールは、入力されたデータセットに必要な構造を定義します。最初の変数を従属変数、残りを独立変数とします。2 番目のモジュールは、入力データの汎用線形回帰モデルに適合させます。
  
![実験フロー][3]

####モジュール 1:
 
####スキーマ定義  
	data <- data.frame(value = "1;2;3,4;5;6,7;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####モジュール 2:
####LM モデリング   
	data <- maml.mapInputPort(1) # class: data.frame  
  
	data.split <- strsplit(data[1,1], ",")[[1]]  
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
	data.split <- as.data.frame(t(data.split)) 
	data.split <- data.matrix(data.split) 
	data.split <- data.frame(data.split) 
	model <- lm(data.split)  

	out=data.frame(predict(model,data.split))  
	out <- data.frame(t(out))

	maml.mapOutputPort("out");  
 
##制限事項
これは、複数の線形回帰の Web サービスのきわめて単純な例です。上のコード例からわかるように、エラーのキャッチは実装されていません。このサービスでは、Web サービスの作成時に数値のみを入力するため、すべてが連続した変数 (カテゴリ別機能は使用できません) である必要があります。また、サービスが処理できるデータ サイズは制限されています。これは、Web サービス呼び出しの要求/応答の性質と、Web サービスを呼び出すたびにモデルが適合されることによります。

##FAQ
Web サービスの使用や、Azure Marketplace への発行に関するよく寄せられる質問については、[ここ](machine-learning-marketplace-faq.md)をご覧ください。

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0921_2016-->