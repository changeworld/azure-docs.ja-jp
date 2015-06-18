<properties 
	pageTitle="Machine Learning を使用した予測ソリューションの開発 | Azure" 
	description="Azure Machine Learning Studio で予測的な分析手法を作成する方法の段階的説明" 
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

# Azure Machine Learning を使用した予測ソリューションの開発

信用貸付の申請書に記入する情報に基づいて個人の信用リスクを予測する必要があるとします。

もちろんこれは複雑な問題ですが、ここでは質問に含まれるパラメーターを少し簡略化し、そのような予測分析ソリューションを作成するために Microsoft Azure Machine Learning を ML Studio および ML API サービスと共に使用する方法の例として取り上げます。

このチュートリアルでは、ML Studio での予測分析モデルの開発プロセスに従って作業した後、モデルを ML API サービスに公開します。公開されている信用リスク データを使用して、データに基づいた予測モデルを開発してトレーニングし、次に、他のユーザーが使用できる Web サービスとしてモデルを公開します。

次の手順に従います。

1.  [ML ワークスペースを作成する][ML ワークスペースを作成する]
2.  [既存のデータをアップロードする][既存のデータをアップロードする]
3.  [新しい実験を作成する][新しい実験を作成する]
4.  [モデルをトレーニングして評価する][モデルをトレーニングして評価する]
5.  [Web サービスを発行する][Web サービスを発行する]
6.  [Web サービスにアクセスする][Web サービスにアクセスする]

このチュートリアルは、ML Studio に含まれる「
[Credit risk prediction sample experiment (信用リスク予測のサンプル実験)][Credit risk prediction sample experiment (信用リスク予測のサンプル実験)]」の簡略版に基づきます。

  [ML ワークスペースを作成する]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [既存のデータをアップロードする]: ../machine-learning-walkthrough-2-upload-data/
  [新しい実験を作成する]: ../machine-learning-walkthrough-3-create-new-experiment/
  [モデルをトレーニングして評価する]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Web サービスを発行する]: ../machine-learning-walkthrough-5-publish-web-service/
  [Web サービスにアクセスする]: ../machine-learning-walkthrough-6-access-web-service/
  [Credit risk prediction sample experiment (信用リスク予測のサンプル実験)]: ../machine-learning-sample-credit-risk-prediction/

<!--HONumber=46--> 
 