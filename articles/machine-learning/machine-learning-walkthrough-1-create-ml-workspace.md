<properties
	pageTitle="手順 1: Machine Learning ワークスペースの作成 | Microsoft Azure"
	description="予測ソリューションの開発に関するチュートリアルの中の、保存されているパブリック データを Azure Machine Learning Studio ワークスペースを設定する方法の 1 番目の手順です。"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="garye"/>


# チュートリアル手順 1: Machine Learning ワークスペースの作成

これは、「[チュートリアル: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する](machine-learning-walkthrough-develop-predictive-solution.md)」の最初の手順です。


1.	**Machine Learning ワークスペースの作成**
2.	[既存のデータをアップロードする](machine-learning-walkthrough-2-upload-data.md)
3.	[新しい実験を作成する](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[モデルをトレーニングして評価する](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Web サービスにアクセスする](machine-learning-walkthrough-6-access-web-service.md)

----------

<!-- This needs to be updated to refer to the new way of creating workspaces in the Ibiza portal -->

Machine Learning Studio を使用するには、Microsoft Azure Machine Learning ワークスペースが必要です。このワークスペースには、実験を管理および公開するのに必要なツールが用意されています。

##ワークスペースを作成するには  

1.	[Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
2.	Azure サービス パネルで、**[MACHINE LEARNING]** をクリックします。![Create workspace][1]

3.	**[ML ワークスペースを作成]** をクリックします。
4.	**[簡易作成]** ページにワークスペースの情報を入力して、**[ML ワークスペースを作成]**. をクリックします。

Machine Learning ワークスペースが作成されると、Azure クラシック ポータルの**機械学習**ページの一覧に表示されます。ワークスペースの作成方法の詳細については、「[Azure Machine Learning ワークスペースの作成](machine-learning-create-workspace.md)」をご覧ください。

> [AZURE.TIP] ワークスペースに招待することで、作業中の実験を自分以外のユーザーと共有できます。これは、Machine Learning Studio の **[設定]** ページから実行でき、必要な情報は各ユーザーの Microsoft アカウントまたは組織アカウントだけです。

----------

**次: [既存のデータをアップロードする](machine-learning-walkthrough-2-upload-data.md)**

[1]: ./media/machine-learning-walkthrough-1-create-ml-workspace/create1.png

<!---HONumber=AcomDC_0921_2016-->