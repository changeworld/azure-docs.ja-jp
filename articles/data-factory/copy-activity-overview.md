---
title: Azure Data Factory のコピー アクティビティ
description: Azure Data Factory のコピー アクティビティについて説明します。 サポートされているソース データ ストアからサポートされているシンク データ ストアにデータをコピーするために使用できます。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: jingwang
ms.openlocfilehash: 0e138e954501df3cf3c3c8819d0198ad9a9288f0
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754465"
---
# <a name="copy-activity-in-azure-data-factory"></a>Azure Data Factory のコピー アクティビティ

> [!div class="op_single_selector" title1="使用している Data Factory のバージョンを選択してください。"]
> * [Version 1](v1/data-factory-data-movement-activities.md)
> * [現在のバージョン](copy-activity-overview.md)

Azure Data Factory では、コピー アクティビティを使用して、オンプレミスにあるデータ ストアやクラウド内のデータ ストアの間でデータをコピーできます。 データをコピーした後は、他のアクティビティを使用してさらに変換および分析できます。 また、コピー アクティビティを使用して、変換や分析の結果を発行し、ビジネス インテリジェンス (BI) やアプリケーションで使用することもできます。

![コピー アクティビティの役割](media/copy-activity-overview/copy-activity.png)

コピー アクティビティは、[統合ランタイム](concepts-integration-runtime.md)で実行されます。 さまざまなデータ コピーのシナリオで、さまざまな種類の統合ランタイムを使用できます。

* 任意の IP からインターネット経由でパブリックにアクセスできる 2 つのデータ ストア間でデータをコピーする場合は、コピー アクティビティに Azure 統合ランタイムを使用できます。 この統合ランタイムは、セキュリティで保護され、信頼性が高く、スケーラブルで、[グローバルに利用できます](concepts-integration-runtime.md#integration-runtime-location)。
* オンプレミス、またはアクセス制御を使用するネットワーク (Azure 仮想ネットワークなど) に配置されているデータ ストアとの間でデータをコピーする場合は、セルフホステッド統合ランタイムを設定する必要があります。

統合ランタイムを各ソースおよびシンク データ ストアに関連付ける必要があります。 使用する統合ランタイムをコピー アクティビティで判別する方法の詳細については、「[使用する IR の判別](concepts-integration-runtime.md#determining-which-ir-to-use)」を参照してください。

ソースからシンクにデータをコピーするために、コピー アクティビティを実行するサービスでは次の手順が実行されます。

1. ソース データ ストアからデータを読み取る。
2. シリアル化/逆シリアル化、圧縮/圧縮解除、列マッピングなどを実行する。 この操作は、入力データセット、出力データセット、およびコピー アクティビティの構成に基づいて実行されます。
3. シンク/宛先データ ストアにデータを書き込む。

![コピー アクティビティの概要](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>サポートされるデータ ストアと形式

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>サポートされるファイル形式

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

コピー アクティビティを使用すると、ファイル ベースの 2 つのデータ ストア間でファイルをそのままコピーできます。その場合、データはシリアル化または逆シリアル化なしで効率的にコピーされます。 また、特定の形式のファイルを解析または生成することもできます。たとえば、次のような操作を実行できます。

* オンプレミスの SQL Server データベースからデータをコピーし、Parquet 形式で Azure Data Lake Storage Gen2 に書き込む。
* オンプレミスのファイル システムからテキスト (CSV) 形式でファイルをコピーし、Azure BLOB ストレージに Avro 形式で書き込む。
* オンプレミスのファイル システムから zip 形式のファイルをコピーし、その場で圧縮解除して、抽出されたファイルを Azure Data Lake Storage Gen2 に書き込む。
* Azure BLOB ストレージから Gzip 圧縮テキスト (CSV) 形式でデータをコピーし、Azure SQL Database に書き込む。
* シリアル化/逆シリアル化または圧縮/展開を必要とする他の多くのアクティビティ。

## <a name="supported-regions"></a>サポートされているリージョン

コピー アクティビティが有効なサービスは、「[統合ランタイムの場所](concepts-integration-runtime.md#integration-runtime-location)」に記載されているリージョンと場所でグローバルに使うことができます。 グローバルに使用できるトポロジでは効率的なデータ移動が保証されます。このデータ移動では、通常、リージョンをまたがるホップが回避されます。 特定のリージョンで Data Factory とデータ移動を利用できるかどうかを確認するには、[リージョン別の製品](https://azure.microsoft.com/regions/#services)に関する記事を参照してください。

## <a name="configuration"></a>構成

Azure Data Factory でコピー アクティビティを使用するには、次のことを行う必要があります。

1. **ソース データ ストアとシンク データ ストアのリンクされたサービスを作成します。** 構成情報とサポートされるプロパティについては、コネクタの記事のリンクされたサービスのプロパティに関するセクションを参照してください。 サポートされるコネクタの一覧については、この記事の「[サポートされるデータ ストアと形式](#supported-data-stores-and-formats)」セクションを参照してください。
2. **ソースとシンクのデータセットを作成します。** 構成情報とサポートされるプロパティについては、ソースとシンク コネクタの記事のデータセットのプロパティに関するセクションを参照してください。
3. **コピー アクティビティを含むパイプラインを作成します。** 次のセクションでは、例を示します。

### <a name="syntax"></a>構文

次のコピー アクティビティのテンプレートは、サポートされるすべてのプロパティの一覧を示しています。 実際のシナリオに適したものを指定してください。

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>構文の詳細

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| 型 | コピー アクティビティの場合は、`Copy` に設定します。 | はい |
| inputs | ソース データを指すように作成したデータセットを指定します。 コピー アクティビティは、1 つの入力のみをサポートします。 | はい |
| outputs | シンク データを指すように作成したデータセットを指定します。 コピー アクティビティは、1 つの出力のみをサポートします。 | はい |
| typeProperties | コピー アクティビティを構成するプロパティを指定します。 | はい |
| source | データを取得するためのコピー ソースの種類と対応するプロパティを指定します。<br/>詳細については、「[サポートされるデータ ストアと形式](#supported-data-stores-and-formats)」に記載されているコネクタの記事のコピー アクティビティのプロパティに関するセクションを参照してください。 | はい |
| sink | データを書き込むためのコピー シンクの種類と対応するプロパティを指定します。<br/>詳細については、「[サポートされるデータ ストアと形式](#supported-data-stores-and-formats)」に記載されているコネクタの記事のコピー アクティビティのプロパティに関するセクションを参照してください。 | はい |
| translator | ソースからシンクへの明示的な列マッピングを指定します。 このプロパティは、既定のコピー動作がニーズに合わない場合に適用されます。<br/>詳細については、「[コピー アクティビティでのスキーマ マッピング](copy-activity-schema-and-type-mapping.md)」を参照してください。 | いいえ |
| dataIntegrationUnits | [Azure 統合ランタイム](concepts-integration-runtime.md)がデータのコピーに使用する機能の量を表す単位を指定します。 これらの単位は、以前はクラウド データ移動単位 (DMU) と呼ばれていました。 <br/>詳細については、「[データ統合単位](copy-activity-performance.md#data-integration-units)」を参照してください。 | いいえ |
| parallelCopies | ソースからのデータの読み取り時やシンクへのデータの書き込み時にコピー アクティビティで使用する並列処理を指定します。<br/>詳細については、「[並列コピー](copy-activity-performance.md#parallel-copy)」を参照してください。 | いいえ |
| preserve | データのコピー中にメタデータ/ACL を保存するかどうかを指定します。 <br/>詳細については、[メタデータの保存](copy-activity-preserve-metadata.md)に関する記事を参照してください。 |いいえ |
| enableStaging<br/>stagingSettings | ソースからシンクにデータを直接コピーするのではなく、BLOB ストレージに中間データをステージングするかどうかを指定します。<br/>役に立つシナリオと構成の詳細については、「[ステージング コピー](copy-activity-performance.md#staged-copy)」を参照してください。 | いいえ |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| ソースからシンクにデータをコピーするときに互換性のない行を処理する方法を選択します。<br/>詳細については、「[フォールト トレランス](copy-activity-fault-tolerance.md)」を参照してください。 | いいえ |

## <a name="monitoring"></a>監視

Azure Data Factory の **[Author & Monitor]\(作成と監視\)** という UI またはプログラムで、コピー アクティビティの実行を監視できます。

### <a name="monitor-visually"></a>視覚的な監視

コピー アクティビティの実行を視覚的に監視するには、データ ファクトリに移動し、 **[Author & Monitor]\(作成と監視\)** に移動します。 **[監視]** タブで **[アクション]** 列の **[View Activity Run]\(アクティビティの実行の表示\)** ボタンを使用して、パイプラインの実行の一覧を表示します。

![パイプラインの実行を監視する](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

**[View Activity Run]\(アクティビティの実行の表示\)** を選択すると、パイプラインの実行に含まれるアクティビティの一覧が表示されます。 **[アクション]** 列に、コピー アクティビティの入力、出力、エラー (コピー アクティビティの実行に失敗した場合) のリンクと詳細が表示されます。

![アクティビティの実行を監視する](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

**[アクション]** 列の **[詳細]** ボタンを選択すると、コピー アクティビティの実行の詳細とパフォーマンスの特性が表示されます。 ソースからシンクにコピーされたデータのボリューム/行数/ファイル数、スループット、コピー アクティビティが対応する期間で実行する手順、コピー シナリオに使用される構成などの情報が表示されます。

>[!TIP]
>シナリオによっては、コピーの監視ページの上部に **[Performance tuning tips]\(パフォーマンス チューニングのヒント\)** が表示されることもあります。 これらのヒントから、特定されたボトルネックがわかり、コピーのスループットを向上させるために変更すべきことに関する情報が提供されます。 例については、この記事の「[パフォーマンスとチューニング](#performance-and-tuning)」セクションを参照してください。

**例:Amazon S3 から Azure Data Lake Store へコピーする**
![アクティビティの実行の詳細を監視する](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**例:ステージング コピーを使用して Azure SQL Database から Azure SQL Data Warehouse にコピーする**
![アクティビティの実行の詳細を監視する](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>プログラムで監視する

コピー アクティビティの実行の詳細とパフォーマンス特性は、 **[Copy Activity run result]\(コピー アクティビティの実行結果\)**  >  **[出力]** セクションでも返されます。 返される可能性があるプロパティの詳細な一覧を次に示します。 コピー シナリオに適用できるプロパティのみが表示されます。 アクティビティの実行を監視する方法の詳細については、「[パイプラインの実行を監視する](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run)」を参照してください。

| プロパティ名  | [説明] | ユニット |
|:--- |:--- |:--- |
| dataRead | ソースから読み取られたデータの量。 | Int64 値 (バイト単位) |
| dataWritten | シンクに書き込まれたデータの量。 | Int64 値 (バイト単位) |
| filesRead | ファイル ストレージからのコピー中にコピーされたファイルの数。 | Int64 値 (単位なし) |
| filesWritten | ファイル ストレージへのコピー中にコピーされたファイルの数。 | Int64 値 (単位なし) |
| sourcePeakConnections | コピー アクティビティの実行中にソース データ ストアに対して確立されたコンカレント接続の最大数。 | Int64 値 (単位なし) |
| sinkPeakConnections | コピー アクティビティの実行中にシンク データ ストアに対して確立されたコンカレント接続の最大数。 | Int64 値 (単位なし) |
| rowsRead | ソースから読み取られた行の数 (バイナリ コピーには適用されません)。 | Int64 値 (単位なし) |
| rowsCopied | シンクにコピーされた行数 (バイナリ コピーには適用されません)。 | Int64 値 (単位なし) |
| rowsSkipped | スキップされた互換性のない行の数。 `enableSkipIncompatibleRow` を true に設定することにより、互換性のない行をスキップすることができます。 | Int64 値 (単位なし) |
| copyDuration | コピーの実行の持続期間。 | Int32 値 (秒単位) |
| throughput | データ転送率。 | 浮動小数点数 (KB/秒単位) |
| sourcePeakConnections | コピー アクティビティの実行中にソース データ ストアに対して確立されたコンカレント接続の最大数。 | Int32 値 (単位なし) |
| sinkPeakConnections| コピー アクティビティの実行中にシンク データ ストアに対して確立されたコンカレント接続の最大数。| Int32 値 (単位なし) |
| sqlDwPolyBase | データを SQL Data Warehouse にコピーするときに PolyBase を使用するかどうかを指定します。 | Boolean |
| redshiftUnload | Redshift からデータをコピーするときに UNLOAD を使用するかどうかを指定します。 | Boolean |
| hdfsDistcp | HDFS からデータをコピーするときに DistCp を使用するかどうかを指定します。 | Boolean |
| effectiveIntegrationRuntime | アクティビティの実行を強化するために使用される統合ランタイム (IR) またはランタイム (`<IR name> (<region if it's Azure IR>)` 形式)。 | Text (文字列) |
| usedDataIntegrationUnits | コピーの間に有効なデータ統合単位。 | Int32 値 |
| usedParallelCopies | コピー中の効率的な parallelCopies。 | Int32 値 |
| redirectRowPath | `redirectIncompatibleRowSettings` プロパティで構成する BLOB ストレージ内のスキップされた互換性のない行のログへのパス。 この記事で後述する「[フォールト トレランス](#fault-tolerance)」を参照してください。 | Text (文字列) |
| executionDetails | コピー アクティビティによって実行されるステージと、対応する手順、期間、構成などに関する詳細情報。 変更される可能性があるため、このセクションを解析することはお勧めしません。<br/><br/>Data Factory では、`detailedDurations` のさまざまなステージに費やされた詳細な期間 (秒単位) も報告されます。 これらの手順の期間は排他的です。 特定のコピー アクティビティの実行に適用される期間のみが表示されます。<br/>**キュー時間** (`queuingDuration`):コピー アクティビティが統合ランタイムで実際に開始されるまでの時間。 セルフホステッド IR を使用していて、この値が大きい場合は、IR の容量と使用量を確認し、ワークロードに応じてスケールアップまたはスケールアウトします。 <br/>**コピー前スクリプト時間** (`preCopyScriptDuration`):コピー アクティビティが IR で開始されてから、コピー アクティビティでシンク データ ストア内のコピー前スクリプトの実行が完了するまでの経過時間。 コピー前スクリプトを構成するときに適用されます。 <br/>**1 バイト目にかかる時間** (`timeToFirstByte`):前の手順が終了してから、IR がソース データ ストアから最初のバイトを受信するまでの経過時間。 ファイルベース以外のソースに適用されます。 この値が大きい場合は、クエリまたはサーバーを確認して最適化します。<br/>**転送時間** (`transferDuration`):前の手順が終了してから、IR ですべてのデータがソースからシンクに転送されるまでの経過時間。 | Array |
| perfRecommendation | コピー パフォーマンスのチューニングのヒント。 詳細については、「[パフォーマンスとチューニング](#performance-and-tuning)」を参照してください。 | Array |

```json
"output": {
    "dataRead": 6198358,
    "dataWritten": 19169324,
    "filesRead": 1,
    "sourcePeakConnections": 1,
    "sinkPeakConnections": 2,
    "rowsRead": 39614,
    "rowsCopied": 39614,
    "copyDuration": 1325,
    "throughput": 4.568,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 4,
    "usedParallelCopies": 1,
    "executionDetails": [
        {
            "source": {
                "type": "AzureBlobStorage"
            },
            "sink": {
                "type": "AzureSqlDatabase"
            },
            "status": "Succeeded",
            "start": "2019-08-06T01:01:36.7778286Z",
            "duration": 1325,
            "usedDataIntegrationUnits": 4,
            "usedParallelCopies": 1,
            "detailedDurations": {
                "queuingDuration": 2,
                "preCopyScriptDuration": 12,
                "transferDuration": 1311
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "Sink Azure SQL Database: The DTU utilization was high during the copy activity run. To achieve better performance, you are suggested to scale the database to a higher tier than the current 1600 DTUs.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2043368",
            "RuleName": "AzureDBTierUpgradePerfRecommendRule"
        }
    ]
}
```

## <a name="incremental-copy"></a>増分コピー

Data Factory を使用すると、ソース データ ストアからシンク データ ストアに差分データを増分コピーできます。 詳細については、[チュートリアルのデータの増分コピー](tutorial-incremental-copy-overview.md)に関する記事を参照してください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング

「[コピー アクティビティのパフォーマンスとスケーラビリティに関するガイド](copy-activity-performance.md)」には、Azure Data Factory でのコピー アクティビティによるデータ移動のパフォーマンスに影響する主な要因が説明されています。 また、テスト時に観察されるパフォーマンス値の一覧が示され、コピー アクティビティのパフォーマンスを最適化する方法も説明されます。

シナリオによっては、Data Factory でコピー アクティビティを実行するときに、次の例に示すように、[[Copy activity monitoring]\(コピー アクティビティの監視\)](#monitor-visually) ページの上部に **[Performance tuning tips]\(パフォーマンスチューニングのヒント\)** が表示されます。 このヒントから、指定されたコピーの実行で特定されたボトルネックがわかります。 また、コピーのスループットを向上させるために変更すべきことに関する情報も提供されます。 現在、パフォーマンス チューニングのヒントには、Azure SQL Data Warehouse にデータをコピーするときに PolyBase を使用する、データ ストア側のリソースがボトルネックになっているときに Azure Cosmos DB RU または Azure SQL Database DTU を増やす、不要なステージングされたコピーを削除するなどの提案が表示されます。

**例:パフォーマンス チューニングのヒントを参考にして Azure SQL Database にコピーする**

このサンプルでは、コピーの実行時に、Data Factory ではシンク Azure SQL Database で高い DTU 使用率が追跡されます。 この条件により、書き込み操作が遅くなります。 Azure SQL Database 層の DTU を増やすことが提案されます。

![パフォーマンスのチューニングに関するヒントを使用したコピーの監視](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="resume-from-last-failed-run"></a>前回失敗した実行から再開する

コピー アクティビティでは、ファイル ベースのストア間でバイナリ形式を使用してサイズの大きいファイルをそのままコピーする場合に、ソースからシンクへのフォルダー/ファイル階層を保持することを選択した場合 (Amazon S3 から Azure Data Lake Storage Gen2 にデータを移行する場合など)、前回失敗した実行からの再開をサポートします。 これは、次のファイルベース コネクタに適用されます。[Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure File Storage](connector-azure-file-storage.md)、[ファイル システム](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、および [SFTP](connector-sftp.md)。

コピー アクティビティの再開は、次の 2 つの方法で利用できます。

- **アクティビティ レベルの再試行:** コピー アクティビティに再試行回数を設定できます。 パイプラインの実行中に、このコピー アクティビティの実行が失敗した場合、次の自動再試行は最後の試行の失敗ポイントから開始されます。
- **失敗したアクティビティから再実行する:** パイプラインの実行完了後、ADF UI 監視ビューまたはプログラムによって失敗したアクティビティから再実行をトリガーすることもできます。 失敗したアクティビティがコピー アクティビティの場合、パイプラインはそのアクティビティから再実行されるだけでなく、前の実行の失敗ポイントからも再開されます。

    ![コピーの再開](media/copy-activity-overview/resume-copy.png)

いくつかの注意点があります。

- 再開は、ファイル レベルで行われます。 ファイルのコピー時にコピー アクティビティが失敗した場合、次回の実行時に、この特定のファイルが再コピーされます。
- 再開が正常に機能するには、再実行の間でコピー アクティビティの設定を変更しないでください。
- Amazon S3、Azure BLOB、Azure Data Lake Storage Gen2、および Google Cloud Storage からデータをコピーする場合、コピー アクティビティは任意の数のコピーされたファイルから再開できます。 ソースとしてのその他のファイル ベースのコネクタの場合、現在のコピー アクティビティは、限られた数のファイルからの再開をサポートしています。通常は 1 万単位の範囲であり、ファイル パスの長さによって異なります。この数を超えるファイルが再実行中に再コピーされます。

バイナリ ファイル コピー以外の他のシナリオでは、コピー アクティビティの再実行は先頭から開始されます。

## <a name="preserve-metadata-along-with-data"></a>データと共にメタデータを保存する

ソースからシンクへデータをコピーするときに、データ レイクの移行のようなシナリオでは、コピー アクティビティを使用して、メタデータと ACL をデータと共に保存することも選択できます。 詳細については、[メタデータの保存](copy-activity-preserve-metadata.md)に関する記事を参照してください。

## <a name="schema-and-data-type-mapping"></a>スキーマとデータ型のマッピング

コピー アクティビティによってソース データがどのようにシンクにマップされるかについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

## <a name="fault-tolerance"></a>フォールト トレランス

既定では、ソース データ行がシンク データ行と互換性がない場合、コピー アクティビティでデータのコピーが停止され、エラーが返されます。 コピーを成功させるには、互換性のない行をスキップし、ログに記録し、互換性のあるデータのみをコピーするようにコピー アクティビティを構成します。 詳細については、[コピー アクティビティのフォールト トレランス](copy-activity-fault-tolerance.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ
次のクイック スタート、チュートリアル、およびサンプルを参照してください。

- [ある場所から同じ Azure Blob Storage アカウントの別の場所にデータをコピーする](quickstart-create-data-factory-dot-net.md)
- [Azure Blob Storage から Azure SQL Database にデータをコピーする](tutorial-copy-data-dot-net.md)
- [オンプレミスの SQL Server データベースから Azure にデータをコピーする](tutorial-hybrid-copy-powershell.md)
