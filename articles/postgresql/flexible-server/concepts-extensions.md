---
title: 拡張機能 - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure Database for PostgreSQL - フレキシブル サーバーでの PostgreSQL 拡張機能の使用の可用性について調べる
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 1126c218f8e80b7d89183746890a3fae1357d29d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90931085"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーの PostgreSQL 拡張機能

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

PostgreSQL では拡張機能を使用してデータベースの機能を拡張することができます。 拡張機能により、関連する複数の SQL オブジェクトを単一のパッケージにまとめて、単一のコマンドでデータベースに対する読み込みや削除を行うことができます。 データベースに読み込まれた後、拡張機能は組み込み機能と同じように機能します。

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL 拡張機能の使用方法
PostgreSQL 拡張機能を使用するには、その拡張機能がデータベースにインストールされている必要があります。 特定の拡張機能をインストールするには、 [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) コマンドを実行します。 このコマンドで、パッケージ化されたオブジェクトがデータベースに読み込まれます。

Azure Database for PostgreSQL でサポートされる主要な拡張機能のサブセットを次に示します。 この情報は、`SHOW azure.extensions;`を実行して確認することもできます。 このドキュメントに記載されていない拡張機能は、Azure Database for PostgreSQL - フレキシブル サーバーではサポートされていません。 Azure Database for PostgreSQL には、独自の拡張機能を作成することも読み込むこともできません。


## <a name="postgres-12-extensions"></a>Postgres 12 の拡張機能

Postgres バージョン 12 を搭載した Azure Database for PostgreSQL - フレキシブル サーバーでは、次の拡張機能を使用できます。 

> [!div class="mx-tableFixed"]
> | **拡張子**| **拡張機能のバージョン** | **説明** |
> |---|---|---|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1.2             | 関係の整合性を検証するための関数|
> |[bloom](https://www.postgresql.org/docs/12/bloom.html)                    | 1.0             | bloom アクセス メソッド - シグネチャ ファイルに基づくインデックス|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | GIN で一般的なデータ型のインデックスを作成するためのサポート|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | GiST で一般的なデータ型のインデックスを作成するためのサポート|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | 大文字と小文字を区別しない文字列のデータ型|
> |[cube](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | 多次元キューブのデータ型|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1.2             | データベース内から他の PostgreSQL データベースに接続する|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1.0             | 整数のテキスト検索辞書テンプレート|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1.0             | 拡張されたシノニム処理のためのテキスト検索ディクショナリのテンプレート|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1.1             | 地表面上の大圏距離を計算する|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1.1             | 文字列間の類似点と相違点を特定する|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | (キー、値) ペアのセットを格納するためのデータ型|
> |[intagg](https://www.postgresql.org/docs/12/intagg.html)                     | 1.1             | 整数のアグリゲーターと列挙子 (廃止)|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1.2             | 整数の 1 次元配列に対する関数、演算子、およびインデックスのサポート|
> |[isn](https://www.postgresql.org/docs/12/isn.html)                          | 1.2             | 国際対応の製品番号規格のデータ型|
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1.1             | 階層ツリー状の構造体のデータ型|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1.7             | 低レベルでデータベース ページの内容を検査する|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | 共有バッファー キャッシュを確認する|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | 空き領域マップ (FSM) を確認する|
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | 関係データをプレウォームする|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1.7             | 実行されたすべての SQL ステートメントの実行統計情報を追跡する|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | trigram に基づくテキストの類似性の測定とインデックス検索|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | 可視性マップ (VM) とページ レベルの可視性情報を調べる|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | 監査機能を提供する|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | 暗号化関数|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | 行レベルのロック情報を表示する|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | タプルレベルの統計情報を表示する|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | PL/pgSQL 手続き型言語|
> |[postgis](https://www.postgis.net/)                      | 3.0.0           | PostGIS ジオメトリ、地理、およびラスターの空間型と関数|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1.0             | リモート PostgreSQL サーバー用の外部データ ラッパー|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1.2             | SSL 証明書に関する情報|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1.0             |  行数を制限として受け取る TABLESAMPLE メソッド|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1.0             |  ミリ秒単位の時間を制限として受け取る TABLESAMPLE メソッド|
> |[unaccent](https://www.postgresql.org/docs/12/unaccent.html)                     | 1.1             | アクセントを削除するテキスト検索辞書|
> |[uuid-ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1.1             | 汎用一意識別子 (UUID) を生成する|

## <a name="postgres-11-extensions"></a>Postgres 11 の拡張機能

Postgres バージョン 11 を搭載した Azure Database for PostgreSQL - フレキシブル サーバーでは、次の拡張機能を使用できます。 

> [!div class="mx-tableFixed"]
> | **拡張子**| **拡張機能のバージョン** | **説明** |
> |---|---|---|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1.1             | 関係の整合性を検証するための関数|
> |[bloom](https://www.postgresql.org/docs/11/bloom.html)                    | 1.0             | bloom アクセス メソッド - シグネチャ ファイルに基づくインデックス|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | GIN で一般的なデータ型のインデックスを作成するためのサポート|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | GiST で一般的なデータ型のインデックスを作成するためのサポート|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | 大文字と小文字を区別しない文字列のデータ型|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | 多次元キューブのデータ型|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | データベース内から他の PostgreSQL データベースに接続する|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | 整数のテキスト検索辞書テンプレート|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1.0             | 拡張されたシノニム処理のためのテキスト検索ディクショナリのテンプレート|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | 地表面上の大圏距離を計算する|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | 文字列間の類似点と相違点を特定する|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | (キー、値) ペアのセットを格納するためのデータ型|
> |[intagg](https://www.postgresql.org/docs/11/intagg.html)                     | 1.1             | 整数のアグリゲーターと列挙子 (廃止)|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | 整数の 1 次元配列に対する関数、演算子、およびインデックスのサポート|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | 国際対応の製品番号規格のデータ型|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | 階層ツリー状の構造体のデータ型|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1.7             | 低レベルでデータベース ページの内容を検査する|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | 共有バッファー キャッシュを確認する|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | 空き領域マップ (FSM) を確認する|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | 関係データをプレウォームする|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | 実行されたすべての SQL ステートメントの実行統計情報を追跡する|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | trigram に基づくテキストの類似性の測定とインデックス検索|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | 可視性マップ (VM) とページ レベルの可視性情報を調べる|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | 監査機能を提供する|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | 暗号化関数|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | 行レベルのロック情報を表示する|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | タプルレベルの統計情報を表示する|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL 手続き型言語|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS ジオメトリ、地理、およびラスターの空間型と関数|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | リモート PostgreSQL サーバー用の外部データ ラッパー|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | SSL 証明書に関する情報|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | クロス集計を含む、テーブル全体を操作する関数|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  行数を制限として受け取る TABLESAMPLE メソッド|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  ミリ秒単位の時間を制限として受け取る TABLESAMPLE メソッド|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | アクセントを削除するテキスト検索辞書|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | 汎用一意識別子 (UUID) を生成する|


## <a name="pg_prewarm"></a>pg_prewarm

pg_prewarm 拡張機能により、リレーショナル データがキャッシュに読み込まれます。 キャッシュをプレウォームすると、再起動後に最初に実行したときのクエリの応答時間が向上します。 自動 prewarm 機能は、Azure Database for PostgreSQL - フレキシブル サーバーでは現在、使用できません。


## <a name="next-steps"></a>次のステップ

使用する拡張機能が見つからない場合は、お知らせください。 [フィードバック フォーラム](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)で、既存のリクエストに投票することや、新しいフィードバック リクエストを作成することができます。