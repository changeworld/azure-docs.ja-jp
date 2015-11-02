<properties 
	pageTitle="Cortana Analytics Process のデータをサンプリングする" 
	description="さまざまなストレージ環境のデータを探索する方法について説明します。" 
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
	ms.date="10/20/2015" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>Cortana Analytics Process のデータをサンプリングする

この**メニュー**は、多様なストレージ環境のデータをサンプリングする方法を説明するトピックにリンクしています。このタスクは、Cortana Analytics Process (CAP) の 1 ステップです。

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-explore-data-selector.md)]

## はじめに

このドキュメントでは、Azure BLOB ストレージに格納されたデータのサンプリングについて説明します。これは、プログラムを使用してこのデータをダウンロードしてから、Python のサンプル コードでサンプリングして行います。これを行う手順は、次のとおりです。

このドキュメントでは、Cortana Analytics Process で一般的に使用されている 3 つの場所に格納されたデータをサンプリングする方法について説明します。

- ** データ**をサンプリングするには、プログラムでダウンロードし、サンプル Python コードを使用します。
- **SQL Server データ**をサンプリングするには、SQL と Python プログラミング言語の両方を使用します。 
- **Hive テーブル データ**をサンプリングするには、Hive クエリを使用します。

<!---HONumber=Oct15_HO4-->