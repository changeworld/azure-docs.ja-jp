---
title: Azure Data Explorer (Kusto)
description: この記事では、コネクタを使用して、Azure Data Explorer (Kusto) およびサーバーレス Apache Spark プールの間でデータを移動する方法について説明します。
services: synapse-analytics
ms.author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/19/2020
ms.reviewer: ManojRaheja
author: midesa
ms.openlocfilehash: 82310e14b3eac25be038a7748d1992daef72d83f
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2021
ms.locfileid: "112516677"
---
# <a name="azure-data-explorer-kusto-connector-for-apache-spark"></a>Apache Spark 用の Azure Data Explorer (Kusto) コネクタ
Apache Spark 用の Azure Data Explorer (Kusto) コネクタは、Kusto クラスターと Spark の間でデータを効率的に転送するように設計されています。 このコネクタは、Python、Java、.NET で使用できます。 これは、Azure Synapse Apache Spark 2.4 ランタイムに組み込まれています。

## <a name="authentication"></a>認証
Azure Synapse Notebook または Apache Spark のジョブ定義を使用すると、リンク サービスを使用してシステム間の認証がシームレスに行われます。 トークン サービスは Azure Active Directory に接続し、Kusto クラスターにアクセスするときに使用するセキュリティ トークンを取得します。

Azure Synapse Pipelines の場合、認証ではサービス プリンシパル名が使用されます。 現在、Azure Data Explorer コネクタでは、マネージド ID はサポートされていません。

## <a name="prerequisites"></a>前提条件 
  - [Azure Data Explorer に接続する](../../quickstart-connect-azure-data-explorer.md): 既存の Kusto クラスターに接続するために、リンク サービスを設定する必要があります。

## <a name="limitations"></a>制限事項
  - Azure Data Explorer (Kusto) コネクタは、現在、Azure Synapse Apache Spark 2.4 ランタイムでのみサポートされています。
  - Azure Data Explorer のリンク サービスは、サービス プリンシパル名を使用してのみ構成できます。
  - Azure Synapse Notebooks または Apache Spark のジョブ定義内では、Azure Data Explorer コネクタは AAD パススルーを使用して Kusto クラスターに接続します。


## <a name="use-the-azure-data-explorer-kusto-connector"></a>Azure Data Explorer (Kusto) コネクタを使用する
次のセクションでは、Kusto テーブルにデータを書き込む方法と、Kusto テーブルからデータを読み取る方法の簡単な例を示します。 詳細については、[Azure Data Explorer (Kusto) コネクタ プロジェクト](https://github.com/Azure/azure-kusto-spark)に関するドキュメントを参照してください。 

### <a name="read-data"></a>データの読み取り

```python
kustoDf  = spark.read \
            .format("com.microsoft.kusto.spark.synapse.datasource") \
            .option("spark.synapse.linkedService", "<link service name>") \
            .option("kustoDatabase", "<Database name>") \
            .option("kustoQuery", "<KQL Query>") \
            .load()

display(kustoDf)
```

また、強制分散モードやその他の詳細オプションを使用してバッチ読み取りを行うこともできます。 追加情報については、[Kusto ソース オプションのリファレンス](https://github.com/Azure/azure-kusto-spark/blob/master/connector/src/main/scala/com/microsoft/kusto/spark/datasource/KustoSourceOptions.scala)に関するページを参照してください。

```python
crp = sc._jvm.com.microsoft.azure.kusto.data.ClientRequestProperties()
crp.setOption("norequesttimeout",True)
crp.toString()

kustoDf  = spark.read \
            .format("com.microsoft.kusto.spark.synapse.datasource") \
            .option("spark.synapse.linkedService", "<link service name>") \
            .option("kustoDatabase", "<Database name>") \
            .option("kustoQuery", "<KQL Query>") \
            .option("clientRequestPropertiesJson", crp.toString()) \
            .option("readMode", 'ForceDistributedMode') \
            .load()

display(kustoDf) 
```
### <a name="write-data"></a>データを書き込む

```python
df.write \
    .format("com.microsoft.kusto.spark.synapse.datasource") \
    .option("spark.synapse.linkedService", "<link service name>") \
    .option("kustoDatabase", "<Database name>") \
    .option("kustoTable", "<Table name>") \
    .mode("Append") \
    .save()
```
さらに、追加のインジェスト プロパティを指定することで、データをバッチ書き込みすることができます。 サポートされているインジェスト プロパティの詳細については、[Kusto インジェスト プロパティの参照資料](/azure/data-explorer/ingestion-properties)に関するページを参照してください。


 ```python
extentsCreationTime = sc._jvm.org.joda.time.DateTime.now().plusDays(1)
csvMap = "[{\"Name\":\"ColA\",\"Ordinal\":0},{\"Name\":\"ColB\",\"Ordinal\":1}]"
# Alternatively use an existing csv mapping configured on the table and pass it as the last parameter of SparkIngestionProperties or use none


sp = sc._jvm.com.microsoft.kusto.spark.datasink.SparkIngestionProperties(
        False, ["dropByTags"], ["ingestByTags"], ["tags"], ["ingestIfNotExistsTags"], extentsCreationTime, csvMap, None)

df.write \
    .format("com.microsoft.kusto.spark.synapse.datasource") \
    .option("spark.synapse.linkedService", "<link service name>") \
    .option("kustoDatabase", "<Database name>") \
    .option("kustoTable", "<Table name>") \
    .option("sparkIngestionPropertiesJson", sp.toString()) \
    .option("tableCreateOptions","CreateIfNotExist") \
    .mode("Append") \
    .save()
```

## <a name="next-steps"></a>次のステップ
- [Azure データ エクスプローラーに接続する](../../quickstart-connect-azure-data-explorer.md)
- [Azure Data Explorer (Kusto) Apache Spark コネクタ](https://github.com/Azure/azure-kusto-spark)
