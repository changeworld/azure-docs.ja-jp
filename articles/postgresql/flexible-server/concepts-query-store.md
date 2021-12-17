---
title: クエリ ストア - Azure Database for PostgreSQL - フレキシブル サーバー
description: この記事では、Azure Database for PostgreSQL - フレキシブル サーバーでのクエリ ストア機能について説明します。
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 6ab2ea6f6544bcf561e92f00b5afee693393c157
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558662"
---
# <a name="monitor-performance-with-query-store"></a>クエリ ストアによるパフォーマンスの監視
**適用対象:** Azure Database for PostgreSQL - フレキシブル サーバー バージョン 11 以降

Azure Database for PostgreSQL のクエリ ストア機能では、一定期間にわたってクエリ パフォーマンスを追跡する手段が提供されます。 クエリ ストアを使用すると、実行時間が最長のクエリおよびリソースを最も消費しているクエリを迅速に特定できるため、パフォーマンスのトラブルシューティングが簡単になります。 クエリ ストアでは、クエリおよびランタイム統計の履歴が自動的にキャプチャされて保持されるので、それらを確認できます。 時間別にデータがスライスされるため、一時的な使用パターンを確認できます。 すべてのユーザー、データベース、クエリに関するデータが Azure Database for PostgreSQL インスタンス内の **azure_sys** という名前のデータベースに格納されます。

> [!IMPORTANT]
> **azure_sys** データベースまたはそのスキーマを変更しないでください。 そうすると、クエリ ストアおよび関連するパフォーマンス機能が正しく機能しません。
## <a name="enabling-query-store"></a>クエリ ストアの有効化
クエリ ストアはオプトイン機能であるため、サーバーでは既定では有効になっていません。 クエリ ストアは、特定のサーバー上のすべてのデータベースに対してグローバルに有効または無効になり、データベースごとにオンまたはオフにすることはできません。
### <a name="enable-query-store-using-the-azure-portal"></a>Azure portal を使用してクエリ ストアを有効にする
1. Azure portal にサインインし、ご利用の Azure Database for PostgreSQL サーバーを選択します。
2. メニューの **[設定]** セクションで、 **[サーバー パラメーター]** を選択します。
3. `pg_qs.query_capture_mode` パラメーターを検索します。
4. 値を `TOP` または `ALL` に設定し、**保存** します。
azure_sys データベースに保持するデータの最初のバッチには、最大で 20 分ほどかかります。
## <a name="information-in-query-store"></a>クエリ ストア内の情報
クエリ ストアには 1 つのストアがあります。
- クエリ実行の統計情報を保持するためのランタイム統計ストア。

クエリ ストアを使用するための一般的なシナリオは次のとおりです。
- 指定された時間枠内にクエリが実行された回数を確認する
- 大きなデルタを確認するために時間枠間でクエリの平均実行回数を比較する
- 過去数時間内で実行時間が最長のクエリを特定する。領域の使用量を最小限に抑えるために、ランタイム統計ストア内のランタイム実行統計が固定の構成可能な時間枠で集計されます。 これらのストア内の情報は、ビューを使用して参照できます。
## <a name="access-query-store-information"></a>クエリ ストア情報へのアクセス
クエリ ストア データは、Postgres サーバー上の azure_sys データベースに格納されます。 次のクエリでは、クエリ ストア内のクエリに関する情報が返されます。
```sql
SELECT * FROM query_store.qs_view; 
``` 

## <a name="configuration-options"></a>構成オプション
クエリ ストアが有効になっている場合、データは 15 分間の集計ウィンドウで保存され、ウィンドウあたり最大 500 件の個別のクエリが保存されます。 次のオプションは、クエリ ストア パラメーターを構成するために使用できます。

| **パラメーター** | **説明** | **[Default]** | **Range**|
|---|---|---|---|
| pg_qs.query_capture_mode | 追跡対象のステートメントを設定します。 | なし | none、top、all |
| pg_qs.max_query_text_length | 保存できるクエリの最大長を設定します。 これより長いクエリは切り詰められます。 | 6000 | 100 - 10K |
| pg_qs.retention_period_in_days | 保有期間を設定します。 | 7 | 1 - 30 |
| pg_qs.track_utility | ユーティリティ コマンドを追跡するかどうかを設定します | on | on、off |

パラメーターの別の値を取得または設定するには、[Azure portal](howto-configure-server-parameters-using-portal.md) を使用します。

## <a name="views-and-functions"></a>ビューと関数
次のビューと関数を使用してクエリ ストアを表示および管理します。 PostgreSQL の public ロールに属するユーザーは、これらのビューを使用してクエリ ストア内のデータを表示できます。 これらのビューは、**azure_sys** データベース内でのみ使用できます。
クエリは、リテラルと定数を削除した後、その構造を調べることで正規化されます。 2 つのクエリがリテラル値を除いて同一の場合、それらは同じ queryId を持ちます。
### <a name="query_storeqs_view"></a>query_store.qs_view
このビューでは、クエリ ストア内のすべてのデータが返されます。 個別のデータベース ID、ユーザー ID、クエリ ID ごとに 1 つの行があります。 

|**名前**   |**Type** | **参照**  | **説明**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | runtime_stats_entries テーブルからの ID|
|user_id    |oid    |pg_authid.oid  |ステートメントを実行したユーザーの OID|
|db_id  |oid    |pg_database.oid    |ステートメントが実行されたデータベースの OID|
|query_id   |bigint  || ステートメントの解析ツリーから計算される内部ハッシュ コード|
|query_sql_text |Varchar(10000)  || 代表的なステートメントのテキスト。 同じ構造を持つ複数の異なるクエリがまとめてクラスター化されます。このテキストは、クラスター内の最初のクエリのテキストです。|
|plan_id    |bigint |   |まだ使用できない、このクエリに対応するプランの ID|
|start_time |timestamp  ||  クエリは、タイム バケットによって集計されます｡バケットの期間は既定で 15 分です。 これは、このエントリのタイム バケットに対応する開始時刻です。|
|end_time   |timestamp  ||  このエントリのタイム バケットに対応する終了時刻。|
|calls  |bigint  || クエリの実行回数|
|total_time |double precision   ||  クエリの合計実行時間 (ミリ秒)|
|min_time   |double precision   ||  クエリの最小実行時間 (ミリ秒)|
|max_time   |double precision   ||  クエリの最大実行時間 (ミリ秒)|
|mean_time  |double precision   ||  クエリの平均実行時間 (ミリ秒)|
|stddev_time|   double precision    ||  クエリ実行時間の標準偏差 (ミリ秒) |
|rows   |bigint ||  ステートメントによって取得または影響された行の合計数|
|shared_blks_hit|   bigint  ||  ステートメントによる共有ブロック キャッシュ ヒットの合計数|
|shared_blks_read|  bigint  ||  ステートメントによって読み取られた共有ブロックの合計数|
|shared_blks_dirtied|   bigint   || ステートメントによって使用された共有ブロックの合計数 |
|shared_blks_written|   bigint  ||  ステートメントによって書き込まれた共有ブロックの合計数|
|local_blks_hit|    bigint ||   ステートメントによるローカル ブロック キャッシュ ヒットの合計数|
|local_blks_read|   bigint   || ステートメントによって読み取られたローカル ブロックの合計数|
|local_blks_dirtied|    bigint  ||  ステートメンによって使用されたローカル ブロックの合計数|
|local_blks_written|    bigint  ||  ステートメントによって書き込まれたローカル ブロックの合計数|
|temp_blks_read |bigint  || ステートメントによって読み取られた一時ブロックの合計数|
|temp_blks_written| bigint   || ステートメントによって書き込まれた一時ブロックの合計数|
|blk_read_time  |double precision    || ステートメントによってブロックの読み取りに費やされた時間の合計 (ミリ秒単位) (track_io_timing が有効になっている場合。それ以外の場合は 0)|
|blk_write_time |double precision    || ステートメントによってブロックの書き込みに費やされた時間の合計 (ミリ秒単位) (track_io_timing が有効になっている場合。それ以外の場合は 0)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
このビューでは、クエリ ストア内のクエリ テキスト データが返されます。 個別の query_text ごとに 1 つの行があります。

| **名前** | **Type** | **説明** |
|--|--|--|
| query_text_id | bigint | query_texts テーブルの ID |
| query_sql_text | Varchar(10000) | 代表的なステートメントのテキスト。 同じ構造を持つ複数の異なるクエリがまとめてクラスター化されます。このテキストは、クラスター内の最初のクエリのテキストです。 |

### <a name="functions"></a>関数
`qs_reset` では、クエリ ストアによってこれまでに収集されたすべての統計が破棄されます。 この関数は、サーバー管理者ロールによってのみ実行できます。

`staging_data_reset` では、クエリ ストアによってメモリ内で収集されたすべての統計 (つまり、データベースにまだフラッシュされていないメモリ内のデータ) が破棄されます。 この関数は、サーバー管理者ロールによってのみ実行できます。

## <a name="limitations-and-known-issues"></a>制限事項と既知の問題
- PostgreSQL サーバーでパラメーター default_transaction_read_only がオンの場合、クエリ ストアはデータをキャプチャしません。
## <a name="next-steps"></a>次のステップ
- 詳細については、[クエリ ストアが特に役に立つシナリオ](concepts-query-store-scenarios.md)に関するページをご覧ください。
- 詳細については、[クエリ ストアの使用のベスト プラクティス](concepts-query-store-best-practices.md)に関するページをご覧ください。
