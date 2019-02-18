---
title: Azure Data Lake Storage Gen2 と他の Azure サービスを統合する | Microsoft Docs
description: Azure Data Lake Storage Gen2 が他の Azure サービスと統合されるしくみを理解します
documentationcenter: ''
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: nitinme
ms.openlocfilehash: 7bc4889403e1d52cfa3b18792a554f0faf605132
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885543"
---
# <a name="integrate-azure-data-lake-storage-gen2-with-other-azure-services"></a>Azure Data Lake Storage Gen2 と他の Azure サービスを統合する

Azure サービスを使用して、Azure Data Lake Storage Gen2 ストレージ アカウント内のデータを取り込み、分析し、視覚化することができます。 実行しようとしているタスクに最適なサービスを選択してください。

## <a name="ingest-data-into-your-data-lake"></a>Data Lake にデータを取り込む

これらのサービスは、Data Lake にデータを取り込むのに役立ちます。

### <a name="azure-data-factory"></a>Azure Data Factory

[Azure Data Factory](https://azure.microsoft.com/services/data-factory/) を使用して、次のソースからデータを取り込むことができます。

* Azure テーブル
* Azure SQL データベース
* Azure SQL DataWarehouse
* Azure Storage BLOB
* オンプレミス データベース

[Data Factory を使用した Data Lake Storage Gen2 との間でのデータ移動](../../data-factory/connector-azure-data-lake-store.md)に関する記事をご覧ください。

## <a name="analyze-and-visualize-data-in-your-data-lake"></a>Data Lake 内のデータを分析して視覚化する

これらのサービスでは、Data Lake Storage Gen2 をストレージ エンドポイントとして使用できます。

### <a name="azure-hdinsight"></a>Azure HDInsight

HDFS に準拠しているストレージとして Data Lake Storage Gen2 を使用する [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) クラスターにプロビジョニングできます。 このリリースでは、Windows と Linux 上の Hadoop または Storm クラスターの場合、Data Lake Storage Gen2 は追加ストレージとしてのみ利用できます。 このようなクラスターでは、依然として Azure Storage (WASB) がデフォルト ストレージとして使用されます。 一方、Windows と Linux 上の HBase クラスターの場合は、Data Lake Storage Gen2 を既定のストレージや追加ストレージとして使用できます。

「[Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)」をご覧ください

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

[Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-overview.md) を使用すると、クラウド スケールでビッグ データを操作できます。 リソースを動的にプロビジョニングするので、エクサバイト規模のデータも分析できます。 Data Lake Analytics は、データ ソースとして Data Lake Storage Gen2 を使用するように最適化されています。 

[Data Lake Storage Gen2 を使用して Data Lake Analytics の使用を開始する](../../data-lake-analytics/data-lake-analytics-get-started-portal.md)方法に関する記事をご覧ください。

## <a name="copy-data-to-other-repositories"></a>データを他のリポジトリにコピーする

Data Lake からデータをコピーし、他のリポジトリ内に配置するには、次のサービスを使用します。

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

PolyBase を使用して、Data Lake Storage Gen2 から SQL Data Warehouse にデータを読み込むことができます。 詳しくは、[SQL Data Warehouse での Data Lake Storage Gen2 の使用](../../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)に関する記事をご覧ください。

## <a name="see-also"></a>関連項目

* [Azure Data Lake Storage Gen2 の概要](data-lake-storage-introduction.md)
* [Data Lake Storage Gen2 を使用してビッグ データの要件に対応する](data-lake-storage-data-scenarios.md)
