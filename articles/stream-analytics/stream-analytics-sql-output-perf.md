---
title: Azure SQL Database への Azure Stream Analytics の出力
description: Azure Stream Analytics から Azure SQL Database にデータを出力し、より高い書き込みスループット レートを実現する方法について説明します。
services: stream-analytics
author: chetang
ms.author: chetang
manager: katicad
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/21/2018
ms.openlocfilehash: 623d03c96866392ef245fb924cbf6600e7850ffe
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47057818"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Azure SQL Database への Azure Stream Analytics の出力

この記事では、Azure Stream Analytics を使用して SQL Azure Database にデータを読み込むときに、よりよい書き込みスループット パフォーマンスを実現するためのヒントについて説明します。

Azure Stream Analytics の SQL 出力では、オプションとして並列書き込みがサポートされます。 このオプションでは[完全に並列](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)なジョブ トポロジが可能で、複数の出力パーティションが書き込み先テーブルに並列で書き込まれます。 ただし、Azure Stream Analytics でこのオプションを有効にするだけでは、より高いスループットを実現するのに十分ではありません。スループットは、SQL Azure データベースの構成とテーブル スキーマに大きく依存しているためです。 インデックス、クラスタリング キー、インデックスの FILL FACTOR、および圧縮の選択が、テーブルを読み込む時間に影響します。 内部ベンチマークに基づいて SQL Azure データベースを最適化してクエリと読み込みのパフォーマンスを向上させる方法について詳しくは、[SQL データベースのパフォーマンス ガイダンス](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)に関するページをご覧ください。

ここでは、ソリューションの全体的なスループットの向上に役立つ各サービス内のいくつかの構成を示します。

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **パーティション分割の継承** – この SQL 出力構成オプションを使用すると、前のクエリ ステップや入力のパーティション構成を継承できます。 これを有効にして、ディスク ベースのテーブルに書き込み、ジョブを[完全並列](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)トポロジにすると、スループットの向上を期待できます。 他の多くの[出力](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks)に対しては、このパーティション分割は既に自動的に行われています。 このオプションで行われる一括挿入に対しては、テーブル ロック (TABLOCK) も無効になります。

> [!NOTE] 
> 入力パーティションが 8 個より多い場合、入力パーティション構成の継承は適切な選択ではない可能性があります。 この上限は、ID 列とクラスター化インデックスが 1 つだけのテーブルにおいて観察されたものです。 スキーマとインデックスの選択によっては、結果が異なる場合があります。

- **バッチ サイズ** - SQL の出力構成では、書き込み先テーブル/ワークロードの特性に基づいて、Azure Stream Analytics SQL 出力の最大バッチ サイズを指定することができます。 バッチ サイズは、すべての一括挿入トランザクションで送信されるレコードの最大数です。 クラスター化された列ストア インデックスでは、バッチ サイズを約 [100 K](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) にすると、並列化を高くし、ログ記録を最小にし、ロックを最適にできます。 ディスク ベースのテーブルでは、10 K (既定値) 以下にするとソリューションに最適な場合があります。バッチ サイズを大きくすると、一括挿入中にロックのエスカレーションがトリガーされる可能性があります。

- **入力メッセージのチューニング** – パーティション分割の継承とバッチ サイズの使用を最適化している場合、1 つのパーティションのメッセージあたりの入力イベントの数を増やすと、書き込みスループットをさらに上げるのに役立ちます。 入力メッセージのチューニングにより、Azure Stream Analytics 内のバッチ サイズを指定したバッチ サイズまで上げることができ、それによってスループットが向上します。 これは、[圧縮](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-inputs)を使用して、または Premium EventHub SKU で利用可能な大きいメッセージ サイズを使用して、実現できます。

## <a name="sql-azure"></a>SQL Azure

- **パーティション テーブルとパーティション インデックス** – パーティション キー (たとえば PartitionId) と同じ列を含むテーブルで[パーティション分割された](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) SQL テーブルとパーティション分割されたインデックスを使用すると、書き込み中のパーティション間の競合を大幅に減らすことができます。 パーティション テーブルの場合は、プライマリ ファイル グループに[パーティション関数](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017)と[パーティション構成](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017)を作成する必要があります。 これにより、新しいデータの読み込み中の既存データの可用性も向上します。 パーティションの数によってはログ IO の上限に達する可能性があり、これは SKU をアップグレードすることで増やすことができます。

- **一意キー違反の回避** – Azure Stream Analytics のアクティビティ ログで[複数キー違反の警告メッセージ](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-common-troubleshooting-issues#handle-duplicate-records-in-azure-sql-database-output)が発生する場合は、復旧の間に発生する可能性がある一意制約違反によってジョブが影響を受けていないことを確認します。 インデックスに対して [IGNORE\_DUP\_KEY](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-common-troubleshooting-issues#handle-duplicate-records-in-azure-sql-database-output) オプションを設定することでこれを回避できます。

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory とインメモリ テーブル

- **一時テーブルとしてのインメモリ テーブル** – [インメモリ テーブル](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)を使用すると、非常に高速のデータ読み込みが可能ですが、データがメモリに収まる必要があります。 ベンチマークでは、インメモリ テーブルからディスク ベースのテーブルへの一括読み込みの方が、ID 列とクラスター化インデックスのあるディスク ベースのテーブルへの単一ライターを使用した一括挿入より、約 10 倍速いことが示されています。 この一括挿入のパフォーマンスを利用するには、インメモリ テーブルからディスク ベースのテーブルにデータをコピーする、[Azure Data Factory を使用するコピー ジョブ](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database)を設定します。

## <a name="summary"></a>まとめ

まとめると、SQL 出力に対する Azure Stream Analytics でのパーティション分割された出力機能では、SQL Azure 内のパーティション テーブルに合わせて、ジョブの並列化を調整することにより、スループットが大幅に向上します。 インメモリ テーブルからディスク ベースのテーブルへのデータの移動の調整に Azure Data Factory を利用すると、1 桁高いスループットが得られます。 可能であれば、メッセージの密度を上げることも、全体的なスループットの向上において重要な要素になる可能性があります。
