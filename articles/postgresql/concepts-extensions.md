---
title: "Azure Database for PostgreSQL での PostgreSQL 拡張機能の使用"
description: "Azure Database for PostgreSQL で拡張機能を使用して、データベースの機能を拡張する方法について説明します。"
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0b4150fcd7d32c823173c3e2676e226634346a2b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL の PostgreSQL 拡張機能
PostgreSQL では拡張機能を使用してデータベースの機能を拡張することができます。 拡張機能により、関連する複数の SQL オブジェクトを 1 つのパッケージにまとめて、1 つのコマンドでデータベースに読み込んだり、データベースから削除したりできます。 データベースに読み込まれた拡張機能は、組み込み機能と同じように動作します。 PostgreSQL 拡張機能の詳細については、「[Packaging Related Objects into an Extension (拡張機能への関連オブジェクトのパッケージ化)](https://www.postgresql.org/docs/9.6/static/extend-extensions.html)」を参照してください。

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL 拡張機能の使用方法
PostgreSQL 拡張機能を使用するには、その拡張機能がデータベースにインストールされている必要があります。 特定の拡張機能をインストールするには、psql ツールから [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) コマンドを実行して、パッケージ化されたオブジェクトをデータベースに読み込みます。

Azure Database for PostgreSQL で現在サポートされている主要な拡張機能のサブセットを次に示します。 ここにない拡張機能はサポートされていません。Azure Database for PostgreSQL サービスでは、独自の拡張機能を作成することはできません。

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Azure Database for PostgreSQL でサポートされる拡張機能
Azure Database for PostgreSQL で現在サポートされている標準的な PostgreSQL 拡張機能を次の表に示します。 この情報は、`pg\_available\_extensions` にクエリを実行して確認することもできます。

### <a name="data-types-extensions"></a>データ型の拡張機能

> [!div class="mx-tableFixed"]
| **拡張機能** | **説明** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | 自動暗号化パスワードのデータ型を提供します。 |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | 大文字と小文字が区別されない文字列型を提供します。 |
| [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | 多次元キューブのデータ型を提供します。 |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | キー/値のペアのセットを格納するデータ型を提供します。 |
| [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | 国際対応の製品番号規格のデータ型を提供します。 |
| [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | 階層ツリー状の構造体のデータ型を提供します。 |

### <a name="functions-extensions"></a>関数の拡張機能

> [!div class="mx-tableFixed"]
| **拡張機能** | **説明** |
|---|---|
| [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | 地球の表面にある大圏距離を計算するための手段を提供します。 |
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | 文字列間の類似点と相違点を特定する関数を提供します。 |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | 整数の null を含まない配列を操作する関数と演算子を提供します。 |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | 暗号化関数を提供します。 |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | 時刻または ID によってパーティション テーブルを管理します。 |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | trigram 一致に基づいて英数字テキストの類似性を特定する関数と演算子を提供します。 |
| [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | クロス集計を含む、テーブル全体を操作する関数を提供します。 |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | 汎用一意識別子 (UUID) を生成します。 |

### <a name="full-text-search-extensions"></a>フルテキスト検索の拡張機能

> [!div class="mx-tableFixed"]
| **拡張機能** | **説明** |
|---|---|
| [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | 整数に対するテキスト検索ディクショナリのテンプレートを提供します。 |
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | 語彙からアクセント記号 (分音記号) を削除するテキスト検索辞書。 |

### <a name="index-types-extensions"></a>インデックス型の拡張機能

> [!div class="mx-tableFixed"]
| **拡張機能** | **説明** |
|---|---|
| [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | 特定のデータ型に対して B ツリーのような動作を実装するサンプル GIN 演算子クラスを提供します。 |
| [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | B ツリーを実装する GiST インデックス演算子クラスを提供します。 |

### <a name="language-extensions"></a>言語の拡張機能

> [!div class="mx-tableFixed"]
| **拡張機能** | **説明** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL 読み込み可能な手続き型言語。 |

### <a name="miscellaneous-extensions"></a>その他の拡張機能

> [!div class="mx-tableFixed"]
| **拡張機能** | **説明** |
|---|---|
| [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | リアルタイムで共有バッファー キャッシュの動作を確認する手段を提供します。 |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | 関係データをバッファー キャッシュに読み込む方法を提供します。 |
| [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | サーバーで実行されるすべての SQL ステートメントの実行統計を追跡する手段を提供します。 |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | 行レベルのロックに関する情報を表示するための手段を提供します。 |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | タプル レベルの統計を表示するための手段を提供します。 |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | 外部 PostgreSQL サーバーに格納されているデータへのアクセスに使用される外部データ ラッパーです。 |

### <a name="postgis-extensions"></a>PostGIS 拡張機能

> [!div class="mx-tableFixed"]
| **拡張機能** | **説明** |
|---|---|
| [PostGIS](http://www.postgis.net/)、postgis\_topology、postgis\_tiger\_geocoder、postgis\_sfcgal | PostgreSQL の空間および地理なオブジェクト。 |
| address\_standardizer、address\_standardizer\_data\_us | 構成要素へのアドレスの解析に使用されます。 ジオコーディング アドレス正規化の手順をサポートするために使用されます。 |
| [pgrouting](http://pgrouting.org/) | PostGIS/PostgreSQL 地理空間データベースを拡張して、地理空間ルーティング機能を提供します。 |

## <a name="next-steps"></a>次の手順
使用する拡張機能が見つからない場合は、お知らせください。 [顧客フィードバック フォーラム](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)では既存のリクエストに投票できます。また、新しいフィードバックやリクエストを作成することも可能です。
