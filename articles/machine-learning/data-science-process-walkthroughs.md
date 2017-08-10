---
title: "Team Data Science Process のチュートリアル | Microsoft Docs"
description: "チュートリアルでは、クラウドとオンプレミスのツールとサービスをワークフローまたはパイプラインに組み込んで、インテリジェントなアプリケーションを作成する方法について説明します。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aa63d5a5-25ee-4c4b-9a4c-7553b98d7f6e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: c0f0ff599909234b2b43f2b8512d77f9053b8a09
ms.contentlocale: ja-jp
ms.lasthandoff: 07/26/2017

---
# <a name="team-data-science-process-walkthroughs"></a>Team Data Science Process のチュートリアル
ここで紹介する各**エンド ツー エンド チュートリアル**で取り上げるのは、**特定のシナリオ**に対する Team Data Science Process のステップです。 クラウド、オンプレミスのツール、およびサービスをワークフローまたはパイプラインに組み込んで、**インテリジェントなアプリケーション**を作成する方法について説明します。 チュートリアルは、使用している**プラットフォーム**ごとにグループ分けされています。 

- PySpark と Scala を使用した Spark
- HDInsight (Hadoop)
- Azure Data Lake 
- SQL Server
- SQL Data Warehouse 


## <a name="hdinsight-spark-using-pyspark-and-scala"></a>PySpark と Scala を使用した HDInsight Spark

- [Azure HDInsight で Spark を使用する](machine-learning-data-science-spark-overview.md)に関するチュートリアルでは、[Azure HDInsight Spark クラスター](https://azure.microsoft.com/services/hdinsight/)を使用したシナリオで Team Data Science Process を使用して、公開されている NYC タクシー乗車および料金データセットのデータを格納、調査し、特徴エンジニアリングを行います。

- [Azure で Spark を使用した Scala を使用する](machine-learning-data-science-process-scala-walkthrough.md)に関するチュートリアルでは、Azure HDInsight Spark クラスターで Spark の Machine Learning ライブラリ (MLlib) と SparkML パッケージを使用して、教師あり機械学習タスクに Scala を使用する方法を説明します。 また、 [データ サイエンス プロセス](http://aka.ms/datascienceprocess)(データの取り込みと探索、視覚化、特徴エンジニアリング、モデリング、モデルの使用) を構成するタスクについても説明します。 構築されるモデルには、ロジスティック回帰と線形回帰、ランダム フォレスト、勾配ブースティング ツリーなどがあります。


## <a name="hdinsight-hadoop"></a>HDInsight Hadoop 

- [HDInsight Hadoop クラスターの使用](machine-learning-data-science-process-hive-walkthrough.md)に関するチュートリアルでは、[Azure HDInsight Hadoop クラスター](https://azure.microsoft.com/services/hdinsight/)を使用して、公開されている NYC タクシー乗車および料金データセットのデータを格納、調査し、特徴エンジニアリングを行います。

- [1 TB データセットでの Azure HDInsight Hadoop クラスターの使用](machine-learning-data-science-process-hive-criteo-walkthrough.md)に関するチュートリアルでは、[Azure HDInsight Hadoop クラスター](https://azure.microsoft.com/services/hdinsight/)を使用して、公開されている [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) データセットのサンプル データを格納、調査し、特徴エンジニアリングとダウンサンプリングを行うシナリオを紹介します。


## <a name="azure-data-lake"></a>Azure Data Lake

- [データ サイエンスに Azure Data Lake を使用する](machine-learning-data-science-process-data-lake-walkthrough.md)に関するチュートリアルでは、NYC タクシーのデータセットのサンプルで Azure Data Lake を使用して、データ調査タスクと 2 項分類タスクを実行し、乗客ごとにチップが支払われるかどうかを予測します。 


## <a name="sql-server-and-sql-data-warehouse"></a>SQL Server と SQL Data Warehouse 

- [SQL Data Warehouse の使用](machine-learning-data-science-process-sqldw-walkthrough.md)に関するチュートリアルでは、SQL Data Warehouse (SQL DW) と公開されている NYC タクシー乗車および料金データセットを使用して、機械学習の分類および回帰モデルの構築とデプロイを行う方法を説明します。

- [SQL Server を使用する](machine-learning-data-science-process-sql-walkthrough.md)に関するチュートリアルでは、SQL Server と公開されている NYC タクシー乗車および料金データセットを使用して、機械学習の分類および回帰モデルの構築とデプロイを行う方法を説明します。

- [SQL Server R Services を使用する](https://msdn.microsoft.com/library/mt612857.aspx)に関するチュートリアルでは、データ サイエンティスト向けに、R モデルを構築して SQL Server にデプロイするための、R コード、SQL Server データ、カスタム SQL 関数の組み合わせを提供します。

- [SQL Server R Services で T-SQL を使用する](https://msdn.microsoft.com/library/mt683480.aspx)に関するチュートリアルは、SQL プログラマ向けに、SQL Server R Services で Transact-SQL を使用した高度な分析ソリューション構築の体験を提供して、R ソリューションを扱えるようにします。

- [SQL Server Python Services で T-SQL を使用する](https://docs.microsoft.com/en-us/sql/advanced-analytics/tutorials/sqldev-in-database-python-for-sql-developers)に関するチュートリアルでは、SQL プログラマが SQL Server で機械学習ソリューションの構築を体験できます。 Python コードをストアド プロシージャに追加することによって、Python をアプリケーションに組み込む方法を示しています。

## <a name="whats-next"></a>次の手順
Azure でのデータ サイエンス プロセスを構成するタスクについて説明したトピックの概要については、 [データ サイエンス プロセス](http://aka.ms/datascienceprocess)に関するページをご覧ください。 


