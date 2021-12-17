---
title: Azure Synapse Data Explorer のデータ インジェストの概要 (プレビュー)
description: Azure Synapse Data Explorer でデータを取り込む (読み込む) さまざまな方法について説明します。
ms.topic: overview
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: aa240292a3263c6ad7ab922654cc4cab5df1d88c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720317"
---
# <a name="azure-synapse-data-explorer-data-ingestion-overview-preview"></a>Azure Synapse Data Explorer のデータ インジェストの概要 (プレビュー)

データ インジェストとは、Azure Synapse Data Explorer プールで 1 つまたは複数のソースからデータ レコードを読み込んで、テーブルにデータをインポートするプロセスのことです。 取り込まれたデータは、クエリに使用できるようになります。

データ インジェストを担当する Azure Synapse Data Explorer のデータ管理サービスは、次のプロセスを実行します。

- 外部ソースからバッチまたはストリーミングでデータをプルし、保留中の Azure キューから要求を読み取ります。
- 同じデータベースおよびテーブルに送られるバッチ データは、インジェストのスループット向けに最適化されます。
- 初期データが検証され、必要に応じて形式が変換されます。
- スキーマの照合、データの整理、インデックス付け、エンコード、圧縮など、その他のデータ操作。
- データは、設定されたアイテム保持ポリシーに従ってストレージに保存されます。
- 取り込まれたデータは、エンジンにコミットされます。このエンジンで、クエリのためにそのデータを使用できます。

## <a name="supported-data-formats-properties-and-permissions"></a>サポートされるデータ形式、プロパティ、およびアクセス許可

* **[サポートされるデータ形式](data-explorer-ingest-data-supported-formats.md)**

* **[インジェストのプロパティ](data-explorer-ingest-data-properties.md)** :データの取り込まれる方法に影響を与えるプロパティ (タグ付け、マッピング、作成時間など)。

* **アクセス許可**:データを取り込むプロセスでは、[データベース インジェスター レベルのアクセス許可](/azure/data-explorer/kusto/management/access-control/role-based-authorization?context=/azure/synapse-analytics/context/context)が必要です。 クエリなどの他の操作では、データベース管理者、データベース ユーザー、またはテーブル管理者のアクセス許可が必要になる場合があります。

## <a name="batching-vs-streaming-ingestions"></a>バッチ処理とストリーミングのインジェスト

* バッチ処理インジェストでは、データのバッチ処理が行われ、高インジェスト スループットのために最適化されます。 この方法は、推奨される、最もパフォーマンスの高い種類のインジェストです。 データはインジェスト プロパティに従ってバッチ処理されます。 データの小さなバッチはマージされ、高速クエリの結果用に最適化されます。 [インジェスト バッチ](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context) ポリシーは、データベースまたはテーブルに対して設定できます。 既定では、バッチ処理の最大値は、5 分、1000 項目、または合計サイズ1 GB です。  バッチ インジェスト コマンドのデータ サイズ制限は 4 GB です。

* [ストリーミング インジェスト](data-explorer-ingest-data-streaming.md) は、ストリーミング ソースからの継続的なデータ インジェストです。 ストリーミング インジェストを使用すると、テーブルごとに少量のデータ セットに対してほぼリアルタイムの待機時間を実現できます。 データは最初に行ストアに取り込まれ、次に列ストアのエクステントに移動されます。

## <a name="ingestion-methods-and-tools"></a>インジェストの方法とツール

Azure Synapse Data Explorer では複数のインジェスト方法がサポートされており、それぞれに固有のターゲット シナリオがあります。 これらの方法には、インジェスト ツール、さまざまなサービスへのコネクタとプラグイン、マネージド パイプライン、SDK を使用したプログラムによる取り込み、インジェストへの直接アクセスなどがあります。

### <a name="ingestion-using-managed-pipelines"></a>マネージド パイプラインを使用したインジェスト

外部サービスによって実行される管理 (調整、再試行、監視、アラートなど) を必要とする組織では、コネクタを使用するのが最も適切なソリューションである可能性があります。 キューによるインジェストは、大量のデータに適しています。 Azure Synapse Data Explorer では、次の Azure Pipelines がサポートされています。

<!-- * **[Event Grid](https://azure.microsoft.com/services/event-grid/)**: A pipeline that listens to Azure storage, and updates Azure Synapse Data Explorer to pull information when subscribed events occur. For more information, see [Ingest Azure Blobs into Azure Synapse Data Explorer](ingest-data-event-grid.md). -->

* **[イベント ハブ](https://azure.microsoft.com/services/event-hubs/)** : サービスから Azure Synapse Data Explorer にイベントを転送するパイプライン。 詳しくは、「[イベント ハブから Azure Synapse Data Explorer にデータを取り込む](data-explorer-ingest-event-hub-overview.md)」を参照してください。

<!-- * **[IoT Hub](https://azure.microsoft.com/services/iot-hub/)**: A pipeline that is used for the transfer of data from supported IoT devices to Azure Synapse Data Explorer. For more information, see [Ingest from IoT Hub](ingest-data-iot-hub.md). -->

* **Synapse パイプライン**: [Synapse パイプライン](../../../data-factory/copy-activity-overview.md?context=%2fazure%2fsynapse-analytics%2fcontext%2fcontext&tabs=synapse-analytics)の分析ワークロード用のフル マネージド データ統合サービスは、90 を超えるサポートされるソースに接続して、効率的で回復性があるデータ転送を提供します。 Synapse パイプラインでは、さまざまな方法で監視できる分析情報を提供するために、データが準備、変換、強化されます。 このサービスは、1 回限りのソリューションとして、または定期的なタイムラインで使用したり、特定のイベントによってトリガーしたりすることができます。

<!-- ### Ingestion using connectors and plugins

* **Logstash plugin**, see [Ingest data from Logstash to Azure Synapse Data Explorer](ingest-data-logstash.md).

* **Kafka connector**, see [Ingest data from Kafka into Azure Synapse Data Explorer](ingest-data-kafka.md).

* **[:::no-loc text="Power Automate":::](https://flow.microsoft.com/)**: An automated workflow pipeline to Azure Synapse Data Explorer. :::no-loc text="Power Automate"::: can be used to execute a query and do preset actions using the query results as a trigger. See [Azure Synapse Data Explorer connector to :::no-loc text="Power Automate"::: (Preview)](flow.md).

* **Apache Spark connector**: An open-source project that can run on any Spark cluster. It implements data source and data sink for moving data across Azure Synapse Data Explorer and Spark clusters. You can build fast and scalable applications targeting data-driven scenarios. See [Azure Synapse Data Explorer Connector for Apache Spark](spark-connector.md). -->

### <a name="programmatic-ingestion-using-sdks"></a>SDK を使用したプログラムによるインジェスト

Azure Synapse Data Explorer では、クエリとデータ インジェストに使用できる SDK が提供されています。 プログラムによるインジェストは、インジェスト プロセスの最中および後のストレージ トランザクションを最小限に抑えることによって、インジェスト コスト (COG) を削減するように最適化されています。

開始する前に、次の手順を使用して、プログラムによるインジェストを構成するための Data Explorer プール エンドポイントを取得します。

[!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]

**使用可能な SDK とオープン ソース プロジェクト**

* [Python SDK](/azure/data-explorer/kusto/api/python/kusto-python-client-library?context=/azure/synapse-analytics/context/context)

* [.NET SDK](/azure/data-explorer/kusto/api/netfx/about-the-sdk?context=/azure/synapse-analytics/context/context)

* [Java SDK](/azure/data-explorer/kusto/api/java/kusto-java-client-library?context=/azure/synapse-analytics/context/context)

* [Node SDK](/azure/data-explorer/kusto/api/node/kusto-node-client-library?context=/azure/synapse-analytics/context/context)

* [REST API](/azure/data-explorer/kusto/api/netfx/kusto-ingest-client-rest?context=/azure/synapse-analytics/context/context)

* [GO SDK](/azure/data-explorer/kusto/api/golang/kusto-golang-client-library?context=/azure/synapse-analytics/context/context)

### <a name="tools"></a>ツール

* **[ワンクリック インジェスト](data-explorer-ingest-data-one-click.md)** : さまざまな種類のソースからテーブルを作成および調整することにより、データを迅速に取り込むことができます。 ワンクリック インジェストでは、Azure Synapse Data Explorer 内のデータ ソースに基づいて、テーブルとマッピングの構造が自動的に提案されます。 ワンクリック インジェストは、1 回限りのインジェストに使用できます。また、データが取り込まれたコンテナーで Event Grid を使用した継続的インジェストを定義するために使用できます。

<!-- * **[LightIngest](lightingest.md)**: A command-line utility for ad-hoc data ingestion into Azure Synapse Data Explorer. The utility can pull source data from a local folder or from an Azure blob storage container. -->

### <a name="kusto-query-language-ingest-control-commands"></a>Kusto クエリ言語の取り込み制御コマンド

Kusto クエリ言語 (KQL) のコマンドを使用してデータをエンジンに直接取り込む方法は多数あります。 この方法ではデータ管理サービスがバイパスされるため、これは探索およびプロトタイプにのみ適しています。 運用環境または大規模なシナリオでは、この方法を使用しないでください。

  * **インライン インジェスト**:取り込まれるデータをコマンド テキスト自体の一部として、制御コマンド [.ingest inline](/azure/data-explorer/kusto/management/data-ingestion/ingest-inline?context=/azure/synapse-analytics/context/context) がエンジンに送信されます。 この方法は、即席のテストを目的としたものです。

  * **クエリからの取り込み**:クエリまたはコマンドの結果としてデータを間接的に指定し、制御コマンド [.set、.append、.set-or-append、または .set-or-replace](/azure/data-explorer/kusto/management/data-ingestion/ingest-from-query?context=/azure/synapse-analytics/context/context) がエンジンに送信されます。

  * **ストレージからの取り込み (プル)** :エンジンによってアクセス可能であり、コマンドで指定された外部ストレージ (Azure Blob Storage など) にデータを格納して、制御コマンド [.ingest into](/azure/data-explorer/kusto/management/data-ingestion/ingest-from-storage?context=/azure/synapse-analytics/context/context) がエンジンに送信されます。

取り込み制御コマンドの使用例については、「[Data Explorer を使用して分析する](../../get-started-analyze-data-explorer.md)」を参照してください。

<!-- ## Comparing ingestion methods and tools

| Ingestion name | Data type | Maximum file size | Streaming, batching, direct | Most common scenarios | Considerations |
| --- | --- | --- | --- | --- | --- |
| [**One click ingestion**](ingest-data-one-click.md) | *sv, JSON | 1 GB uncompressed (see note)| Batching to container, local file and blob in direct ingestion | One-off, create table schema, definition of continuous ingestion with event grid, bulk ingestion with container (up to 10,000 blobs) | 10,000 blobs are randomly selected from container|
| [**LightIngest**](lightingest.md) | All formats supported | 1 GB uncompressed (see note) | Batching via DM or direct ingestion to engine |  Data migration, historical data with adjusted ingestion timestamps, bulk ingestion (no size restriction)| Case-sensitive, space-sensitive |
| [**ADX Kafka**](ingest-data-kafka.md) | | | | |
| [**ADX to Apache Spark**](spark-connector.md) | | | | |
| [**LogStash**](ingest-data-logstash.md) | | | | |
| [**Azure Data Factory (ADF)**](./data-factory-integration.md) | [Supported data formats](../../../data-factory/copy-activity-overview.md#supported-data-stores-and-formats) | unlimited *(per ADF restrictions) | Batching or per ADF trigger | Supports formats that are usually unsupported, large files, can copy from over 90 sources, from on perm to cloud | This method takes relatively more time until data is ingested. ADF uploads all data to memory and then begins ingestion. |
|[ **Power Automate**](./flow.md) | | | | Ingestion commands as part of flow| Must have high-performing response time |
| [**IoT Hub**](ingest-data-iot-hub-overview.md) | [Supported data formats](ingest-data-iot-hub-overview.md#data-format)  | N/A | Batching, streaming | IoT messages, IoT events, IoT properties | |
| [**Event Hub**](ingest-data-event-hub-overview.md) | [Supported data formats](ingest-data-event-hub-overview.md#data-format) | N/A | Batching, streaming | Messages, events | |
| [**Event Grid**](ingest-data-event-grid-overview.md) | [Supported data formats](ingest-data-event-grid-overview.md#data-format) | 1 GB uncompressed | Batching | Continuous ingestion from Azure storage, external data in Azure storage | Ingestion can be triggered by blob renaming or blob creation actions. |
| [**.NET SDK**](./net-sdk-ingest-data.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |
| [**Python**](python-ingest-data.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |
| [**Node.js**](node-ingest-data.md) | All formats supported | 1 GB uncompressed (see note | Batching, streaming, direct | Write your own code according to organizational needs |
| [**Java**](kusto/api/java/kusto-java-client-library.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |
| [**REST**](kusto/api/netfx/kusto-ingest-client-rest.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct| Write your own code according to organizational needs |
| [**Go**](kusto/api/golang/kusto-golang-client-library.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |

> [!Note]
> When referenced in the above table, ingestion supports a maximum file size of 4 GB. The recommendation is to ingest files between 100 MB and 1 GB. -->

## <a name="ingestion-process"></a>インジェスト プロセス

ニーズに最も適したインジェスト方法を選択したら、次の手順を実行します。

1. **保持ポリシーを設定する**

    Azure Synapse Data Explorer のテーブルに取り込まれたデータは、テーブルの有効な保持ポリシーの対象となります。 テーブルに明示的に設定しない限り、有効な保持ポリシーはデータベースの保持ポリシーから取得されます。 ホット リテンションは、クラスター サイズと保持ポリシーの機能です。 使用可能な領域よりも多くのデータを取り込むと、最初のデータにコールド リテンションが適用されます。

    データベースの保持ポリシーがご自分のニーズに適していることを確認してください。 適していない場合は、テーブル レベルで明示的にオーバーライドします。 詳細については、「[保持ポリシー](/azure/data-explorer/kusto/management/retentionpolicy?context=/azure/synapse-analytics/context/context)」を参照してください。

1. **テーブルの作成**

    データを取り込むためには、事前にテーブルを作成する必要があります。 次のいずれかのオプションを使用します。

    * コマンドを使用してテーブルを作成します。 テーブルの作成コマンドの使用例については、「[Data Explorer を使用して分析する](../../get-started-analyze-data-explorer.md)」を参照してください。

    * [ワンクリック インジェスト](data-explorer-ingest-data-one-click.md)を使用してテーブルを作成します。

    > [!Note]
    > レコードが不完全な場合、またはフィールドを必要なデータ型として解析できない場合は、対応するテーブル列に null 値が設定されます。

1. **スキーマ マッピングの作成**

    [スキーマ マッピング](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context)は、ソースのデータ フィールドをターゲットのテーブル列にバインドするのに役立ちます。 マッピングを使用すると、定義された属性に基づいて、異なるソースから同じテーブルにデータを取り込むことができます。 行指向 (CSV、JSON、AVRO) と列指向 (Parquet) の両方で、さまざまな種類のマッピングがサポートされています。 ほとんどの方法では、マッピングを[テーブルで事前に作成](/azure/data-explorer/kusto/management/create-ingestion-mapping-command?context=/azure/synapse-analytics/context/context)して、取り込みコマンドのパラメーターから参照することもできます。

1. **更新ポリシーの設定** (省略可能)

   一部のデータ形式マッピング (Parquet、JSON、Avro) では、簡単で便利な取り込み時の変換がサポートされています。 取り込み時により複雑な処理を必要とするシナリオでは、更新ポリシーを使用します。これにより、Kusto クエリ言語コマンドを使用した簡易処理が可能になります。 更新ポリシーでは、元のテーブルの取り込まれたデータに対して抽出と変換が自動的に実行され、結果のデータが 1 つ以上の宛先テーブルに取り込まれます。 [更新ポリシー](/azure/data-explorer/kusto/management/update-policy?context=/azure/synapse-analytics/context/context)を設定します。



## <a name="next-steps"></a>次のステップ

- [サポートされるデータ形式](data-explorer-ingest-data-supported-formats.md)
- [サポートされるインジェストのプロパティ](data-explorer-ingest-data-properties.md)