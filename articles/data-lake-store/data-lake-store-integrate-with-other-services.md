<properties
   pageTitle="Data Lake Store と他の Azure サービスを統合する | Azure"
   description="Data Lake Store が他の Azure サービスと統合されるしくみを理解します"
   documentationCenter=""
   services="data-lake-store" 
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/04/2016"
   ms.author="nitinme"/>

# Data Lake Store と他の Azure サービスを統合する

Azure Data Lake Store を他の Azure サービスと連携させれば、さまざまなシナリオが可能になります。次の記事では、Data Lake Store と統合できるサービスについてまとめてあります。

## Azure HDInsight で Data Lake Store を使用する

HDFS 準拠ストレージとして Data Lake Store を使用する [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) クラスターにプロビジョニングできます。このリリースでは、Windows と Linux の Hadoop または Storm クラスターの場合、Data Lake Store は追加ストレージとしてのみ利用できます。このようなクラスターでは、依然として Azure Storage (WASB) がデフォルト ストレージとして使用されます。ただし、Windows と Linux の HBase クラスターの場合、Data Lake Store をデフォルト ストレージと追加ストレージの両方として使用できます。

Data Lake Store で HDInsight クラスターにプロビジョニングする方法については、次を参照してください。

* [Azure ポータルを使用して、Data Lake Store を使用する HDInsight クラスターをプロビジョニングする](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Azure PowerShell を使用して、Data Lake Store を使用する HDInsight クラスターをプロビジョニングする](data-lake-store-hdinsight-hadoop-use-powershell.md)


## Azure Data Lake Analytics で Data Lake Store を使用する

[Azure Data Lake Analytics](data-lake-analytics/data-lake-analytics-overview.md) を利用すれば、クラウド スケールでビッグ データを操作できます。リソースを動的にプロビジョニングし、サポートされているさまざまなデータ ソース (Data Lake Store を含む) に保存可能なテラバイト単位の、さらにはエクサバイト単位の分析を可能にします。Data Lake Analytics は Azure Data Lake Store と連動するように最適化されており、ビッグ データの作業負荷に対して最高レベルのパフォーマンス、スループット、並列化を提供します。

Data Lake Store で Data Lake Analytics を使用する方法については、「[Data Lake Store を使用して Data Lake Analytics を始める](data-lake-analytics/data-lake-analytics-get-started-portal.md)」を参照してください。


## Azure Data Factory で Data Lake Store を使用する

[Azure Data Factory](https://azure.microsoft.com/services/data-factory/) を使用し、Azure テーブル、Azure SQL Database、Azure SQL DataWarehouse、Azure Storage BLOB、オンプレミス データベースからデータを取り込むことができます。Azure エコシステムの第一級オブジェクトである Azure Data Factory を使用し、これらのソースから Azure Data Lake Store へのデータ取り込みを調整できます。

Data Lake Store で Azure Data Factory を使用する方法については、「[Data Factory を使用し、Data Lake Store のデータを移動する](data-factory/data-factory-azure-datalake-connector.md)」を参照してください。

## Azure Storage BLOB から Data Lake Store へのデータのコピー

Azure Data Lake Store には、AdlCopy というコマンド ライン ツールがあります。このツールを使用すると、Azure Blob Storage のデータを Data Lake Store アカウントにコピーできます。詳細については、「[Azure Storage BLOB から Data Lake Store へのデータのコピー](data-lake-store-copy-data-azure-storage-blob.md)」を参照してください。


## 関連項目

- [Azure Data Lake Store の概要](data-lake-store-overview.md)
- [Azure プレビュー ポータルで Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)
- [Azure PowerShell で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-powershell.md)  

<!---HONumber=AcomDC_0107_2016-->