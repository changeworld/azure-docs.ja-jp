<properties 
	pageTitle="手順 1: Machine Learning ワークスペースの作成 | Azure" 
	description="ソリューションのチュートリアル手順 1: 新しい Azure Machine Learning Studio ワークスペースを作成する" 
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
	ms.date="01/29/2015" 
	ms.author="garye"/>


これは、チュートリアル「[Azure Machine Learning を使用した予測ソリューションの開発][develop]」の最初の手順です。

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/

1.	**ML ワークスペースを作成する**
2.	[既存のデータをアップロードする][upload-data]
3.	[新しい実験を作成する][create-new]
4.	[モデルをトレーニングして評価する][train-models]
5.	[Web サービスを発行する][publish]
6.	[Web サービスにアクセスする][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# 手順 1.Azure Machine Learning ワークスペースの作成

ML Studio を使用するには、ML ワークスペースを作成する必要があります。このワークスペースには、実験を管理および公開するのに必要なツールが用意されています。  

## ワークスペースを作成するには  

1.	Microsoft Azure のアカウントを使用してサインインします。
2.	Microsoft Azure サービス パネルで、**[MACHINE LEARNING]** をクリックします。  
![Create workspace][1]

3.	**[ML ワークスペースを作成]** をクリックします。
4.	**[簡易作成]** ページにワークスペースの情報を入力して、**[ML ワークスペースを作成]** をクリックします。

	>**注** - **[ワークスペースの所有者]** は Microsoft アカウント (name@outlook.com など) または組織アカウントです。

ML ワークスペースが作成されると、**[Machine Learning]** ページの一覧に表示されます。  

>**ヒント** - ワークスペースに招待することで、作業中の実験を自分以外のユーザーと共有することができます。これは、ML Studio の **[設定]** ページから実行でき、必要な情報は各ユーザーの Microsoft アカウントまたは組織アカウントだけです。

----------

**次の手順: [既存のデータをアップロードする][upload-data]**

[1]: ./media/machine-learning-walkthrough-1-create-ml-workspace/create1.png

<!--HONumber=46--> 
