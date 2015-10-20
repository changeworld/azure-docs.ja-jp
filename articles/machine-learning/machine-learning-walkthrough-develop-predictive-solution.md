<properties
	pageTitle="信用リスク評価のための予測分析ソリューションを Machine Learning で開発する | Microsoft Azure"
	description="信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する方法を詳しく紹介したチュートリアルです。"
	keywords="credit risk, predictive analytics solution,risk assessment"
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
	ms.topic="get-started-article"
	ms.date="10/13/2015"
	ms.author="garye"/>


# チュートリアル: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する

信用貸付の申請書に記入する情報に基づいて個人の信用リスクを予測する必要があるとします。

もちろん信用リスクの評価は複雑な問題ですが、ここでは質問に含まれるパラメーターを少し簡略化し、そのような予測分析ソリューションを作成するために Microsoft Azure Machine Learning を Machine Learning Studio および Machine Learning Web サービスと共に使用する方法の例として取り上げます。

ここでは、Machine Learning Studio での予測分析モデルの開発プロセスに従って作業した後、モデルを Machine Learning Web サービスにデプロイします。公開されているクレジット リスク データを使用して、データに基づいた予測モデルを開発してトレーニングし、次に、他のユーザーが信用リスクの評価目的に使用できる Web サービスとしてモデルをデプロイします。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Machine Learning Studio を開くには、[https://studio.azureml.net/Home](https://studio.azureml.net/Home) のリンクをクリックします。Machine Learning Studio の概要の詳細については、「[Microsoft Azure Machine Learning Studio ホーム](https://studio.azureml.net/)」をご覧ください。

>[AZURE.TIP]Machine Learning Studio の機能の概要を示す図をダウンロードして印刷する場合は、「[Azure Machine Learning Studio 機能の概要図](machine-learning-studio-overview-diagram.md)」参照してください。

信用リスク評価ソリューションを作成するのには、次の手順に従います。

1.	[Machine Learning ワークスペースの作成](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[既存のデータをアップロードする](machine-learning-walkthrough-2-upload-data.md)
3.	[新しい実験を作成する](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[モデルをトレーニングして評価する](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Web サービスにアクセスする](machine-learning-walkthrough-6-access-web-service.md)

このチュートリアルは、Machine Learning Studio に含まれる「[Credit risk prediction sample experiment (クレジット リスク予測のサンプル実験)](../machine-learning-sample-credit-risk-prediction.md)」の簡略版に基づきます。

<!---HONumber=Oct15_HO3-->