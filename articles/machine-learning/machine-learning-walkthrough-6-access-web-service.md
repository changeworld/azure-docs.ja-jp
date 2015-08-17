<properties 
	pageTitle="手順 6: Machine Learning Web サービスにアクセスする | Microsoft Azure" 
	description="予測ソリューションの開発に関するチュートリアルの中の、アクティブな Azure Machine Learning Web サービスにアクセスするステップの 6 番目の手順です。" 
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
	ms.date="07/10/2015" 
	ms.author="garye"/>


# チュートリアル手順 6: Azure Machine Learning Web サービスにアクセスする

これは、チュートリアル「[Azure Machine Learning を使用した予測ソリューションの開発](machine-learning-walkthrough-develop-predictive-solution.md)」の最後の手順です。


1.	[Machine Learning ワークスペースの作成](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[既存のデータをアップロードする](machine-learning-walkthrough-2-upload-data.md)
3.	[新しい実験を作成する](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[モデルをトレーニングして評価する](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Web サービスを発行する](machine-learning-walkthrough-5-publish-web-service.md)
6.	**Web サービスにアクセスする**

----------

Web サービスとして役に立つためには、ユーザーが Web サービスにデータを送信し、結果を受信できるようにする必要があります。Web サービスを Azure Web サービスとして提供することで、以下のいずれかの方法でデータを受信して返すことができるようになります。

-	**要求/応答** - ユーザーが HTTP プロトコルを使用して 1 セットのクレジット データをサービスに送信し、サービスが 1 セットの結果を返します。
-	**Batch 実行** - ユーザーが Azure BLOB の URL をサービスに送信します。この BLOB には 1 行以上のクレジット データが含まれています。サービスは結果を別の BLOB に保存し、この BLOB のURL を返します。  

開発者は、Web サービスの **[ダッシュボード]** タブにある 2 種類のリンク先に記載された情報を活用して、サービスにアクセスするためのコードを記述することができます。**[要求/応答]** 行の **[API ヘルプ ページ]** リンクをクリックして表示されるページには、サービスの要求/応用プロトコルを使用するためのサンプル コードがあります。同様に、**[バッチ実行]** 行のリンクでは、サービスにバッチ要求を実行するためのサンプル コードを確認できます。

API ヘルプ ページには、R、C#、Python の各プログラミング言語に対応したサンプルがあります。

これらの Web サービスにアクセスして使用する方法の詳細については、「[Machine Learning の実験から発行された Azure Machine Learning Web サービスを使用する方法](machine-learning-consume-web-services.md)」を参照してください。

<!---HONumber=August15_HO6-->