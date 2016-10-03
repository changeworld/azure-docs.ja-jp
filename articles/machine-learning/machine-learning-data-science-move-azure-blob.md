<properties
	pageTitle="Azure Blob ストレージとの間のデータの移動 | Microsoft Azure"
	description="Azure Blob ストレージとの間のデータの移動"
	services="machine-learning,storage"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="bradsev;sachouks" />

# Azure Blob ストレージとの間のデータの移動

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

以下のリンクから、Azure Blob ストレージとの間でデータを移動するために使用するテクノロジについてのガイダンスを参照してください。

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]
 
最適な方法はシナリオによって異なります。高度な分析プロセスで使用されるさまざまなデータ サイエンス ワークフローで必要なリソースを確認するには、記事「[Azure Machine Learning での高度な分析のシナリオ](machine-learning-data-science-plan-sample-scenarios.md)」が役立ちます。

> [AZURE.NOTE] Azure BLOB ストレージの概要については、[Azure BLOB の基礎](../storage/storage-dotnet-how-to-use-blobs.md)に関する記事および [Azure BLOB サービス](https://msdn.microsoft.com/library/azure/dd179376.aspx)に関するページをご覧ください。

別の方法として、[Azure Data Factory](https://azure.microsoft.com/services/data-factory/) を使用して以下を行うこともできます。

- Azure BLOB ストレージからデータをダウンロードするパイプラインを作成し、スケジュールを設定する
- パイプラインを発行済みの Azure Machine Learning Web サービスに渡す
- 予測分析の結果を受け取る
- 結果をストレージにアップロードする

詳細については、「[Azure Data Factory および Azure Machine Learning を使用して予測パイプラインを作成する](../data-factory/data-factory-azure-ml-batch-execution-activity.md)」を参照してください。

## 前提条件

このドキュメントは、Azure サブスクリプション、ストレージ アカウント、そのアカウントに対応するストレージ キーがあることを前提としています。データのアップロード/ダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。

- Azure サブスクリプションを設定するには、[1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
- ストレージ アカウントの作成と、アカウントとキー情報の取得の手順については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。

<!---HONumber=AcomDC_0921_2016-->