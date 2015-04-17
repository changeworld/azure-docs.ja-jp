<properties 
	pageTitle="Machine Learning のアプリケーション例:Frequently Bought Together | Azure" 
	description="オンラインのショッピング カート分析によって、ユーザーによって提供されるトランザクション履歴から、頻繁に一緒に購入された品目を推奨製品として表示する Machine Learning Web サービスです。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="CoromT" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/17/2014" 
	ms.author="coromt"/> 

# Machine Learning のアプリケーション例:Frequently Bought Together

##概要
 
Machine Learning  の [Frequently Bought Together Web サービス]( https://datamarket.azure.com/dataset/amla/mba) はオンライン ショッピング カートの分析を実行し、履歴のトランザクションから頻繁に一緒に購入された品目の推奨製品を生成します。Frequently Bought Together による推奨は、買い物客が特定の品目を購入する際に、それと最も関係の深いカタログ内の製品を特定するのに役立ちます。これらの推奨製品を目立つように表示することは、オンライン ショッピング業者の売上の増加に効果的であることが証明されています。 

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)] 
  
##はじめに 

Frequently Bought Together Web サービスをサブスクライブすると、[マーケット バスケット分析サービス Web アプリケーションの例 ](https://marketbasket.cloudapp.net/) を使用して、簡単にデータをモデルにアップロードして、頻繁に購入される製品セットを検出することができます。アプリケーションまたは API を使用するには、最初に、[Azure データ マーケットのアカウント ページ](https://datamarket.azure.com/account) から入手できる API キーが必要となります。

##Web サービスの使用 

このサービスに含まれる API を使用して、Frequently Bought Together モデルを管理および作成し、トランザクションの履歴をアップロードし、特定の製品に対して最も頻繁に一緒に購入された製品のセットを取得することができます。これらの API の使用方法を示す例が、[GitHub の Azure-MachineLearning-DataScience リポジトリ](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Apps/FrequentlyBoughtTogether) にあります。


<!--HONumber=49-->