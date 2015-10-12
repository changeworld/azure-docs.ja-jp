<properties 
	pageTitle="Azure Blob ストレージとの間のデータの移動 | Microsoft Azure" 
	description="Azure Blob ストレージとの間のデータの移動" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/01/2015" 
	ms.author="bradsev;sunliangms;sachouks;mohabib" />

# Azure Blob ストレージとの間のデータの移動

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

## はじめに

このトピックでは、Azure Blob ストレージからデータを移動する 3 つの方法について説明します。

- Azure ストレージ エクスプローラーの使用
- AzCopy コマンド ライン ユーティリティの使用
- Python での Azure SDK の使用

最適な方法は、シナリオによって異なります。高度な分析プロセスで使用されるさまざまなデータ サイエンス ワークフローで必要なリソースを確認するには、記事「[Azure Machine Learning での Advanced Analytics Process and Technology (ADAPT) のためのシナリオ](../machine-learning-data-science-plan-sample-scenarios.md)」が役立ちます。

> [AZURE.NOTE]Azure BLOB ストレージの完全な概要については、「[Azure BLOB の基礎](../storage-dotnet-how-to-use-blobs.md)」と「[Azure BLOB Service](https://msdn.microsoft.com/library/azure/dd179376.aspx)」を参照してください。

> [AZURE.TIP]別の方法として、[Azure Data Factory](https://azure.microsoft.com/ja-JP/services/data-factory/) を使用して、Azure BLOB ストレージからデータをダウンロードして公開済みの Azure Machine Learning Web サービスに渡し、予測分析の結果を受け取ってストレージにアップロードするパイプラインを作成してスケジュールできます。詳細については、「[Azure Data Factory および Azure Machine Learning を使用して予測パイプラインを作成する](../data-factory/data-factory-create-predictive-pipelines.md)」を参照してください。

## 前提条件

このドキュメントは、Azure サブスクリプション、ストレージ アカウント、そのアカウントに対応するストレージ キーがあることを前提としています。データのアップロード/ダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。

- Azure サブスクリプションを設定するには、「[1 か月間の無料評価版](https://azure.microsoft.com/ja-JP/pricing/free-trial/)」を参照してください。
- ストレージ アカウントの作成と、アカウントとキー情報の取得についての手順については、「[Azure ストレージ アカウントについて](../storage-create-storage-account.md)」を参照してください。




 

<!---HONumber=Oct15_HO1-->