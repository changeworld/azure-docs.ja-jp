<properties 
	pageTitle="Team Data Science Process のチュートリアル | Microsoft Azure" 
	description="チュートリアルでは、クラウドとオンプレミスのツールとサービスをワークフローまたはパイプラインに組み込んで、インテリジェントなアプリケーションを作成する方法について説明します。" 
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
	ms.date="06/14/2016" 
	ms.author="bradsev" />


# Team Data Science Process のチュートリアル

ここで紹介する各エンド ツー エンド チュートリアルで取り上げるのは、**特定のシナリオ**に対する Team Data Science Process のステップです。クラウドとオンプレミスのツールとサービスをワークフローまたはパイプラインに組み込んで、インテリジェントなアプリケーションを作成する方法についても説明します。

## SQL Server の使用
[Team Data Science Process の活用: SQL Sever の使用](machine-learning-data-science-process-sql-walkthrough.md)チュートリアルでは、SQL Server と公開されている [NYC タクシー乗車](http://www.andresmh.com/nyctaxitrips/)データセットを使用し、機械学習モデルを構築してデプロイする方法について説明します


## HDInsight Hadoop クラスターの使用
[Team Data Science Process の活用: HDInsight Hadoop クラスターの使用](machine-learning-data-science-process-hive-walkthrough.md)チュートリアルでは、[Azure HDInsight Hadoop クラスター](https://azure.microsoft.com/services/hdinsight/) を使用して、公開されている [NYC タクシー乗車](http://www.andresmh.com/nyctaxitrips/)データセットのデータを格納、調査し、特徴エンジニアリングを行います。


## Team Data Science Process の活用 - 1 TB データセットでの Azure HDInsight Hadoop クラスターの使用
[Team Data Science Process の活用 - 1 TB データセットでの Azure HDInsight Hadoop クラスターの使用](machine-learning-data-science-process-hive-criteo-walkthrough.md)チュートリアルでは、[Azure HDInsight Hadoop クラスター](https://azure.microsoft.com/services/hdinsight/)を使用して、公開されている [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) データセットのいずれかのデータを格納、調査し、特徴エンジニアリングとダウンサンプリングを行うエンド ツー エンドのシナリオを紹介します。

## Azure HDInsight での Spark を使用したデータ サイエンス
[Azure HDInsight での Spark を使用したデータ サイエンス](machine-learning-data-science-spark-overview.md) チュートリアルでは、[Azure HDInsight Spark クラスター](https://azure.microsoft.com/services/hdinsight/)を使用したエンド ツー エンドのシナリオで Team Data Science Process を使用して、公開されている [NYC タクシー乗車](http://www.andresmh.com/nyctaxitrips/)データセットでデータを格納、調査し、特徴エンジニアリングを行います。


## Azure Data Lake のスケーラブルなデータ サイエンス
[Azure Data Lake のスケーラブルなデータ サイエンス: エンド ツー エンド チュートリアル](machine-learning-data-science-process-data-lake-walkthrough.md)では、NYC タクシー乗車と料金のデータセットを例にして、Azure Data Lake を使用してデータ調査タスクと二項分類タスクを実行し、料金ごとにチップが支払われるかどうかを予測します。

## SQL Server R Services を使用したデータ サイエンスのエンド ツー エンド チュートリアル
[SQL Server R Services を使用したデータ サイエンスのエンド ツー エンド チュートリアル](https://msdn.microsoft.com/library/mt612857.aspx)では、よく知られた公開されているニューヨーク市タクシー乗車データセットを使用します。R コード、SQL Server データ、およびカスタム SQL 関数を組み合わせて使用し、ドライバーが特定のタクシー乗車でチップを得る可能性を示す分類モデルを構築します。また、R モデルを SQL Server にデプロイし、サーバー データを使用して、そのモデルに基づいてスコアを生成します。

## SQL 開発者を対象とした高度な In-Database 分析

[SQL 開発者を対象とした高度な In-Database 分析](https://msdn.microsoft.com/library/mt683480.aspx)チュートリアルの目的は、SQL プログラマに、SQL Server R Services を使用した高度な分析ソリューション構築の実践的体験を提供することです。ここでは、ストアド プロシージャで R コードをラップして、R をアプリケーションまたは BI ソリューションに統合する方法について説明します。

<!---HONumber=AcomDC_0622_2016-->