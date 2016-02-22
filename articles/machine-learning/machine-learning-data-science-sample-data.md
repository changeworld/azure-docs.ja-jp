<properties 
	pageTitle="Azure BLOB コンテナー、SQL Server、Hive テーブルのデータのサンプリング | Microsoft Azure" 
	description="さまざまな Azure 環境に格納されているデータを探索する方法について説明します。" 
	services="machine-learning" 
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
	ms.date="02/07/2016" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>Azure BLOB コンテナー、SQL Server、Hive テーブルのデータのサンプリング

## はじめに

このドキュメントでは、Cortana Analytics Process でデータの分析とモデリングを行うときに一般的に使用される Azure の 3 つの場所に格納されたデータをサンプリングする方法について説明します。

- ** データ**をサンプリングするには、プログラムでダウンロードし、サンプル Python コードを使用します。
- **SQL Server データ**をサンプリングするには、SQL と Python プログラミング言語の両方を使用します。 
- **Hive テーブル データ**をサンプリングするには、Hive クエリを使用します。

次の**メニュー**は、これらの各 Azure ストレージ環境のデータをサンプリングする方法を説明するトピックにリンクしています。

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

このサンプリング タスクは、[Cortana Analytics Process (CAP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) の 1 ステップです。

## データをサンプリングする理由

分析しようとしているデータセットが大規模な場合、データをダウンサンプリングして、小規模であっても典型的であり、管理しやすいサイズに減らすことが通常は推奨されます。これにより、データの理解、探索、および特徴エンジニアリングが容易になります。Cortana Analytics Process におけるダウンサンプリングの役割は、データ処理機能と機械学習モデルのプロトタイプをより迅速に作成できるようにすることです。

<!---HONumber=AcomDC_0211_2016-->