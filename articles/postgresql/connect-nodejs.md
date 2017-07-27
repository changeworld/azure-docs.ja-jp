---
title: "Node.js から Azure Database for PostgreSQL に接続する | Microsoft Docs"
description: "このクイックスタートでは、Azure Database for PostgreSQL に接続してデータを照会するために使用できる、Node.js コード サンプルを紹介します。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 06/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 6a51fcf4f4494e5b32ccf6dabb19f8d004bb20d4
ms.contentlocale: ja-jp
ms.lasthandoff: 06/26/2017

---

<a id="azure-database-for-postgresql-use-nodejs-to-connect-and-query-data" class="xliff"></a>

# Azure Database for PostgreSQL: Node.js を使用した接続とデータの照会
このクイックスタートでは、Windows、Ubuntu Linux、Mac の各プラットフォームから [Node.js](https://nodejs.org/) を使用して Azure Database for PostgreSQL に接続する方法を紹介します。 ここでは、SQL ステートメントを使用してデータベース内のデータを照会、挿入、更新、削除する方法を説明します。 この記事の手順では、Node.js を使用した開発には慣れているものの、Azure Database for PostgreSQL の使用は初めてであるユーザーを想定しています。

<a id="prerequisites" class="xliff"></a>

## 前提条件
このクイックスタートでは、次のいずれかのガイドで作成されたリソースを出発点として使用します。
- [DB の作成 - ポータル](quickstart-create-server-database-portal.md)
- [DB の作成 - CLI](quickstart-create-server-database-azure-cli.md)

さらに、以下を実行する必要があります。
- [Node.js](https://nodejs.org)
- [pg](https://www.npmjs.com/package/pg) パッケージをインストします。 

<a id="install-nodejs" class="xliff"></a>

## Node.js のインストール 
Node.js のインストール方法は、ご使用のプラットフォームによって異なります。

<a id="mac-os" class="xliff"></a>

### **Mac OS**
次のコマンドを入力して **brew** をインストールします。これは、Mac OS X と **Node.js** に対応する使いやすいパッケージ マネージャーです。

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

<a id="linux-ubuntu" class="xliff"></a>

### **Linux (Ubuntu)**
次のコマンドを入力して、**Node.js** と、Node.js 用のパッケージ マネージャー **npm** をインストールします。

```bash
sudo apt-get install -y nodejs npm
```

<a id="windows" class="xliff"></a>

### **Windows**
[Node.js ダウンロード ページ](https://nodejs.org/en/download/)にアクセスし、必要な Windows インストーラー オプションを選択します。

<a id="install-pg-client" class="xliff"></a>

## pg クライアントのインストール
[pg](https://www.npmjs.com/package/pg) をインストールします。pg は、PostgreSQL に接続してクエリを実行するのに役立つ、node.js 用の純粋な JavaScript 非ブロッキング クライアントです。

インストールするには、コマンド ラインから JavaScript の npm (node package manager) を実行して、pg クライアントをインストールします。
```bash
npm install pg
```

インストールされたパッケージを一覧表示して確認します。
```bash
npm list
```
list コマンドの出力で、各コンポーネントのバージョンを確認します。 
```
`-- pg@6.2.3
  +-- buffer-writer@1.0.1
  +-- packet-reader@0.3.1
etc...
```

<a id="get-connection-information" class="xliff"></a>

## 接続情報の取得
Azure Database for PostgreSQL に接続するために必要な接続情報を取得します。 完全修飾サーバー名とログイン資格情報が必要です。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. Azure ポータルの左側のメニューにある **[すべてのリソース]** をクリックし、作成したばかりのサーバー「**mypgserver-20170401**」を検索します。
3. サーバー名 **[mypgserver-20170401]** をクリックします。
4. サーバーの **[概要]** ページを選択します。 **[サーバー名]** と **[サーバー管理者ログイン名]** の値を書き留めておきます。
 ![Azure Database for PostgreSQL - サーバー管理者ログイン](./media/connect-nodejs/1-connection-string.png)
5. サーバーのログイン情報を忘れた場合は、**[概要]** ページに移動して、サーバー管理者ログイン名を確認し、必要に応じてパスワードをリセットします。

<a id="running-the-javascript-code-in-nodejs" class="xliff"></a>

## Node.js での JavaScript コードの実行
Node.js は、bash シェルか Windows のコマンド プロンプトから「`node`」と入力して起動できます。起動後、サンプルの JavaScript コードをコピーし、プロンプトに貼り付けて対話形式で実行してください。 または、JavaScript コードをテキスト ファイルに保存し、パラメーターとしてそのファイル名を使用して `node filename.js` を起動して実行することもできます。

<a id="connect-create-table-and-insert-data" class="xliff"></a>

## 接続、テーブルの作成、データの挿入
接続し、**CREATE TABLE** および **INSERT INTO** SQL ステートメントを使用してデータを読み込むには、次のコードを使用します。
PostgreSQL サーバーとのインターフェイスには、[pg.Client](https://github.com/brianc/node-postgres/wiki/Client) オブジェクトを使用します。 サーバーへの接続を確立するには、[pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 関数を使用します。 PostgreSQL データベースに対して SQL クエリを実行するには、[pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 関数を使用します。 

host、dbname、user、password の各パラメーターは、サーバーとデータベースの作成時に指定した値に置き換えてください。 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }
});

function queryDatabase()
{
    client.query(
        ' \
            DROP TABLE IF EXISTS inventory; \
            CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER); \
            INSERT INTO inventory (name, quantity) VALUES (\'banana\', 150); \
            INSERT INTO inventory (name, quantity) VALUES (\'orange\', 154); \
            INSERT INTO inventory (name, quantity) VALUES (\'apple\', 100); \
        ',
        function (err)
    {
        console.log("Connection established");

        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;

                // Else closing connection finished without error
                console.log("Closed client connection");
            });

            console.log("Finished execution, exiting now");
            process.exit()
        }
    });
}
```

<a id="read-data" class="xliff"></a>

## データの読み取り
接続し、**SELECT** SQL ステートメントを使用してデータを読み取るには、次のコードを使用します。 PostgreSQL サーバーとのインターフェイスには、[pg.Client](https://github.com/brianc/node-postgres/wiki/Client) オブジェクトを使用します。 サーバーへの接続を確立するには、[pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 関数を使用します。 PostgreSQL データベースに対して SQL クエリを実行するには、[pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 関数を使用します。 

host、dbname、user、password の各パラメーターは、サーバーとデータベースの作成時に指定した値に置き換えてください。 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};


const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;

    else
    {
        console.log("Connected to Azure Database for PostgreSQL server:" + config.host);
        queryDatabase();
    }
});

function queryDatabase()
{
    // Declare array to hold query result set
    const results = [];

    console.log("Running query to PostgreSQL server:" + config.host);

    // Perform query
    var query = client.query('SELECT * FROM inventory;');

    // Print result set
    query.on('row', function(row)
    {
        console.log("Read " + JSON.stringify(row));
    });

    // Exit program after execution
    query.on('end', function(row)
    {
        console.log("Finished execution, exiting now");
        process.exit()
    });
}
```

<a id="update-data" class="xliff"></a>

## データの更新
接続し、**UPDATE** SQL ステートメントを使用してデータを読み取るには、次のコードを使用します。 PostgreSQL サーバーとのインターフェイスには、[pg.Client](https://github.com/brianc/node-postgres/wiki/Client) オブジェクトを使用します。 サーバーへの接続を確立するには、[pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 関数を使用します。 PostgreSQL データベースに対して SQL クエリを実行するには、[pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 関数を使用します。 

host、dbname、user、password の各パラメーターは、サーバーとデータベースの作成時に指定した値に置き換えてください。 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }   
});

function queryDatabase()
{
    client.query('UPDATE inventory SET quantity= 1000 WHERE name=\'banana\';', function (err, result)
    {
        console.log("Connection established");

        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;
                
                // Else closing connection finished without error
                console.log("Closed client connection");
            });             
        }

        console.log("Finished execution, exiting now");
        process.exit()
    });
}
```

<a id="delete-data" class="xliff"></a>

## データの削除
接続し、**DELETE** SQL ステートメントを使用してデータを削除するには、次のコードを使用します。 PostgreSQL サーバーとのインターフェイスには、[pg.Client](https://github.com/brianc/node-postgres/wiki/Client) オブジェクトを使用します。 サーバーへの接続を確立するには、[pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 関数を使用します。 PostgreSQL データベースに対して SQL クエリを実行するには、[pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 関数を使用します。 

host、dbname、user、password の各パラメーターは、サーバーとデータベースの作成時に指定した値に置き換えてください。 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }   
});

function queryDatabase()
{
    client.query('DELETE FROM inventory WHERE name=\'apple\';', function (err, result)
    {
        console.log("Connection established");
        
        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;
                
                // Else closing connection finished without error
                console.log("Closed client connection");
            });

            console.log("Finished execution, exiting now");
            process.exit()          
        }
    }); 
}
```

<a id="next-steps" class="xliff"></a>

## 次のステップ
> [!div class="nextstepaction"]
> [エクスポートとインポートを使用したデータベースの移行](./howto-migrate-using-export-and-import.md)

