<properties
	pageTitle="手順 6: Machine Learning Web サービスにアクセスする | Microsoft Azure"
	description="予測ソリューションの開発に関するチュートリアルの中の、アクティブな Azure Machine Learning Web サービスにアクセスするステップの 6 番目の手順です。"
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


# チュートリアル手順 6: Azure Machine Learning Web サービスにアクセスする

これは、「[チュートリアル: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する](machine-learning-walkthrough-develop-predictive-solution.md)」の最後の手順です。


1.	[Machine Learning ワークスペースの作成](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[既存のデータをアップロードする](machine-learning-walkthrough-2-upload-data.md)
3.	[新しい実験を作成する](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[モデルをトレーニングして評価する](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)
6.	**Web サービスにアクセスする**

----------

このチュートリアルで前の手順では、信用リスク予測モデルを使用する Web サービスをデプロイしました。ここで、ユーザーが Web サービスにデータを送信し、結果を受信できるようにする必要があります。

Web サービスを Azure Web サービスとして提供すると、以下のいずれかの方法で、REST API によりデータを受信して返すことができるようになります。

-	**要求/応答** - ユーザーが HTTP プロトコルを使用して 1 行以上のクレジット データをサービスに送信し、サービスが 1 つ以上の結果のセットを返します。
-	**バッチ実行** - ユーザーが Azure BLOB に 1 行以上のクレジット データを格納し、そのBLOB の場所をサービスに送信します。サービスは、入力 BLOB 内のデータのすべての行にスコアを付け、結果を別の BLOB に格納し、この BLOB のURL を返します。

Web サービスにアクセスするための最もすばやくて簡単な方法は、[Azure Web App Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/) で入手できる Web アプリ テンプレートを利用する方法です。このような Web アプリ テンプレートを使用すると、Web サービスの入力データとサービスで返される内容を認識するカスタム Web アプリを構築できます。必要な操作は、Web サービスおよびデータへのアクセスを許可することだけで、後の処理はテンプレートによって行われます。

Web アプリ テンプレートの使用方法の詳細については、「[Web アプリケーション テンプレートによる Azure Machine Learning Web サービスの使用](machine-learning-consume-web-service-with-web-app-template.md)」をご覧ください。

R、C#、Python プログラミング言語で提供されるスターター コードを使用して、Web サービスにアクセスするカスタム アプリケーションを開発することもできます。詳細については、「[Machine Learning の実験からデプロイされた Azure Machine Learning Web サービスを使用する方法](machine-learning-consume-web-services.md)」をご覧ください。

<!---HONumber=AcomDC_0921_2016-->