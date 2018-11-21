---
title: Azure Database for PostgreSQL サーバーでの自動バキュームを最適化する
description: この記事では、Azure Database for PostgreSQL サーバーで自動バキュームを最適化する方法について説明します。
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 3f35779337082b7280398bd13ef870c74f3ec082
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685992"
---
# <a name="optimizing-autovacuum-on-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーでの自動バキュームの最適化 
この記事では、Azure Database for PostgreSQL で自動バキュームを効率的に最適化する方法について説明します。

## <a name="overview-of-autovacuum"></a>自動バキュームの概要
PostgreSQL は、MVCC を使用して、データベースの同時実行の強化を実現しています。 すべての更新は結果として挿入と削除になり、すべての削除は結果として、削除対象のためにソフト マークされている行になります。 ソフト マークは結果として、後で消去される使用不能タプルの識別になります。 PostgreSQL は、これらのすべてを、バキューム ジョブを実行することによって実現します。

バキューム ジョブは、手動または自動でトリガーできます。 データベースで更新や削除の操作が大量に発生している場合や、アイドル状態のときが少ないほど、より多くの使用不能タプルが存在することになります。  バキュームをより頻繁に実行する必要性が高くなるのは、データベースにかかる負荷が高いときで、これは、バキューム ジョブを**手動で**実行するのは不便な状況です。

自動バキュームは、構成することが可能で、チューニングによるメリットがあります。 PostgreSQL 出荷時の既定値は、Raspberry Pi など、どのような種類のデバイスでも製品が確実に機能するようになっていて、理想的な構成値は、以下のようにいくつかの要因に左右されます。
- 使用可能な合計リソース - SKU とストレージ サイズ。
- リソースの使用状況。
- 個々のオブジェクトの特性。

## <a name="autovacuum-benefits"></a>自動バキュームのメリット
バキュームを随時実行しないと、使用不能タプルの蓄積によって、以下のような結果が生じる可能性があります。
- データの肥大化 - データベースやテーブルの拡大。
- 最適とはいえないインデックスの増大。
- I/O の増加。

## <a name="monitoring-bloat-with-autovacuum-queries"></a>自動バキューム クエリによる肥大化の監視
次のサンプル クエリは、"XYZ"という名前のテーブルで、使用不能タプルとライブ タプルの数を識別するために設計されています: 'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>自動バキュームの構成
自動バキュームを制御する構成パラメーターは、2 つの重要な問いに焦点を合わせて検討します。
- いつ開始する必要があるか。
- 開始後にどれだけ消去する必要があるか。

以下に、上の問いに基づいて更新することができる自動バキューム構成パラメーターの一部と共に、いくつかのガイダンスを示します。
パラメーター|説明|既定値
---|---|---
autovacuum_vacuum_threshold|任意の 1 つのテーブルで VACUUM をトリガーするために必要な、更新または削除されたタプルの最小数を指定します。 既定値は 50 タプルです。 このパラメーターは、postgresql.conf ファイルまたはサーバーのコマンド ラインでのみ設定できます。 テーブル ストレージのパラメーターを変更することで、個々のテーブルの設定をオーバーライドできます。|50
autovacuum_vacuum_scale_factor|VACUUM をトリガーするかどうかを決定する際に、autovacuum_vacuum_threshold に追加するテーブル サイズの割合を指定します。 既定値は 0.2 (テーブル サイズの 20%) です。 このパラメーターは、postgresql.conf ファイルまたはサーバーのコマンド ラインでのみ設定できます。 テーブル ストレージのパラメーターを変更することで、個々のテーブルの設定をオーバーライドできます。|5%
autovacuum_vacuum_cost_limit|自動 VACUUM 操作で使用されるコストの上限値を指定します。 -1 (既定値) が指定されている場合は、通常の vacuum_cost_limit 値が使用されます。 この値は、worker が 1 つ以上がある場合、実行中の自動バキューム worker 間で均等に配分されます。 各 worker での上限の合計は、この変数の値を超えません。 このパラメーターは、postgresql.conf ファイルまたはサーバーのコマンド ラインでのみ設定できます。 テーブル ストレージのパラメーターを変更することで、個々のテーブルの設定をオーバーライドできます。|-1
autovacuum_vacuum_cost_delay|自動 VACUUM 操作で使用される遅延のコスト値を指定します。 -1 が指定された場合は、通常の vacuum_cost_delay 値が使用されます。 既定値は 20 ミリ秒です。 このパラメーターは、postgresql.conf ファイルまたはサーバーのコマンド ラインでのみ設定できます。 テーブル ストレージのパラメーターを変更することで、個々のテーブルの設定をオーバーライドできます。|20 ミリ秒
autovacuum_nap_time|特定のデータベースに対する自動バキューム実行の間隔の最小遅延を指定します。 各ラウンドで、デーモンがデータベースを調査し、そのデータベースのテーブルに対して、必要に応じて VACUUM コマンドと ANALYZE コマンドを発行します。 遅延は秒単位で測定され、既定値は 1 分 (1 min) です。 このパラメーターは、postgresql.conf ファイルまたはサーバーのコマンド ラインでのみ設定できます。|15 s
autovacuum_max_workers|任意の時点で実行されている可能性がある (自動バキューム ランチャー以外の) 自動バキューム プロセスの最大数を指定します。 既定値は 3 です。 このパラメーターは、サーバー起動時にのみ設定できます。|3
上記の設定は、テーブル ストレージのパラメーターを変更することで、個々のテーブルについてオーバーライドできます。  

## <a name="autovacuum-cost"></a>自動バキュームのコスト
バキューム操作実行時の "コスト" を次に示します。
- バキューム実行対象のデータ ページにはロックが配置されます。
- バキュームの実行中にはコンピューティングとメモリが使用されます。

これは、バキュームの実行頻度は高すぎても低すぎてもまずく、ワークロードに適応させる必要があることを意味します。 それぞれにトレードオフがあるため、すべての自動バキューム パラメーターの変更をテストすることをお勧めします。

## <a name="autovacuum-start-trigger"></a>自動バキュームの開始トリガー
自動バキュームは、使用不能タプルの数が、autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples を超えるとトリガーされます。この reltuples は定数です。

自動バキュームからのクリーンアップは、データベースの負荷に応じて行われる必要があります。そうでないと、ストレージが不足し、クエリの全般的な速度低下が起きることがあります。 時間の経過と共に調整を進めて、バキュームで使用不能タプルをクリーンアップする速度が、使用不能タプルが作られる速度と等しくなる必要があります。

更新/削除の数が多いデータベースでは、使用不能タプルが多くなり、より多くの領域が必要です。 一般に、更新/削除の数が多いデータベースでは、使用不能タプルが長時間にわたって蓄積しないように、autovacuum_vacuum_scale_factor の値と、autovacuum_vacuum_threshold の値を小さくする価値があります。 小さなデータベースでは、バキュームが必要な緊急度が低いため、どちらのパラメーターにもより大きな値を使用できます。 頻繁なバキュームは、コンピューティングとメモリのコストによって実現されることを思い出してください。

既定の割合である 20% は、使用不能タプルの割合が高いテーブルではなく、使用不能タプルの割合が低いテーブルで適切に機能します。 たとえば、20 GB のテーブルでは、これは 4 GB の使用不能タプルになり、1 TB のテーブルでは 200 GB の使用不能タプルになります。

PostgreSQL では、テーブル レベルまたはインスタンス レベルでこれらのパラメーターを設定できます。 Azure Database for PostgreSQL では、現在、これらのパラメーターはテーブル レベルでのみ設定できます。

## <a name="estimating-the-cost-of-autovacuum"></a>自動バキュームのコストの見積もり
自動バキュームの実行は "コスト高" で、バキューム操作の実行時間を制御するためのパラメーターがあります。 以下のパラメーターが、バキューム実行コストの見積りの際に役立ちます。
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

バキューム プロセスは、物理ページを読み取り、使用不能タプルがないか調べます。 shared_buffers 内のページはそれぞれ、コスト 1 (vacuum_cost_page_hit) であると見なされます。その他のページはすべて、使用不能タプルが存在する場合はコスト 20 (vacuum_cost_page_dirty)、使用不能タプルが存在しない場合はコスト 10 (vacuum_cost_page_miss) と見なされます。 プロセスが autovacuum_vacuum_cost_limit を超過するとバキューム操作は停止されます。  

上限に達すると、プロセスは autovacuum_vacuum_cost_delay パラメーターで指定された期間の間、スリープ状態になり、その後再度開始されます。 上限に達しない場合、自動バキュームは、autovacuum_nap_time パラメーターで指定された値の後に開始されます。

要約すると、autovacuum_vacuum_cost_delay パラメーターと autovacuum_vacuum_cost_limit パラメーターによって、単位時間あたりにクリーンアップが許可されるデータ量が制御されます。 既定値は、ほとんどの価格レベルに対して小さすぎることに注意してください。 これらのパラメーターの最適値は、価格レベルに左右されるので、それに応じて構成する必要があります。

autovacuum_max_workers パラメーターは、同時に実行できる自動バキューム プロセスの最大数を決定します。

PostgreSQL では、テーブル レベルまたはインスタンス レベルでこれらのパラメーターを設定できます。 Azure Database for PostgreSQL では、現在、これらのパラメーターはテーブル レベルでのみ設定できます。

## <a name="optimizing-autovacuum-per-table"></a>テーブルごとの自動バキュームの最適化
上記の構成パラメーターはすべて、テーブルごとに構成することができます。以下に例を示します。
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

自動バキュームは、テーブルごとの同期プロセスです。 テーブルにある使用不能タプルの割合が増えるにつれて、自動バキュームを行う "コスト" が高くなります。  更新/削除のペースが速いテーブルを複数のテーブルに分割すれば、自動バキュームを並列化し、1 つのテーブルで自動バキュームを完了するための "コスト" を削減する助けになります。 並列の自動バキューム worker の数を増やし、worker が豊富にスケジュールされるようにすることもできます。

## <a name="next-steps"></a>次の手順
自動バキュームの使用とチューニングの詳細については、PostgreSQL の以下のドキュメントを参照してください。
 - PostgreSQL のドキュメント – [Chapter 18. Server Configuration (第 18 章、サーバーの構成)](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - PostgreSQL のドキュメント – [Chapter 24, Routine Database Maintenance Tasks (第 24 章、日常のデータベース メンテナンス タスク)](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
