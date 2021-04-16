---
title: 'クイックスタート: Rust を使用して接続する - Azure Database for PostgreSQL (単一サーバー)'
description: このクイックスタートでは、Azure Database for PostgreSQL (単一サーバー) に接続してデータを照会するために使用できる、Rust のコード サンプルを紹介します。
author: abhirockzz
ms.author: abhishgu
ms.service: postgresql
ms.devlang: rust
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: 00adc50ac14e627eb356a3e62ce0faa5a9716aa3
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505559"
---
# <a name="quickstart-use-rust-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>クイックスタート: Rust を使用して Azure Database for PostgreSQL (単一サーバー) に接続してデータを照会する

この記事では、[Rust 用 PostgreSQL ドライバー](https://github.com/sfackler/rust-postgres)を使用して Azure Database for PostgreSQL を操作する方法を、サンプル コードに実装されている CRUD (作成、読み取り、更新、削除) 操作を通じて見ていきます。 最後に、ローカルからアプリケーションを実行して、その動作を確認することができます。

## <a name="prerequisites"></a>前提条件
このクイックスタートでは、以下が必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free)。
- [Rust](https://www.rust-lang.org/tools/install) の最新バージョンがインストールされていること。
- Azure Database for PostgreSQL の単一サーバー - [Azure portal](./quickstart-create-server-database-portal.md) <br/> または [Azure CLI](./quickstart-create-server-database-azure-cli.md) を使用して作成できます。
- パブリック アクセスとプライベート アクセスのどちらを使用しているかに基づいて、次の **いずれか** のアクションを実行して、接続を有効にします。

  |アクション| 接続方法|ハウツー ガイド|
  |:--------- |:--------- |:--------- |
  | **ファイアウォール規則を構成する** | パブリック | [ポータル](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **サービス エンドポイントを構成する** | パブリック | [ポータル](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **プライベート リンクを構成する** | プライベート | [ポータル](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Git](https://git-scm.com/downloads) のインストール。

## <a name="get-database-connection-information"></a>データベース接続情報を取得する
Azure Database for PostgreSQL データベースに接続するには、完全修飾サーバー名とログイン資格情報が必要です。 この情報は Azure portal から取得できます。

1. [Azure portal](https://portal.azure.com/) で、ご利用の Azure Database for PostgreSQL サーバー名を検索して選択します。
1. サーバーの **[概要]** ページで、完全修飾 **サーバー名** と **管理者ユーザー名** をコピーします。 完全修飾 **サーバー名** は常に *\<my-server-name>.postgres.database.azure.com* の形式になり、**管理者ユーザー名** は常に *\<my-admin-username>@\<my-server-name>* の形式になります。

## <a name="review-the-code-optional"></a>コードの確認 (省略可能)

コードのしくみに関心がある場合は、以下のスニペットで確認できます。 それ以外の場合は、「[アプリケーションの実行](#run-the-application)」に進んでかまいません。

### <a name="connect"></a>接続

`main` 関数は Azure Database for PostgreSQL に接続することによって開始され、接続情報を表す環境変数 `POSTGRES_HOST`、`POSTGRES_USER`、`POSTGRES_PASSWORD`、および `POSTGRES_DBNAME` に依存します。 既定では、PostgreSQL データベース サービスは `TLS` 接続を要求するように構成されます。 クライアント アプリケーションで `TLS` 接続がサポートされていない場合は、`TLS` の要求を無効にすることもできます。 詳細については、「[Azure Database for PostgreSQL (単一サーバー) で TLS 接続を構成する](./concepts-ssl-connection-security.md)」を参照してください。

この記事のサンプル アプリケーションでは、[postgres-openssl crate](https://crates.io/crates/postgres-openssl/) を使って TLS を使用します。 [postgres::Client::connect](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.connect) 関数は、接続を開始するために使用されます。これが失敗した場合、プログラムは終了します。

```rust
fn main() {
    let pg_host = std::env::var("POSTGRES_HOST").expect("missing environment variable POSTGRES_HOST");
    let pg_user = std::env::var("POSTGRES_USER").expect("missing environment variable POSTGRES_USER");
    let pg_password = std::env::var("POSTGRES_PASSWORD").expect("missing environment variable POSTGRES_PASSWORD");
    let pg_dbname = std::env::var("POSTGRES_DBNAME").unwrap_or("postgres".to_string());

    let builder = SslConnector::builder(SslMethod::tls()).unwrap();
    let tls_connector = MakeTlsConnector::new(builder.build());

    let url = format!(
        "host={} port=5432 user={} password={} dbname={} sslmode=require",
        pg_host, pg_user, pg_password, pg_dbname
    );
    let mut pg_client = postgres::Client::connect(&url, tls_connector).expect("failed to connect to postgres");
...
}
```

### <a name="drop-and-create-table"></a>テーブルの削除と作成

サンプル アプリケーションでは、簡単な `inventory` テーブルを使用して CRUD (作成、読み取り、更新、削除) 操作を示します。

```sql
CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
```

`drop_create_table` 関数は、新しいテーブルを作成する前に、まず `inventory` テーブルの `DROP` を試みます。 これにより、既知の (クリーンな) 状態から開始することができるので、学習や実験がやりやすくなります。 [execute](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.execute) メソッドは、作成および削除操作に使用されます。 

```rust
const CREATE_QUERY: &str =
    "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";

const DROP_TABLE: &str = "DROP TABLE inventory";

fn drop_create_table(pg_client: &mut postgres::Client) {
    let res = pg_client.execute(DROP_TABLE, &[]);
    match res {
        Ok(_) => println!("dropped table"),
        Err(e) => println!("failed to drop table {}", e),
    }
    pg_client
        .execute(CREATE_QUERY, &[])
        .expect("failed to create 'inventory' table");
}
```

### <a name="insert-data"></a>データの挿入

`insert_data` は、`inventory` テーブルにエントリを追加します。 これは、[prepare](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare) 関数を使用して、[準備されたステートメント](https://docs.rs/postgres/0.19.0/postgres/struct.Statement.html)を作成します。 


```rust
const INSERT_QUERY: &str = "INSERT INTO inventory (name, quantity) VALUES ($1, $2) RETURNING id;";

fn insert_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare(&INSERT_QUERY)
        .expect("failed to create prepared statement");

    let row = pg_client
        .query_one(&prep_stmt, &[&"item-1", &42])
        .expect("insert failed");

    let id: i32 = row.get(0);
    println!("inserted item with id {}", id);
...
}
```

また、クエリ パラメーターの型を明示的に指定できるようにする [prepare_typed](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare_typed) メソッドの使用法にもご注意ください。

```rust
...
let typed_prep_stmt = pg_client
        .prepare_typed(&INSERT_QUERY, &[Type::VARCHAR, Type::INT4])
        .expect("failed to create prepared statement");

let row = pg_client
        .query_one(&typed_prep_stmt, &[&"item-2", &43])
        .expect("insert failed");

let id: i32 = row.get(0);
println!("inserted item with id {}", id);
...
```

最後に、`for` ループを使用して `item-3`、`item-4`、および `item-5` を追加し、それぞれにランダムに生成された数量を指定します。

```rust
...
    for n in 3..=5 {
        let row = pg_client
            .query_one(
                &typed_prep_stmt,
                &[
                    &("item-".to_owned() + &n.to_string()),
                    &rand::thread_rng().gen_range(10..=50),
                ],
            )
            .expect("insert failed");

        let id: i32 = row.get(0);
        println!("inserted item with id {} ", id);
    }
...
```

### <a name="query-data"></a>クエリ データ

`query_data` 関数は、`inventory` テーブルからデータを取得する方法を示しています。 [query_one](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query_one) メソッドを使用して、項目の `id` で項目を取得します。 

```rust
const SELECT_ALL_QUERY: &str = "SELECT * FROM inventory;";
const SELECT_BY_ID: &str = "SELECT name, quantity FROM inventory where id=$1;";

fn query_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare_typed(&SELECT_BY_ID, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item_id = 1;

    let c = pg_client
        .query_one(&prep_stmt, &[&item_id])
        .expect("failed to query item");

    let name: String = c.get(0);
    let quantity: i32 = c.get(1);
    println!("quantity for item {} = {}", name, quantity);
...
}
```

`select * from` クエリと [query](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query) メソッドを使用して、inventory テーブル内のすべての行をフェッチします。 各列の値を抽出するために、[get](https://docs.rs/postgres/0.19.0/postgres/row/struct.Row.html#method.get) を使用して、返された行を反復処理します。 

> [!TIP]
> `get` では、行の数値インデックスまたは列名を使用して、列を指定できることに注意してください。

```rust
...
    let items = pg_client
        .query(SELECT_ALL_QUERY, &[])
        .expect("select all failed");

    println!("listing items...");

    for item in items {
        let id: i32 = item.get("id");
        let name: String = item.get("name");
        let quantity: i32 = item.get("quantity");
        println!(
            "item info: id = {}, name = {}, quantity = {} ",
            id, name, quantity
        );
    }
...
```

### <a name="update-data"></a>データの更新

`update_date` 関数は、すべての項目の数量をランダムに更新します。 `insert_data` 関数によって `5` 行が追加されているため、`for` ループではこのことが考慮されます - `for n in 1..=5`

> [!TIP]
> `execute` ではなく `query` を使用していることに注意してください。これは、`id` と新しく生成された `quantity` を ([RETURNING 句](https://www.postgresql.org/docs/current/dml-returning.html)を使用して) 取得するためです。

```rust
const UPDATE_QUERY: &str = "UPDATE inventory SET quantity = $1 WHERE name = $2 RETURNING quantity;";

fn update_data(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&UPDATE_QUERY, &[Type::INT4, Type::VARCHAR])
        .expect("failed to create prepared statement");

    for id in 1..=5 {
        let row = pg_client
            .query_one(
                &stmt,
                &[
                    &rand::thread_rng().gen_range(10..=50),
                    &("item-".to_owned() + &id.to_string()),
                ],
            )
            .expect("update failed");
        
        let quantity: i32 = row.get("quantity");
        println!("updated item id {} to quantity = {}", id, quantity);
    }
}
```

### <a name="delete-data"></a>データの削除

最後に、`delete` 関数は、`id` を使って `inventory` テーブルから項目を削除する方法を示しています。 `id` はランダムに選択されます。これは、最初に `insert_data` 関数によって`5` 行が追加されたため、`1` から `5` まで (`5` を含む) のランダムな整数となります。 

> [!TIP]
> `execute` ではなく `query` を使用していることに注意してください。これは、先ほど削除した項目に関する情報を ([RETURNING 句](https://www.postgresql.org/docs/current/dml-returning.html)を使用して) 取得するためです。

```rust
const DELETE_QUERY: &str = "DELETE FROM inventory WHERE id = $1 RETURNING id, name, quantity;";

fn delete(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&DELETE_QUERY, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item = pg_client
        .query_one(&stmt, &[&rand::thread_rng().gen_range(1..=5)])
        .expect("delete failed");

    let id: i32 = item.get(0);
    let name: String = item.get(1);
    let quantity: i32 = item.get(2);
    println!(
        "deleted item info: id = {}, name = {}, quantity = {} ",
        id, name, quantity
    );
}
```

## <a name="run-the-application"></a>アプリケーションの実行

1. まず次のコマンドを実行して、サンプル リポジトリをクローンします。

    ```bash
    git clone https://github.com/Azure-Samples/azure-postgresql-rust-quickstart.git
    ```

2. Azure portal からコピーした値を使用して、必要な環境変数を設定します。

    ```bash
    export POSTGRES_HOST=<server name e.g. my-server.postgres.database.azure.com>
    export POSTGRES_USER=<admin username e.g. my-admin-user@my-server>
    export POSTGRES_PASSWORD=<admin password>
    export POSTGRES_DBNAME=<database name. it is optional and defaults to postgres>
    ```

3. アプリケーションを実行するには、それをクローンしたディレクトリに移動し、`cargo run` を実行します。

    ```bash
    cd azure-postgresql-rust-quickstart
    cargo run
    ```

    次のような出力が表示されます。

    ```bash
    dropped 'inventory' table
    inserted item with id 1
    inserted item with id 2
    inserted item with id 3 
    inserted item with id 4 
    inserted item with id 5 
    quantity for item item-1 = 42
    listing items...
    item info: id = 1, name = item-1, quantity = 42 
    item info: id = 2, name = item-2, quantity = 43 
    item info: id = 3, name = item-3, quantity = 11 
    item info: id = 4, name = item-4, quantity = 32 
    item info: id = 5, name = item-5, quantity = 24 
    updated item id 1 to quantity = 27
    updated item id 2 to quantity = 14
    updated item id 3 to quantity = 31
    updated item id 4 to quantity = 16
    updated item id 5 to quantity = 10
    deleted item info: id = 4, name = item-4, quantity = 16 
    ```

4. 確認するには、[psql を使用して](./quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) Azure Database for PostgreSQL に接続し、データベースに対してクエリを実行することもできます。次に例を示します。

    ```sql
    select * from inventory;
    ```

[問題がある場合は、お知らせください](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで使用したすべてのリソースをクリーンアップするには、次のコマンドを使用してリソース グループを削除します。

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [ポータルを使用して Azure Database for PostgreSQL サーバーを管理する](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [CLI を使用して Azure Database for PostgreSQL サーバーを管理する](./how-to-manage-server-cli.md)<br/>

[お探しの情報が見つからない場合は、お知らせください。](https://aka.ms/postgres-doc-feedback)
