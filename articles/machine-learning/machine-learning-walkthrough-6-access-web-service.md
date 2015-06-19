<properties 
	pageTitle="手順 6: Machine Learning Web サービスにアクセスする | Azure" 
	description="ソリューションのチュートリアル手順 6: アクティブな Azure Machine Learning API Web サービスにアクセスする" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/06/2014" 
	ms.author="garye"/>


これは、チュートリアル「[Azure Machine Learning を使用した予測ソリューションの開発][develop]」の最後の手順です。

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[ML ワークスペースを作成する][create-workspace]
2.	[既存のデータをアップロードする][upload-data]
3.	[新しい実験を作成する][create-new]
4.	[モデルをトレーニングして評価する][train-models]
5.	[Web サービスを発行する][publish]
6.	**Web サービスにアクセスする**

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# 手順 6.Azure Machine Learning Web サービスにアクセスする

Web サービスとして役に立つためには、ユーザーがサービスにデータを送信し、結果を受信できるようにする必要があります。Web サービスを Azure Web サービスとして提供することで、以下のいずれかの方法でデータを受信して返すことができるようになります。  

-	**要求/応答** - ユーザーが HTTP プロトコルを使用して 1 セットの信用データをサービスに送信し、サービスが 1 セットの結果を返します。
-	**バッチ実行** - ユーザーが Azure BLOB の URL をサービスに送信します。この BLOB には 1 行以上の信用データが含まれています。サービスは結果を別の BLOB に保存し、この BLOB のURL を返します。  

開発者は、Web サービスの **[ダッシュボード]** タブにある 2 種類のリンク先に記載された情報を活用して、サービスにアクセスするためのコードを記述することができます。**[要求/応答]** 行の **[API ヘルプ ページ]** リンクをクリックして表示されるページには、サービスの要求/応用プロトコルを使用するためのサンプル コードがあります。同様に、**[バッチ実行]** 行のリンクでは、サービスにバッチ要求を実行するためのサンプル コードを確認できます。  

API ヘルプ ページには、R、C#、Python の各プログラミング言語に対応したサンプルがあります。たとえば、以下に示す R コードを使って、発行した Web サービスにアクセスすることができます (実際のサンプル コードには、実際のサービス URL が表示されます)。  

	library("RCurl")
	library("RJSONIO")
	
	h = basicTextGatherer()
	req = list(Id="score00001",
	 Instance=list(FeatureVector=list(
	    "row.names"= "0",
	    "Status of checking account"= "0",
	    "Duration in months"= "0",
	    "Credit history"= "0",
	    "Purpose"= "0",
	    "Credit amount"= "0",
	    "Savings account/bond"= "0",
	    "Present employment since"= "0",
	    "Installment rate in percentage of disposable income"= "0",
	    "Personal status and sex"= "0",
	    "Other debtors"= "0",
	    "Present residence since"= "0",
	    "Property"= "0",
	    "Age in years"= "0",
	    "Other installment plans"= "0",
	    "Housing"= "0",
	    "Number of existing credits"= "0",
	    "Job"= "0",
	    "Number of people providing maintenance for"= "0",
	    "Telephone"= "0",
	    "Foreign worker"= "0",
	    "Credit risk"= "0"
	 ),GlobalParameters=fromJSON('{}')))
	
	body = toJSON(req)
	api_key = "abc123" # You can obtain the API key from the publisher of the web service
	
	h$reset()
	curlPerform(url = "http://xxx.cloudapp.net/workspaces/xxx/services/xxx/score",
	            httpheader=c('Content-Type' = "application/json", 'Authorization' = "Bearer " + api_key),
	            postfields=body,
	            writefunction = h$update,
	            verbose = TRUE
	            )
	
	result = h$value()



<!--HONumber=46--> 
 