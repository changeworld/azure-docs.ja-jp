---
title: コピー アクティビティを監視する
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics でのコピー アクティビティの実行を監視する方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 14607231fd68cf78f33a21abdb26dd68e2ef6871
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131062190"
---
# <a name="monitor-copy-activity"></a>コピー アクティビティの監視

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Synapse パイプラインでのコピー アクティビティの実行を監視する方法の概要について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。  同じ方法を使用して、[データ コピー ツール](copy-data-tool.md)で生成されたコピー アクティビティや、[削除アクティビティ](delete-activity.md)を監視することもできます。

## <a name="monitor-visually"></a>視覚的な監視

パイプラインを作成して公開した後、それをトリガーに関連付けたり、アドホック実行を手動で開始したりできます。 ユーザー エクスペリエンスで、すべてのパイプラインの実行をネイティブに監視できます。 監視の全般については、「[Azure Data Factory と Synapse パイプラインを視覚的に監視する](monitor-visually.md)」を参照してください。

コピー アクティビティの実行を監視するには、ご利用のサービス インスタンスの **Data Factory Studio** または **Azure Synapse Studio** のUI に移動します。 **[監視]** タブに表示されるパイプライン実行の一覧で、**パイプライン名** のリンクをクリックして、パイプライン実行でのアクティビティの実行の一覧にアクセスします。

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="./media/copy-activity-overview/monitor-pipeline-run.png" alt-text="パイプラインの実行の監視":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

:::image type="content" source="./media/copy-activity-overview/monitor-pipeline-run-synapse.png" alt-text="パイプラインの実行の監視":::

---

このレベルでは、コピー アクティビティの入力、出力、エラー (コピー アクティビティの実行が失敗した場合) と、実行時間や状態などの統計情報へのリンクが表示されます。 コピー アクティビティ名の横にある **[詳細]** ボタン (眼鏡) をクリックすると、コピー アクティビティの実行に関する詳細が表示されます。 

:::image type="content" source="./media/copy-activity-overview/monitor-copy-activity-run.png" alt-text="コピー アクティビティの実行を監視する":::

このグラフィカルな監視ビューでは、サービスによってコピー アクティビティの実行の情報が表示されます。これには、データの読み取りと書き込みの量、ソースからシンクにコピーされたデータのファイルと行の数、スループット、コピー シナリオに適用された構成、コピー アクティビティで行われた手順および対応する実行時間と詳細などが含まれます。 各メトリックとその詳細な説明については、[こちらの表](#monitor-programmatically)を参照してください。 

シナリオによっては、コピー アクティビティを実行すると、例に示すように、コピー アクティビティ監視ページの上部に **[Performance tuning tips]\(パフォーマンス チューニングのヒント\)** が表示されます。 このヒントでは、この特定のコピーの実行について、サービスによって特定されたボトルネックと、何を変更するとコピーのスループットが向上するかについての提案が示されます。 詳しくは、[自動パフォーマンス チューニングのヒント](copy-activity-performance-troubleshooting.md#performance-tuning-tips)に関する記事を参照してください。

下部の **実行の詳細と実行時間** では、コピー アクティビティで行われた主要な手順が示されます。これは、コピーのパフォーマンスのトラブルシューティングに特に役立ちます。 コピー実行のボトルネックは、実行時間が最も長いものです。 各ステージの内容と詳細なトラブルシューティングのガイダンスについては、[「コピー アクティビティのパフォーマンスのトラブルシューティング](copy-activity-performance-troubleshooting.md)」を参照してください。

**例:Amazon S3 から Azure Data Lake Storage Gen2 にコピーする**

:::image type="content" source="./media/copy-activity-overview/monitor-copy-activity-run-details.png" alt-text="コピー アクティビティの実行状況の詳細の監視":::

## <a name="monitor-programmatically"></a>プログラムで監視する

コピー アクティビティの実行の詳細とパフォーマンス特性は、 **[Copy Activity run result]\(コピー アクティビティの実行結果\)**  >  **[出力]** セクションでも返されます。それを使用して、UI 監視ビューが表示されます。 返される可能性があるプロパティの詳細な一覧を次に示します。 コピー シナリオに適用できるプロパティのみが表示されます。 プログラムでアクティビティの実行を監視する方法の概要については、「[Azure Data Factory または Synapse パイプラインをプログラムで監視する](monitor-programmatically.md)」を参照してください。

| プロパティ名  | 説明 | 出力の単位 |
|:--- |:--- |:--- |
| dataRead | ソースから読み取られたデータの実際の量。 | Int64 値 (バイト単位) |
| dataWritten | シンクに書き込まれてコミットされたデータの実際の量。 このサイズは、各データ ストアでのデータの格納方法に関係するため、`dataRead` のサイズと異なる場合があります。 | Int64 値 (バイト単位) |
| filesRead | ファイル ベースのソースから読み取られたファイルの数。 | Int64 値 (単位なし) |
| filesWritten | ファイル ベースのシンクに書き込まれてコミットされたファイルの数。 | Int64 値 (単位なし) |
| filesSkipped | ファイル ベースのソースからスキップされたファイルの数。 | Int64 値 (単位なし) |
| dataConsistencyVerification | ソースとコピー先のストア間でコピーされたファイルの整合性が検証されているかどうかを確認する、データ整合性検証の詳細。 詳しくは、[こちらの記事](copy-activity-data-consistency.md#monitoring)をご覧ください。 | Array |
| sourcePeakConnections | コピー アクティビティの実行中にソース データ ストアに対して確立されたコンカレント接続の最大数。 | Int64 値 (単位なし) |
| sinkPeakConnections | コピー アクティビティの実行中にシンク データ ストアに対して確立されたコンカレント接続の最大数。 | Int64 値 (単位なし) |
| rowsRead | ソースから読み取られた行の数。 このメトリックは、ファイルを解析せずにそのままコピーする場合には適用されません (たとえば、ソースとシンクのデータセットがバイナリ形式の場合や、同じ設定を持つ他の形式の種類の場合)。 | Int64 値 (単位なし) |
| rowsCopied | シンクにコピーされた行の数。 このメトリックは、ファイルを解析せずにそのままコピーする場合には適用されません (たとえば、ソースとシンクのデータセットがバイナリ形式の場合や、同じ設定を持つ他の形式の種類の場合)。  | Int64 値 (単位なし) |
| rowsSkipped | スキップされた互換性のない行の数。 `enableSkipIncompatibleRow` を true に設定することにより、互換性のない行をスキップすることができます。 | Int64 値 (単位なし) |
| copyDuration | コピーの実行の持続期間。 | Int32 値 (秒単位) |
| throughput | `dataRead` を `copyDuration` で除算して計算されたデータ転送の比率。 | 浮動小数点数 (KB/秒単位) |
| sourcePeakConnections | コピー アクティビティの実行中にソース データ ストアに対して確立されたコンカレント接続の最大数。 | Int32 値 (単位なし) |
| sinkPeakConnections| コピー アクティビティの実行中にシンク データ ストアに対して確立されたコンカレント接続の最大数。| Int32 値 (単位なし) |
| sqlDwPolyBase | データを Azure Synapse Analytics にコピーするときに PolyBase を使用するかどうかを指定します。 | Boolean |
| redshiftUnload | Redshift からデータをコピーするときに UNLOAD を使用するかどうかを指定します。 | Boolean |
| hdfsDistcp | HDFS からデータをコピーするときに DistCp を使用するかどうかを指定します。 | Boolean |
| effectiveIntegrationRuntime | アクティビティの実行を強化するために使用される統合ランタイム (IR) またはランタイム (`<IR name> (<region if it's Azure IR>)` 形式)。 | Text (文字列) |
| usedDataIntegrationUnits | コピーの間に有効なデータ統合単位。 | Int32 値 |
| usedParallelCopies | コピー中の効率的な parallelCopies。 | Int32 値 |
| logPath | BLOB ストレージ内のスキップされたデータのセッション ログへのパス。 「[フォールト トレランス](copy-activity-overview.md#fault-tolerance)」を参照してください。 | Text (文字列) |
| executionDetails | コピー アクティビティによって実行されるステージと、対応する手順、期間、構成などに関する詳細情報。 変更される可能性があるため、このセクションを解析することはお勧めしません。 コピーのパフォーマンスの理解とトラブルシューティングに役立てる方法について詳しくは、「[視覚的な監視](#monitor-visually)」セクションを参照してください。 | Array |
| perfRecommendation | コピー パフォーマンスのチューニングのヒント。 詳細については、「[パフォーマンスのチューニングのヒント](copy-activity-performance-troubleshooting.md#performance-tuning-tips)」を参照してください。 | Array |
| billingReference | 指定された実行の使用量に応じた課金。 詳しくは、「[アクティビティ実行レベルでの使用量を監視する](plan-manage-costs.md#monitor-consumption-at-activity-run-level)」をご覧ください。 | Object |
| durationInQueue | コピー アクティビティの実行が開始されるまでキューに入れる期間 (秒単位)。 | Object |

**例:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "filesSkipped": 0,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "dataConsistencyVerification": 
    { 
        "VerificationResult": "Verified", 
        "InconsistentData": "None" 
    },
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>次のステップ
コピー アクティビティの他の記事を参照してください。

\- [コピー アクティビティの概要](copy-activity-overview.md)

\- [コピー アクティビティのパフォーマンス](copy-activity-performance.md)
