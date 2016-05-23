<properties 
	pageTitle="Machine Learning Web サービスのログ記録 | Microsoft Azure" 
	description="Machine Learning Web サービスのログ記録を有効にする方法について説明します。ログ記録では、API のトラブルシューティングに役立つ追加情報を提供します。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data" 
	ms.date="05/10/2016"
	ms.author="raymondl;garye"/>

#Machine Learning Web サービスのログ記録の有効化  

このドキュメントは、Azure ML Web サービスのログ記録機能について説明します。Web サービスでのログ記録を有効にすると、正常とエラーの数やメッセージだけでなく、API のトラブルシューティングに役立つ追加情報を得られます。

-	Web サービスでのログ記録を有効にする方法   
	-	[Azure クラシック ポータル](https://manage.windowsazure.com/)にログインします。
	-	[Machine Learning]、自分のワークスペース、[Web サービス] (メニュー オプション) の順にクリックします。
	-	Web サービスの一覧で、Web サービスの名前をクリックします。
	-	エンドポイントの一覧で、エンドポイントの名前をクリックします。
	-	[構成] メニュー オプションをクリックします。
	-	[診断トレース レベル] を [エラー] または [すべて] に設定し、下部にあるメニュー バーの [保存] をクリックします。
-	ログ記録の有効化による効果  
	-	ログ記録が有効な場合、選択したエンドポイントのすべての診断とエラーが、ユーザーのワークスペースにリンクされている Azure ストレージ アカウントに記録されます。このストレージ アカウントは、ワークスペースの Azure クラシック ポータルの [ダッシュボード] ビュー ([概要] セクションの下部) で確認できます。  
-	ログを表示する方法  
	-	Azure のストレージ アカウントを探索できる複数のツールを使って、ログを表示できます。最も簡単な方法は、Azure クラシック ポータルで [ストレージ アカウント] に移動し、[コンテナー] タブをクリックすることです。そうすることで、**"ml-diagnostics"** という名前のコンテナーを確認できます。このコンテナーには、このストレージ アカウントに関連付けられているすべてのワークスペースの Web サービス エンドポイントの診断情報が保持されています。  
-	ログ BLOB の詳細：  
	-	コンテナーの各 BLOB には、次の診断情報が 1 つだけ保持されています。
		-	バッチ実行メソッドの実行  
		-	要求応答メソッドの実行  
		-	要求応答コンテナーの初期化  
	-	各 BLOB の名前には、プレフィックスが付きます (形式: {ワークスペース ID}-{Web サービス ID}-{エンドポイント ID}/{ログの種類})。  
-	ログの種類には、次のいずれかの値を指定します。  
	- batch (バッチ)  
	- score/requests (スコア/要求)  
	- score/init (スコア/初期化)  

 

<!---HONumber=AcomDC_0511_2016-->