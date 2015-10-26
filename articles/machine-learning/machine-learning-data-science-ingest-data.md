<properties 
	pageTitle="データの取り込み | Microsoft Azure" 
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
	ms.date="10/12/2015" 
	ms.author="bradsev" />

# データの取り込み

## はじめに

Cortana Analytics Process (CAP) では、さまざまなストレージ環境に取り込まれるデータまたは読み込まれるデータを、プロセスの各段階において最も適切な方法で処理または分析する必要があります。CAP での処理に通常使用されるデータの取り込み先として、Azure BLOB ストレージ、Azure SQL Database、Azure VM 上の SQL Server、HDInsight (Hadoop)、Azure Machine Learning などがあります。

この**メニュー**は、データを保存および処理するこれらのターゲット環境にデータを取り込む方法について説明するトピックにリンクしています。

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

分析の目的を達成するためにデータを取り込む必要があるターゲット環境は、技術上およびビジネス上のニーズと、データの最初の保存場所、形式、およびサイズによって決まります。予測モデルを構築するために必要なさまざまなタスクを実行するために、複数の環境間でデータを移動する必要があるシナリオも珍しくありません。この一連のタスクには、データ探索、前処理、クリーニング、サンプリング、モデル トレーニングなどが含まれます。

<!---HONumber=Oct15_HO3-->