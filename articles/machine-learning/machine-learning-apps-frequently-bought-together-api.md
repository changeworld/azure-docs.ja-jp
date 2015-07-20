<properties 
	pageTitle="Machine Learning のアプリケーション例: Frequently Bought Together | Microsoft Azure" 
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
	ms.date="06/12/2015" 
	ms.author="luisca"/>

# Machine Learning のアプリケーション例: Frequently Bought Together


## 重要: このサービスは推奨されていません

このサービスによって提供される Frequently Bought Together 機能は、より広範囲にわたる [Recommendations API](http://gallery.azureml.net/MachineLearningAPI/3574432384684cac9cc766e57729ea4c) に統合されました。このサービスではなく、Recommendations を使用することをお勧めします。

##概要

Machine Learning の [Frequently Bought Together Web サービス](https://datamarket.azure.com/dataset/amla/mba)は、オンライン ショッピング カート分析を実行し、履歴のトランザクションから、一緒に購入されることの多いお勧め製品の品目を生成します。Frequently Bought Together による推奨は、買い物客が特定の品目を購入する際に、それと最も関係の深いカタログ内の製品を特定するのに役立ちます。これらの推奨製品を目立つように表示することは、オンライン ショッピング業者の売上の増加に効果的であることが証明されています。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
  
##作業開始 

Frequently Bought Together Web サービスをサブスクライブすると、[マーケット バスケット分析サービス Web アプリケーションの例](https://marketbasket.cloudapp.net/)を使用して、簡単にデータをモデルにアップロードして、頻繁に購入される製品セットを検出することができます。アプリケーションまたは API を使用するには、[Azure データ マーケットのアカウント ページ](https://datamarket.azure.com/account)から入手できる API キーを最初に用意しておく必要があります。

##Web サービスの使用 

このサービスに含まれる API を使用して、Frequently Bought Together モデルを管理および作成し、トランザクションの履歴をアップロードし、特定の製品に対して最も頻繁に一緒に購入された製品のセットを取得することができます。これらの API の使用方法を示す例は、GitHub の [Azure-MachineLearning-DataScience](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Apps/FrequentlyBoughtTogether) リポジトリにあります。

 

<!---HONumber=July15_HO2-->