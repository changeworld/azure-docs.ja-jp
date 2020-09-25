---
title: PostgreSQL 拡張機能を使用する
description: PostgreSQL 拡張機能を使用する
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 19b2ec283619df0cc8d3c880cb2df6f53f6fb332
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90931291"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループで PostgreSQL 拡張機能を使用する

PostgreSQL は、拡張機能で使用する場合に最適です。 実際、独自の Hyperscale 機能の主要素は、Microsoft 提供の `citus` 拡張機能です。これは既定でインストールされ、Postgres で、複数ノード間のデータの透過的なシャード化を可能にします。


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="list-of-extensions"></a>拡張機能の一覧表示
[`contrib`](https://www.postgresql.org/docs/12/contrib.html) の拡張機能に加えて、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのコンテナーに存在する拡張機能の一覧は次のとおりです。
- `citus`、v:9.4
- `pg_cron`、v:1.2
- `plpgsql`、v:1.0
- `postgis`、v:3.0.2

この一覧は時間とともに更新され、更新プログラムがこのドキュメントに掲載されていきます。 今はまだ、上記以外の拡張機能を追加することはできません。

このガイドでは、次の 2 つの拡張機能を使用するシナリオについて説明します。
- [PostGIS](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)


## <a name="manage-extensions"></a>拡張機能の管理

### <a name="enable-extensions"></a>拡張機能を有効にする
この手順は、`contrib` に含まれる拡張機能には必要ありません。
拡張機能を有効にするコマンドの一般的な形式は、次のとおりです。

#### <a name="enable-an-extension-at-the-creation-time-of-a-server-group"></a>サーバー グループの作成時に拡張機能を有効にする:
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
#### <a name="enable-an-extension-on-an-instance-that-already-exists"></a>既に存在するインスタンスで拡張機能を有効にする:
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```

#### <a name="get-the-list-of-extensions-enabled"></a>有効になっている拡張機能の一覧を取得する:
次のコマンドのいずれかを実行します。

##### <a name="with-azdata"></a>azdata を使用する
```console
azdata arc postgres server show -n <server group name>
```
出力をスクロールし、サーバー グループの仕様の engine\extensions セクションを確認します。 次に例を示します。
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
##### <a name="with-kubectl"></a>kubectl を使用する
```console
kubectl describe postgresql-12s/postgres02
```
出力をスクロールし、サーバー グループの仕様の engine\extensions セクションを確認します。 次に例を示します。
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


### <a name="create-extensions"></a>拡張機能を作成する:
任意のクライアント ツールを使用してサーバー グループに接続し、標準の PostgreSQL クエリを実行する:
```console
CREATE EXTENSION <extension name>;
```

### <a name="get-the-list-of-extension-created-in-your-server-group"></a>サーバー グループに作成された拡張機能の一覧を取得する:
任意のクライアント ツールを使用してサーバー グループに接続し、標準の PostgreSQL クエリを実行する:
```console
select * from pg_extension;
```

### <a name="drop-an-extension-from-your-server-group"></a>サーバー グループから拡張機能を削除する:
任意のクライアント ツールを使用してサーバー グループに接続し、標準の PostgreSQL クエリを実行する:
```console
drop extension <extension name>;
```

## <a name="use-the-postgis-and-the-pg_cron-extensions"></a>PostGIS と pg_cron の拡張機能を使用する

### <a name="the-postgis-extension"></a>PostGIS 拡張機能

既存のサーバー グループで PostGIS 拡張機能を有効にするか、新規作成で既に有効になっている拡張機能を使用できます。

**サーバー グループの作成時に拡張機能を有効にする:**
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server create -n pg2 -w 2 --extensions postgis
```

**既に存在するインスタンスで拡張機能を有効にする:**
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server edit --extensions postgis -n pg2
```

インストールされている拡張機能を確認するには、Azure Data Studio など、お気に入りの PostgreSQL クライアント ツールを使用してインスタンスに接続した後、以下の標準 PostgreSQL コマンドを使用します。
```console
select * from pg_extension;
```

PostGIS の例については、最初に、MIT の Department of Urban Studies & Planning から提供されている[サンプル データ](http://duspviz.mit.edu/tutorials/intro-postgis/)を取得します。 テストに VM を使用する場合、`apt-get install unzip` を実行して解凍をインストールすることが必要になることがあります。

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

データベースに接続し、PostGIS 拡張機能を作成しましょう。

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> `postgis` パッケージ (`postgis_raster`、`postgis_topology`、`postgis_sfcgal`、`fuzzystrmatch` など) の拡張機能のいずれかを使用する場合は、PostGIS 拡張機能を最初に作成してから、その他の拡張機能を作成する必要があります。 例: `CREATE EXTENSION postgis`、`CREATE EXTENSION postgis_raster`。

スキーマを作成します。

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

ここでは、coffee_shops テーブルを分散させることで、PostGIS をスケールアウト機能と組み合わせることができます。

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

データを読み込みましょう。

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

`geom` フィールドに、PostGIS `geometry` データ型の適切にエンコードされた緯度と経度を入力します。

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

これで、MIT に最も近いコーヒー ショップ (77 Massachusetts Ave at 42.359055, -71.093500) を一覧表示できるようになりました。

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


### <a name="the-pg_cron-extension"></a>pg_cron 拡張機能

PostGIS に加えて、`pg_cron` を PostgreSQL サーバー グループで有効にしましょう。

```console
azdata postgres server update -n pg2 -ns arc --extensions postgis,pg_cron
```

ノードが再起動され、追加の拡張機能がインストールされます。これには、2、3 分かかる場合があります。

これでもう一度接続し、`pg_cron` 拡張機能を作成することができます。

```sql
CREATE EXTENSION pg_cron;
```

テストのため、先ほどの `coffee_shops` テーブルからランダムな名前を取得し、テーブルの内容を設定して `the_best_coffee_shop` を作成します。

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

`cron.schedule` といくつかの SQL ステートメントを使用して、ランダムなテーブル名を取得 (分散クエリの結果を格納するために一時テーブルを使用することに注意) し、`the_best_coffee_shop` に格納できます。

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

これで、1 分ごとに、別の名前を取得するようになります。

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

構文の詳細については、[pg_cron README](https://github.com/citusdata/pg_cron) を参照してください。

>[!NOTE]
>`citus` 拡張機能の削除はサポートされていません。 `citus` 拡張機能は、Hyperscale エクスペリエンスを提供するために必要です。

