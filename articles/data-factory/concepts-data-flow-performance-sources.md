---
title: マッピング データ フローでのソースのパフォーマンスの最適化
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory および Azure Synapse Analytics パイプラインのマッピング データ フローでのソースのパフォーマンスの最適化について説明します。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 7fef72d1ca52ae04106797c8ce7088b259aad033
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293511"
---
# <a name="optimizing-sources"></a>ソースの最適化

Azure SQL Database を除くすべてのソースについては、 **[Use current partitioning]\(現在のパーティションを使用する\)** を選択された値としてそのまま使用することをお勧めします。 他のすべてのソース システムから読み取る場合、データ フローでは、データのサイズに基づいて自動的にデータが均等にパーティション分割されます。 データ 128 MB ごとに新しいパーティションが作成されます。 データのサイズが増加するにつれて、パーティションの数が増加します。

カスタム パーティション分割は、Spark にデータが読み取られた "*後*" に発生し、データ フローのパフォーマンスに悪影響を与えます。 読み取り時にデータが均等にパーティション分割されるため、この方法はお勧めしません。 

> [!NOTE]
> 読み取り速度は、ソース システムのスループットによって制限される場合があります。

## <a name="azure-sql-database-sources"></a>Azure SQL Database のソース

Azure SQL Database には、"ソース" パーティション分割と呼ばれる独自のパーティション分割オプションがあります。 ソースのパーティション分割を有効にした場合、ソース システムで並列接続を有効にすることで、Azure SQL DB からの読み取り時間を短縮できます。 パーティションの数とデータをパーティション分割する方法を指定します。 カーディナリティが高いパーティション列を使用します。 また、ソース テーブルのパーティション構成に一致するクエリを入力することもできます。

> [!TIP]
> ソースのパーティション分割では、SQL Server の I/O がボトルネックになります。 追加するパーティションが多すぎると、ソース データベースが飽和状態になる可能性があります。 通常、このオプションを使用する場合、4 つまたは 5 つのパーティションが理想的です。

:::image type="content" source="media/data-flow/sourcepart3.png" alt-text="ソースのパーティション分割":::

### <a name="isolation-level"></a>分離レベル

Azure SQL ソース システムでの読み取りの分離レベルは、パフォーマンスに影響します。 [コミットされていないものを読み取り] を選択すると、パフォーマンスが最速になり、データベース ロックが防止されます。 SQL 分離レベルの詳細については、「[分離レベルについて](/sql/connect/jdbc/understanding-isolation-levels)」を参照してください。

### <a name="read-using-query"></a>クエリを使用した読み取り

テーブルまたは SQL クエリを使用して Azure SQL Database から読み取ることができます。 SQL クエリを実行する場合は、変換を開始する前にクエリが完了する必要があります。 SQL クエリは、より高速に実行される可能性がある操作をプッシュ ダウンし、SELECT、WHERE、JOIN ステートメントなどの、SQL Server から読み取るデータの量を減らすのに役立ちます。 操作をプッシュ ダウンすると、データがデータ フローに取り込まれる前に、変換の系列とパフォーマンスを追跡できなくなります。

## <a name="azure-synapse-analytics-sources"></a>Azure Synapse Analytics のソース

Azure Synapse Analytics を使用するとき、ソース オプションに **[Enable staging]\(ステージングの有効化\)** という設定があります。 これにより、サービスでは ```Staging``` を使用して Synapse から読み取りを実行できます。これにより、最もパフォーマンスの高い一括読み込み機能に対して [Synapse COPY ステートメント](/sql/t-sql/statements/copy-into-transact-sql) コマンドを使用することで、読み取りパフォーマンスが大幅に向上します。 ```Staging``` を有効にするには、データ フロー アクティビティの設定で Azure Blob Storage または Azure Data Lake Storage gen2 ステージングの場所を指定する必要があります。

:::image type="content" source="media/data-flow/enable-staging.png" alt-text="ステージングの有効化":::

## <a name="file-based-sources"></a>ファイルベースのソース

さまざまな種類のファイルがデータ フローでサポートされていますが、読み取りと書き込みを最適に行うために、Spark ネイティブの Parquet 形式を使用することをお勧めします。

一連のファイルで同じデータ フローを実行している場合は、ワイルドカード パスを使用して、またはファイルの一覧から読み取ることで、フォルダーから読み取ることをお勧めします。 1 つのデータ フロー アクティビティの実行で、バッチ内のすべてのファイルを処理できます。 これらの設定を構成する方法の詳細については、[Azure Blob Storage コネクタ](connector-azure-blob-storage.md#source-transformation) ドキュメントの「**ソース変換**」セクションを参照してください。

可能な場合、一連のファイルに対し、For Each アクティビティを使用してデータ フローを実行することは避けてください。 for-each の各反復処理に専用の Spark クラスターが作成されることになるのですが、これは必要でないことが多く、コストが高くなる可能性があります。 

## <a name="next-steps"></a>次のステップ

- [データ フローのパフォーマンスの概要](concepts-data-flow-performance.md)
- [シンクの最適化](concepts-data-flow-performance-sinks.md)
- [変換の最適化](concepts-data-flow-performance-transformations.md)
- [パイプラインでのデータ フローの使用](concepts-data-flow-performance-pipelines.md)

パフォーマンスに関する Data Flow のその他の記事を参照してください。

- [Data Flow のアクティビティ](control-flow-execute-data-flow-activity.md)
- [データ フローのパフォーマンスの監視](concepts-data-flow-monitoring.md)
- [Integration Runtime のパフォーマンス](concepts-integration-runtime-performance.md)
