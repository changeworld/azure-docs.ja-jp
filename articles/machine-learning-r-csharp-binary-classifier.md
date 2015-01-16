<properties title="Binary Classifier" pageTitle="バイナリ分類器 | Azure" description="バイナリ分類器" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/13/2014" ms.author="jaymathe" /> 



#バイナリ分類器

  


データセットを用意し、独立変数に基づいてバイナリ従属変数を予測するとします。'ロジスティック回帰' は、こうした予測のための一般的な統計手法です。ここでは、従属変数はバイナリ変数または二値変数で、p は関心のある特性が存在する確率です。 

単純なシナリオとしては、研究者が情報 (高校の GPA、家族の収入、居住状況、性別) を基に、見込みのある学生が大学への入学許可を受け入れるかどうかを予測しようとする場合が考えられます。予測結果は、見込みのある学生が入学許可を受け入れる確率です。この [Web サービス]( https://datamarket.azure.com/dataset/aml_labs/log_regression)は、ロジスティック回帰モデルをデータに適合させ、データ内の観測ごとに、確率の値 (y) を出力します。  
  
>この Web サービスは、モバイル アプリ、Web サイト、ローカル コンピューターなどからユーザーが使用できますが、この Web サービスのもう 1 つの目的は、Azure ML を使用して R コード上に Web サービスを作成する方法の例を示すことです。数行の R コードを記述し、Azure ML Studio 内でボタンを何回かクリックするだけで、R コードで実験を作成し、Web サービスとして発行できます。この Web サービスは Azure Marketplace に発行され、Web サービスの作成者がインフラストラクチャを設定することなく、世界中のユーザーやデバイスで使用されます。  
  

#Web サービスの使用  
この Web サービスは、観察のすべての独立変数に基づいて、従属変数の予測値を示します。Web サービスでは、エンド ユーザーが、行をコンマ (,) で区切り、列をセミコロン (;) で区切った文字列として、データを入力する必要があります。Web サービスでは、一度に 1 行が入力され、最初の列は従属変数であると想定されます。データセットの例は、次のようになります。

![Sample data][1]

従属変数を指定しない観察の場合は、y に「NA」と入力する必要があります。上のデータセットへの入力データは、次の文字列になります。「1;5;2,1;1;6,0;5.3;2.1,0;5;5,0;3;4,1;2;1,NA;3;4」。出力は、独立変数に基づく各行に対する予測値です。 

>Microsoft Azure Marketplace でホストされているこのサービスは、OData サービスです。これらは、POST または GET メソッドによって呼び出すことができます。 

自動でサービスを使用するための複数の方法があります ([ここ](http://microsoftazuremachinelearning.azurewebsites.net/BinaryClassifier.aspx )にアプリケーション例があります)。

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


#Web サービスの作成  
>この Web サービスは、Azure ML を使用して作成されました。無料評価版の場合、実験を作成して[Web サービスを発行する](http://azure.microsoft.com/ja-jp/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/)入門ビデオに加えて、[azure.com/ml](http://azure.com/ml) も参照してください。Web サービスを作成した実験のスクリーン ショット、および実験内の各モジュールに対するコード例を以下に示します。

Azure ML 内で、新しい空白の実験が作成され、ワークスペース上に 2 つの "R スクリプトの実行" を取得しました。この Web サービスは、基になる R スクリプトで Azure Machine Learning の実験を実行します。つまり、この実験には、スキーマ定義、およびモデルのトレーニング + スコア付けの 2 つの部分があります。  最初のモジュールは、入力されたデータセットに必要な構造を定義します。最初の変数を従属変数、残りを独立変数とします。2 番目のモジュールは、入力データを汎用ロジスティック回帰モデルに適合させます。    

![Experiment flow][2]

####モジュール 1:

	#Schema definition  
	data <- data.frame(value = "1;2;3,1;5;6,0;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####Module 2:
	#GLM Modeling   
	data <- maml.mapInputPort(1) # class: data.frame  data.split <- strsplit(data[1,1], ",")[[1]] data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) data.split <- as.data.frame(t(data.split)) data.split <- 
	data.matrix(data.split) data.split <- data.frame(data.split) model <- glm(data.split$V1 ~., family='binomial', data=data.split)  out <- 
	data.frame(predict(model,data.split, type="response")) pred1 <- as.data.frame(out) group <- array(1:nrow(pred1)) for (i in 
	1:nrow(pred1))  
	{if(as.numeric(pred1[i,])>0.5) {group[i]=1} else {group[i]=0}} pred2 <- as.data.frame(group) maml.mapOutputPort("pred2");  


#制限事項
これは、バイナリ分類 Web サービスのきわめて単純な例です。上のコード例からわかるように、エラーのキャッチは実装されていません。このサービスでは、Web サービスの作成時に数値のみを入力するため、すべてがバイナリの連続した変数 (カテゴリ別機能は使用できません) である必要があります。また、サービスが処理できるデータ サイズは制限されています。これは、Web サービス呼び出しの要求/応答の性質と、Web サービスを呼び出すたびにモデルが適合されることによります。 

##FAQ
Web サービスの使用、または Marketplace への発行に関するよく寄せられる質問については、[ここ](http://azure.microsoft.com/ja-jp/documentation/articles/machine-learning-marketplace-faq)を参照してください。

[1]: ./media/machine-learning-r-csharp-binary-classifier/binary1.png
[2]: ./media/machine-learning-r-csharp-binary-classifier/binary2.png
