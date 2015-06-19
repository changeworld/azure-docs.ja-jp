<properties 
	pageTitle="クラスター モデル | Azure" 
	description="クラスター モデル" 
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
	ms.date="02/12/2015" 
	ms.author="jaymathe"/>


#クラスター モデル    



クレジット カード発行者の償却リスクを軽減するために、クレジット カード所有者グループの行動を予測することはできますか。職場でのパフォーマンスを向上させるために、従業員の性格特性グループを定義することができますか。医師が、病気の特性に基づいて患者をグループに分類することができますか。原則として、こうした質問はすべて、クラスター分析によって回答することができます。    
   
実際には、クラスター分析は、変数の組み合わせに基づいて、一連の観察を 2 つ以上の相互に排他的な未知のグループに分類します。クラスター分析の目的は、観察 (通常は人々やその特性に対する観察) をグループに整理する方法を発見することです。グループのメンバーは、通常、プロパティを共有しています。この[サービス](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model)は、一般的に使用されるクラスタリング手法である K 平均法を使用して、任意のデータをグループに分類します。この Web サービスは、入力としてデータとクラスターの数 k を取得し、各観察がどの k グループに属するかの予測を生成します。 

>この Web サービスは、モバイル アプリ、Web サイト、ローカル コンピューターなどからユーザーが使用できますが、この Web サービスのもう 1 つの目的は、Azure ML を使用して R コード上に Web サービスを作成する方法の例を示すことです。数行の R コードを記述し、Azure ML Studio 内でボタンを何回かクリックするだけで、R コードで実験を作成し、Web サービスとして発行できます。この Web サービスは Azure Marketplace に発行され、Web サービスの作成者がインフラストラクチャを設定することなく、世界中のユーザーやデバイスで使用されます。  

#Web サービスの使用   
この Web サービスは、一連の k 個のグループにデータをグループ化し、行ごとにグループの割り当てを出力します。Web サービスでは、エンド ユーザーが、行をコンマ (,) で区切り、列をセミコロン (;) で区切った文字列として、データを入力する必要があります。Web サービスでは、一度に 1 行を入力します。データセットの例は、次のようになります。

![Sample data][1]

ユーザーがこのデータを相互に排他的な 3 つのグループに分割したいとします。上のデータセットに次のデータを入力します。「value = "10;5;2,18;1;6,7;5;5,22;3;4,12;2;1,10;3;4"; k="3"」。出力は、それぞれの行に対して予測されたグループ メンバーシップです。

>Microsoft Azure Marketplace でホストされているこのサービスは、OData サービスです。これらは、POST または GET メソッドによって呼び出すことができます。 

自動でサービスを使用するための複数の方法があります ([ここ](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx )にアプリケーション例があります)。

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
>この Web サービスは、Azure ML を使用して作成されました。無料評価版の場合、実験を作成して[Web サービスを発行する](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/)入門ビデオに加えて、[azure.com/ml](http://azure.com/ml) も参照してください。Web サービスを作成した実験のスクリーン ショット、および実験内の各モジュールに対するコード例を以下に示します。

Azure ML 内で、新しい空白の実験が作成され、ワークスペース上に 2 つの "R スクリプトの実行" を取得しました。データ スキーマが簡単な "R スクリプトの実行" で作成されました。次にそのデータ スキーマがクラスター モデル セクションにリンクされ、もう一度 "R スクリプト実行" で作成されました。クラスター モデルに使用された "R スクリプトの実行" では、Web サービスは "k-means" 関数を使用します。この関数は、Azure ML の "R スクリプト実行" に事前に組み込まれています。    
   

     
![Experiment flow][3]

####モジュール 1: 
	#Enter the input data as a string 
	mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
	maml.mapOutputPort("mydata");     
	

####モジュール 2:
	# Map 1-based optional input ports to variables
	mydata <- maml.mapInputPort(1) # class: data.frame

	data.split <- strsplit(mydata[1,1], ",")[[1]]
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- as.data.frame(t(data.split))

	data.split <- data.matrix(data.split)
	data.split <- data.frame(data.split)

	# K-Means Cluster Analysis
	fit <- kmeans(data.split, mydata$k) # k-cluster solution

	# get cluster means 
	aggregate(data.split,by=list(fit$cluster),FUN=mean)
	# append cluster assignment
	mydatafinal <- data.frame(t(fit$cluster))
	n_col=ncol(mydatafinal)
	colnames(mydatafinal) <- paste("V",1:n_col,sep="")

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("mydatafinal");
   
 
#制限事項
これは、Web サービスのクラスタリングのきわめて単純な例です。上のコード例からわかるように、エラーのキャッチは実装されていません。このサービスでは、Web サービスの作成時に数値のみを入力するため、すべてが連続した変数 (カテゴリ別機能は使用できません) である必要があります。また、サービスが処理できるデータ サイズは制限されています。これは、Web サービス呼び出しの要求/応答の性質と、Web サービスを呼び出すたびにモデルが適合されることによります。 

##FAQ
Web サービスの使用、または Marketplace への発行に関するよく寄せられる質問については、[ここ](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq)を参照してください。

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png

<!--HONumber=46--> 
 