<properties
	pageTitle="Hive クエリを使用して Hive テーブルのデータを探索する | Microsoft Azure"
	description="Hive クエリを使用して Hive テーブルのデータを探索します。"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="hangzh;bradsev" />

# Hive クエリを使用して Hive テーブルのデータを探索する 

この**メニュー**は、多様なストレージ環境のデータを探索するツールの使用方法を説明するトピックにリンクしています。このタスクは、Cortana Analytics Process (CAP) の 1 ステップです。

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## はじめに

このドキュメントでは、Hive テーブルのデータの探索に使用される Hive スクリプトの例を紹介します。

## 前提条件
この記事では、以下のことを前提としています。

* Azure のストレージ アカウントが作成されている。手順については、「[Azure ストレージ アカウントの作成](../hdinsight-get-started.md#storage)」をご覧ください。
* HDInsight サービスでカスタマイズされた Hadoop クラスターがプロビジョニングされている。手順については、「[Advanced Analytics Process and Technology 向けに Azure HDInsight Hadoop クラスターをカスタマイズする](machine-learning-data-science-customize-hadoop-cluster.md)」をご覧ください。
* データが Azure HDInsight Hadoop クラスターの Hive テーブルにアップロードされている。アップロードされていない場合は、まず「[データを作成して Hive テーブルに読み込む](machine-learning-data-science-move-hive-tables.md)」に従って Hive テーブルにデータをアップロードします。
* クラスターへのリモート アクセスが有効になっている。手順については、「[Hadoop クラスターのヘッド ノードへのアクセス](machine-learning-data-science-customize-hadoop-cluster.md#headnode)」をご覧ください。
* Hive クエリの送信手順については、「[データを作成して Azure BLOB ストレージから Hive テーブルに読み込む](machine-learning-data-science-move-hive-tables.md#submit)」を参照してください。

## データ探索のための Hive クエリ スクリプトの例

1. パーティションごとの所見の数を取得する `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. 1 日ごとの所見の数を取得する `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. カテゴリ列内のレベルを取得する `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. 2 つのカテゴリ列の組み合わせ内のレベル数を取得する `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. 数値型列の分布を取得する `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. 2 つのテーブルの結合からレコードを抽出する

	    SELECT
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>,
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>,
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## タクシー乗車データ シナリオのその他のクエリ スクリプト

[NYC タクシー乗車データ](http://chriswhong.com/open-data/foil_nyc_taxi/) シナリオに固有のクエリの例も、[Github リポジトリ](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)に用意されています。これらのクエリには、指定されたデータ スキーマが既にあり、すぐに送信して実行できる状態になっています。

 

<!---HONumber=Oct15_HO4-->