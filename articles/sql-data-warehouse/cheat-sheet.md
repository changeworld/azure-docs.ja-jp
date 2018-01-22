---
title: "Azure SQL Data Warehouse のチート シート | Microsoft Docs"
description: "Azure SQL Data Warehouse ソリューションをすばやく構築するためのリンクとベスト プラクティスが見つかります。"
services: sql-data-warehouse
documentationcenter: NA
author: acomet
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 12/14/2017
ms.author: acomet
ms.openlocfilehash: 2d17385ff255ddf7b85baa81600a2af60d015540
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse のチート シート
このページは、データ ウェアハウス ソリューションの主要なユース ケースの設計に役立ちます。 このチート シートを読むと最高水準のデータ ウェアハウスを構築する過程がよくわかりますが、各ステップの詳細についてさらに学習することを強くお勧めします。 最初に、SQL Data Warehouse が**[どのようなものであり、どのようなものではないか]**についての、この優れた記事を読むことをお勧めします。

次に示すのは、SQL Data Warehouse の設計を始めるときに従う必要があるプロセスの概要です。

![概要]

## <a name="queries-and-operations-across-tables"></a>複数のテーブルを対象とするクエリおよび操作

データ ウェアハウスで行われる最も重要な操作とクエリについて事前に知っておくことが非常に重要です。 データ ウェアハウスのアーキテクチャでは、これらの操作を優先する必要があります。 一般的な操作の例は次のとおりです。
* 1 つまたは 2 つのファクト テーブルをディメンション テーブルと結合し、このテーブルにフィルター処理を行って特定の期間を抽出して、結果をデータ マートに追加する
* ファクト セールに対して大きい更新または小さい更新を行う
* テーブルにデータのみを追加する

操作の種類を知ることは、テーブルの設計を最適化するのに役立ちます。

## <a name="data-migration"></a>データ移行

最初に、データを **[ADLS]** または Azure Blob Storage に読み込むことをお勧めします。 次に、Polybase を使って SQL Data Warehouse のステージング テーブルにデータを読み込む必要があります。 次の構成をお勧めします。

| 設計 | 推奨 |
|:--- |:--- |
| ディストリビューション | ラウンド ロビン |
| インデックス作成 | ヒープ |
| パーティション分割 | なし |
| リソース クラス | largerc または xlargerc |

詳しくは、**[データの移行]、[データの読み込み]**、**読み込みについての[詳細なガイダンス]**に関するページをご覧ください。 

## <a name="distributed-or-replicated-tables"></a>分散テーブルまたはレプリケート テーブル

テーブルのプロパティに応じて、次の戦略をお勧めします。

| type | 適しているプロパティ | 条件|
|:--- |:--- |:--- |
| レプリケート | • 圧縮 (最大 5 倍の圧縮) 後のストレージが 2 GB 未満である、スター スキーマの小さいディメンション テーブル |• テーブルに対する多くの書き込みトランザクション (挿入、アップサート、削除、更新など)<br></br>• Data Warehouse ユニット (DWU) のプロビジョニングを頻繁に変更する<br></br>• テーブルには多くの列があるが、使うのは 2 - 3 列だけである<br></br>• レプリケート テーブルにインデックスを作成する |
| ラウンド ロビン (既定) | • 一時/ステージング テーブル<br></br> • 明白な結合キーまたは適切な候補列がない |• データ移動のためにパフォーマンスが低い |
| Hash | • ファクト テーブル<br></br>• 大きいディメンション テーブル |• ディストリビューション キーを更新できない |

**ヒント:**
* 最初はラウンド ロビンを使いますが、大規模な並列アーキテクチャを活用するにはハッシュ ディストリビューション戦略を目指します
* 共通ハッシュ キーが同じデータ形式であることを確認します
* 分散には varchar 形式を使わないようにします
* 頻繁に結合操作が行われるファクト テーブルに対する共通ハッシュ キーを持つディメンション テーブルは、ハッシュが分散される可能性があります
* データの偏りを分析するには、*[sys.dm_pdw_nodes_db_partition_stats]* を使います
* クエリの背後で行われているデータ移動を分析し、ブロードキャストおよびシャッフル操作にかかる時間を監視するには、*[sys.dm_pdw_request_steps]* を使います。 ディストリビューション戦略の検討に役立ちます。

詳しくは、**[レプリケート テーブル]および[分散テーブル]**に関するページをご覧ください。

## <a name="indexing-your-table"></a>テーブルのインデックスを作成する

インデックスを作成すると、テーブルを迅速に読み取るのに**効果的**です。 ニーズに応じて、異なるテクノロジのセットを使うことができます。

| type | 適しているプロパティ | 条件|
|:--- |:--- |:--- |
| ヒープ | • ステージング/一時テーブル<br></br>• 小さいテーブルと小さい参照 |• すべての参照がテーブル全体をスキャンします |
| クラスター化インデックス | • 最大 1 億行のテーブル<br></br>• 1 - 2 列のみが頻繁に使われる大規模なテーブル (1 億行以上) |• レプリケート テーブルで使われます<br></br>• 複数の Join、Group By 操作を含む複雑なクエリ<br></br>• インデックス付き列で更新を行い、メモリを消費します |
| クラスター化列ストア インデックス (CCI) (既定) | • 大規模なテーブル (1 億行以上) | • レプリケート テーブルで使われます<br></br>• 大量のテーブル更新操作を行います<br></br>• テーブルの過剰なパーティション分割: 行グループは異なるディストリビューション ノードおよびパーティションにはまたがりません |

**ヒント:**
* クラスター化インデックスに加えて、フィルターで使用頻度の高い列に非クラスター化インデックスを追加することが必要な場合があります。 
* CCI を含むテーブルでのメモリの管理方法に注意する必要があります。 データを読み込むときに、大きいリソース クラスによってユーザー (またはクエリ) にメリットがあるようにします。 トリミングによって多くの小さい圧縮された行グループが作成されないようにします
* CCI を使用するコンピューティング層ロックに対して最適化されています
* CCI では、行グループの圧縮が不十分であるためにパフォーマンスが低下することがあり、CCI の再構築または再編成が必要になる場合はあります。 圧縮された行グループあたり 10 万行以上にします。 理想は行グループあたり 100 万行です。
* 増分読み込みの頻度とサイズに基づいて、インデックスを再編成または再構築するタイミングを自動化します。 大掃除は常に役に立ちます。
* 行グループをトリミングするときは戦略的に行います。開いている行グループの大きさはどれくらいですか。 今後、どれくらいのデータが読み込まれると予想されますか。

詳しくは、**[インデックス]**に関するページをご覧ください。

## <a name="partitioning"></a>パーティション分割
大きなファクト テーブル (10 億行以上) がある場合、テーブルをパーティション分割することがあります。 ほとんどの場合、パーティション キーは日付に基づく必要があります。 パーティション分割しすぎないように注意してください (特に、クラスター化列ストア インデックスの場合)。
ETL を必要とするステージング テーブルでは、パーティション分割によるメリットがあります。 データのライフサイクル管理が容易になります。
データをパーティション分割しすぎないように注意してください (特に、クラスター化列ストア インデックスの場合)。

詳しくは、**[パーティション]**に関するページをご覧ください。

## <a name="incremental-load"></a>段階的に読み込む

最初に、データを読み込むために大きなリソース クラスを割り当てる必要があります。 SQL DW に ETL パイプラインを自動化するための Polybase および ADF V2 を使うことをお勧めします。

履歴データの更新の大きなバッチの場合は、関係のあるデータを最初に削除することをお勧めします。 その後、新しいデータを一括挿入できます。 この 2 段階のアプローチがより効率的です。

## <a name="maintain-statistics"></a>統計を管理する
自動統計は、間もなく一般に利用できるようになります。 それまでは、SQL Data Warehouse には統計情報の手動メンテナンスが必要です。 データに**大幅な**変更が発生したときに統計を更新することが重要です。 これにより、クエリ プランを最適化できます。 すべての統計情報の管理に時間がかかりすぎる場合は、統計を作成する列を限定してもかまいません。 更新の頻度を定義することもできます。 たとえば、新しい値が毎日追加される日付列を更新します。 結合に含まれる列、WHERE 句で使われている列、および GROUP BY に含まれている列に関する統計を作成すると、最も大きなメリットが得られます。

詳しくは、**[統計情報]**に関するページをご覧ください。

## <a name="resource-class"></a>リソース クラス
SQL Data Warehouse では、クエリにメモリを割り当てる方法としてリソース グループを使用します。 クエリまたは読み込みの速度を向上させるために、より多くのメモリが必要な場合は、さらに高いリソース クラスを割り当てる必要があります。 その一方で、使うリソース クラスを大きくすると同時実行に影響があります。 すべてのユーザーを大きいリソース クラスに移行する前に、そのことを考慮する必要があります。

クエリに時間がかかりすぎる場合は、ユーザーが大きいリソース クラスで実行していないことを確認します。 大きいリソース クラスは、多くの同時実行スロットを消費します。 それにより、他のクエリが待機する可能性があります。

最後に、コンピューティング最適化層を使うと、各リソース クラスが取得するメモリの量が弾力性最適化層の 2.5 倍になります。

詳しくは、**[リソース クラスと同時実行]**に関するページをご覧ください。

## <a name="lower-your-cost"></a>コストの削減
SQL Data Warehouse の重要な機能として、使用していないときは一時停止できます。そうすると、コンピューティング リソースの課金が停止されます。 もう 1 つの重要な機能として、リソースをスケールできます。 一時停止とスケールは、Azure Portal または PowerShell コマンドを使用して行うことができます。

Azure Functions では自動スケールを利用できます。

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-you-architecture-for-performance"></a>パフォーマンスのためのアーキテクチャの最適化

SQL データベースと Azure Analysis Services はハブとスポークのアーキテクチャにすることを検討するようお勧めします。 このソリューションは、異なるユーザー グループ間のワークロードを分離しながら、SLQ DB と Azure Analysis Services の高度なセキュリティ機能を利用できます。 また、無制限の同時実行をユーザーに提供することもできます。

詳しくは、**[SQL DW を利用する一般的なアーキテクチャ]**に関するページをご覧ください。

クリックして、SQL DW からの SQL DB データベースにスポークを展開してください。

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[概要]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[データの読み込み]:./design-elt-data-loading.md
[詳細なガイダンス]: ./guidance-for-loading-data.md
[インデックス]:./sql-data-warehouse-tables-index.md
[パーティション]:./sql-data-warehouse-tables-partition.md
[統計情報]:./sql-data-warehouse-tables-statistics.md
[リソース クラスと同時実行]:./sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[SQL DW を利用する一般的なアーキテクチャ]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[どのようなものであり、どのようなものではないか]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[データの移行]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
[レプリケート テーブル]:https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables
[分散テーブル]:https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute
[ADLS]: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store
[sys.dm_pdw_nodes_db_partition_stats]: https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
