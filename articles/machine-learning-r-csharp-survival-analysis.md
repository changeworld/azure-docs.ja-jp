<properties 
	pageTitle="生存分析 | Azure" 
	description="生存分析イベントの発生確率" 
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


#生存分析 





多くのシナリオでは、主な評価結果は、関心のあるイベントまでの時間です。つまり、"このイベントはいつ発生するか" という質問に回答します。例として、データが、関心のあるイベント (疾病再発、博士号の取得、ブレーキ パッドの故障など) が発生するまでの経過時間 (日数、年数、マイレージなど) を示している場合を考えてみます。データの各インスタンスは、特定のオブジェクト (患者、人、車など) を表します。

この [Web サービス]( https://datamarket.azure.com/dataset/aml_labs/survivalanalysis)は、"オブジェクト x に対して n (時間) までに関心のあるイベントが発生する確率はどのくらいでしょうか" という質問に回答します。生存分析モデルを提供することによって、この Web サービスによりユーザーはデータを入力してモデルをトレーニングし、テストすることができます。実験の主要なテーマは、関心のあるイベントが発生するまでの経過時間の長さをモデル化することです。 

>この Web サービスは、モバイル アプリ、Web サイト、ローカル コンピューターなどからユーザーが使用できますが、この Web サービスのもう 1 つの目的は、Azure ML を使用して R コード上に Web サービスを作成する方法の例を示すことです。数行の R コードを記述し、Azure ML Studio 内でボタンを何回かクリックするだけで、R コードで実験を作成し、Web サービスとして発行できます。この Web サービスは Azure Marketplace に発行され、Web サービスの作成者がインフラストラクチャを設定することなく、世界中のユーザーやデバイスで使用されます。  

##Web サービスの使用

次の表には、Web サービスの入力データ スキーマが表示されます。6 種類の情報 (トレーニング データ、テスト データ、関心のある時間、"time" ディメンションのインデックス、"event" ディメンションのインデックス、変数の種類 (連続または要因)) を入力する必要があります。トレーニング データは、文字列 (行はコンマで、列はセミコロンで区切ります) で表します。データの特徴の数は自由です。入力文字列内のすべての要素を数値にする必要があります。トレーニング データでは、"time" ディメンションは、学習の開始位置 (患者が薬物治療プログラムを受け取る、学生が博士号の勉強を開始する、車の運転を開始するなど) から、関心のあるイベント (患者が再び薬物使用に戻る、学生が博士号を取得する、車のブレーキ パッドが故障するなど) が発生するまでに経過した時間の単位数 (日数、年数、マイレージなど) を示します。"event" ディメンションでは、学習の最後に関心のあるイベントが発生するかどうかを示します。"event = 1" は、"time" ディメンションによって示された時点に関心のあるイベントが発生するという意味です。"event = 0" は、関心のあるイベントが、"time" ディメンションが示している時点ではまだ発生していないということです。

- trainingdata: 文字列。行はコンマで区切り、列はセミコロンで区切ります。各行には、"time" ディメンション、"event" ディメンション、および予測変数が含まれます。
- testingdata: 特定のオブジェクトに対する予測変数が含まれている 1 行のデータ。
- time_of_interest: 関心 n の経過時間
- index_time: "time" ディメンション (1 から始まる) の列インデックス
- index_event: "event" ディメンション (1 から始まる) の列インデックス
- variable_types: 区切り記号としてセミコロンを使用した文字列。0 は連続変数を表し、1 は要因変数を表します。


出力は、特定の時間にイベントが発生する確率です。 

>Microsoft Azure Marketplace でホストされているこのサービスは、OData サービスです。これらは、POST または GET メソッドによって呼び出すことができます。 

自動でサービスを使用するための複数の方法があります ([ここ](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx)にアプリケーション例があります)。 

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


このテストの解釈を次に示します。データの目標は、次の 2 つの治療プログラムのいずれかを受け取った患者が薬物使用に戻るまでの経過時間をモデル化することです。Web サービスの出力から、次を読み取ります。患者の年齢は 35 歳、これまで 2 回薬物治療を受け、長期的な宿泊型の治療プログラムを採用しています。そして、ヘロインとコカインの両方について、この患者が 500 日以内に薬物使用に戻る確率は 95.64% です。

##Web サービスの作成

>この Web サービスは、Azure ML を使用して作成されました。無料評価版の場合、実験を作成して[Web サービスを発行する](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/)入門ビデオに加えて、[azure.com/ml](http://azure.com/ml) も参照してください。Web サービスを作成した実験のスクリーン ショット、および実験内の各モジュールに対するコード例を以下に示します。

Azure ML 内で、新しい空白の実験が作成され、ワークスペース上に 2 つの "R スクリプトの実行" を取得しました。データ スキーマは、Web サービス用の入力データ スキーマを定義する、単純な "R スクリプトの実行" で作成されました。このモジュールは、主要な作業を行う 2 つ目の "R スクリプトの実行" モジュールにリンクされます。このモジュールは、データの前処理、モデル構築、および予測を実行します。データの前処理手順で、長い文字列で表された入力データが変換され、データ フレームに変換されます。モデル構築手順では、生存分析を実施するため、外部の R パッケージ "survival_2.37 7.zip" が最初にインストールされます。一連のデータ処理タスクの後に "coxph" 関数が実行されます。生存分析のための "coxph" 関数の詳細については、R のドキュメントを参照してください。予測手順では、"surfit" 関数でトレーニング済みのモデルにテスト インスタンスが入力され、このテストのインスタンスの生存曲線が "curve" 変数として生成されます。最後に、関心のある時間の確率が取得されます。 

###実験フロー:

![experiment flow][1]

####モジュール 1:

    #data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1"
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)
    maml.mapOutputPort("sampleInput"); #send data to output port
	
####モジュール 2:

    #read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")
    # preprocessing trainingdata
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)
    # preprocessing testingdata
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))
    # preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types
    # necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)
    # prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # based on user input, find the event occurance probablity
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }
    #pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##制限事項

この Web サービスでは、特徴変数として数値変数 (列) のみを使用できます。"event" 列には 0 または 1 の値のみを指定できます。"time" 列は、正の整数である必要があります。

##FAQ
Web サービスの使用、または Marketplace への発行に関するよく寄せられる質問については、[ここ](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq)を参照してください。

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png

<!--HONumber=46--> 
