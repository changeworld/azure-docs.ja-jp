---
title: "Python から Azure Database for PostgreSQL に接続する | Microsoft Docs"
description: "Azure Database for PostgreSQL に接続してデータを照会するために使用できる、Python コード サンプルを紹介します。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: python
ms.topic: hero-article
ms.date: 06/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 0027d25bcaa3376c5a29299f3ec88809ebf1d2d8
ms.contentlocale: ja-jp
ms.lasthandoff: 06/26/2017

---
<a id="azure-database-for-postgresql-use-python-to-connect-and-query-data" class="xliff"></a>

# Azure Database for PostgreSQL: Python を使用した接続とデータの照会
このクイックスタートでは、Mac OS、Ubuntu Linux、Windows の各プラットフォームから [Python](https://python.org) を使用して Azure Database for PostgreSQL に接続し、SQL ステートメントを使用してデータベース内のデータを照会、挿入、更新、削除する方法について説明します。 この記事の手順では、Python を使用した開発には慣れているものの、Azure Database for PostgreSQL の使用は初めてであるユーザーを想定しています。

<a id="prerequisites" class="xliff"></a>

## 前提条件
このクイックスタートでは、次のいずれかのガイドで作成されたリソースを出発点として使用します。
- [DB の作成 - ポータル](quickstart-create-server-database-portal.md)
- [DB の作成 - CLI](quickstart-create-server-database-azure-cli.md)

以下も必要です。
- [python](https://www.python.org/downloads/) がインストールされていること。
- [pip](https://pip.pypa.io/en/stable/installing/) パッケージがインストールされていること ([python.org](https://python.org) からダウンロードした Python 2 (2.7.9 以上) または Python 3 (3.4 以上) のバイナリをご利用の場合、pip は既にインストールされていますが、pip のアップグレードが必要となります)。

<a id="install-the-python-connection-libraries-for-postgresql" class="xliff"></a>

## PostgreSQL 用 Python 接続ライブラリのインストール
[psycopg2](http://initd.org/psycopg/docs/install.html) パッケージをインストールします。これにより、データベースに接続してクエリを実行できるようになります。 psycopg2 は、最も一般的なプラットフォーム (Linux、OSX、Windows) を対象に [wheel](http://pythonwheels.com/) パッケージ形式で [PyPI に公開](https://pypi.python.org/pypi/psycopg2/)されているため、pip install を使用して、すべての依存関係を含んだバイナリ バージョンのモジュールを取得できます。

```cmd
pip install psycopg2
```
必ず最新バージョンの pip を使用してください (「`pip install -U pip`」のようにしてアップグレードできます)。

<a id="get-connection-information" class="xliff"></a>

## 接続情報の取得
Azure Database for PostgreSQL に接続するために必要な接続情報を取得します。 完全修飾サーバー名とログイン資格情報が必要です。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. Azure ポータルの左側のメニューにある **[すべてのリソース]** をクリックし、作成したばかりのサーバー「**mypgserver-20170401**」を検索します。
3. サーバー名 **[mypgserver-20170401]** をクリックします。
4. サーバーの **[概要]** ページを選択します。 **[サーバー名]** と **[サーバー管理者ログイン名]** の値を書き留めておきます。
 ![Azure Database for PostgreSQL - サーバー管理者ログイン](./media/connect-python/1-connection-string.png)
5. サーバーのログイン情報を忘れた場合は、**[概要]** ページに移動して、サーバー管理者ログイン名を確認し、必要に応じてパスワードをリセットします。
   
<a id="connect-create-table-and-insert-data" class="xliff"></a>

## 接続、テーブルの作成、データの挿入
[psycopg2.connect](http://initd.org/psycopg/docs/connection.html) 関数と共に **INSERT** SQL ステートメントを使用して、接続してデータを読み込むには、次のコードを使用します。 PostgreSQL データベースに対して SQL クエリを実行するには、[cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 関数を使用します。 host、dbname、user、password の各パラメーターは、サーバーとデータベースの作成時に指定した値に置き換えてください。

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print "Finished dropping table (if existed)"

# Create table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print "Finished creating table"

# Insert some data into table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print "Inserted 3 rows of data"

conn.commit()
```

<a id="read-data" class="xliff"></a>

## データの読み取り
[cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 関数と共に **SELECT** SQL ステートメントを使用して、挿入したデータを読み取るには、次のコードを使用します。 この関数はクエリを受け取り、[cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) を使用して反復処理できる結果セットを返します。 host、dbname、user、password の各パラメーターは、サーバーとデータベースの作成時に指定した値に置き換えてください。

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print "Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2]))

conn.commit()
```

<a id="update-data" class="xliff"></a>

## データの更新
[cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 関数と共に **UPDATE** SQL ステートメントを使用して、先ほど挿入した inventory 行を更新するには、次のコードを使用します。 host、dbname、user、password の各パラメーターは、サーバーとデータベースの作成時に指定した値に置き換えてください。

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print "Updated 1 row of data"

conn.commit()
```

<a id="delete-data" class="xliff"></a>

## データの削除
[cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 関数と共に **DELETE** SQL ステートメントを使用して、先ほど挿入した inventory 項目を削除するには、次のコードを使用します。 host、dbname、user、password の各パラメーターは、サーバーとデータベースの作成時に指定した値に置き換えてください。

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print ("Deleted 1 row of data")

conn.commit()
```

<a id="next-steps" class="xliff"></a>

## 次のステップ
> [!div class="nextstepaction"]
> [エクスポートとインポートを使用したデータベースの移行](./howto-migrate-using-export-and-import.md)

