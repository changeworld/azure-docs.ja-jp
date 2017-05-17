---
title: "Azure Database for PostgreSQL からの Python での読み取り | Microsoft Docs"
description: "予備知識がないことを前提に、Python コード サンプルを実行して、Azure Database for PostgreSQL のテーブルのデータを読み書きする方法について説明します。"
services: postgresql
author: MightyPen
ms.author: genemi
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 42f5e8191e52d1e2a4502a961158b6fec1c061d1
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---
# <a name="python-reading-from-azure-database-for-postgresql"></a>Azure Database for PostgreSQL からの Python での読み取り


この記事では、SQL SELECT ステートメントを使用してテーブルから行を読み取る短い Python プログラムについて説明します。 また、Python プログラムを実行するために必要なすべての前提条件を確認し、インストールする方法についても説明します。

ここで説明する Python サンプル プログラムと Python 関連ツールはすべて、Linux、Mac、Windows など、さまざまなプラットフォームに等しく適切に適用されます。 


## <a name="install-the-python-interpreter"></a>Python インタープリターのインストール


この記事の Python コード サンプルは、Python インタープリター バージョン 2.7 を対象としています。バージョン 3.x で実行することはできません。

Python インタープリター バージョン 2.7 をダウンロードし、インストールしてください。

- [python.org からの Python インタープリターのダウンロード](https://www.python.org/downloads/)

インストール後、コマンド ラインでインタープリターを見つけて、実行できることを確かめます。 たとえば、次のコマンドを使用します。

`python.exe -?`


## <a name="install-pipexe-the-python-module-installer"></a>pip.exe (Python モジュールのインストーラー) のインストール


Python インタープリターをインストールすると、一緒に pip.exe がインストールされる可能性があります。このインストーラーは、おそらく *Scripts/* という名前のサブディレクトリで見つかります。 pip.exe を実行すると、特殊な Python モジュールがインストールされます。 pip.exe を見つけて、実行できることを確かめてください。

`pip.exe`

pip.exe を実行できない場合は、**get-pip.py** という名前の Python ユーティリティ プログラム ファイルがあるかどうかを確認します。 get-pip.py が見つかった場合は、それを実行すると pip.exe を取得できます。

`python.exe get-pip.py`


## <a name="install-psycopg-the-connection-module"></a>psycopg (接続モジュール) のインストール


Python プログラムには、プログラムを Azure Database for PostgreSQL サーバーに接続する方法を認識するモジュールが必要です。 この接続モジュールの名前は **psycopg2** です。 詳細については、次を参照してください。

- [psycopg2 Web サイト](http://initd.org/psycopg/)

次の pip.exe インストール コマンドを使用して psycopg2 をインストールします。

`pip.exe install psycopg2`


## <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーの作成


Azure Database for PostgreSQL サーバーへのアクセス権がまだない場合は、次のドキュメントを参照してください。このドキュメントは、サーバーを作成する方法について説明しています。

- [Azure Portal を使用した Azure Database for PostgreSQL の作成](quickstart-create-server-database-portal.md)
- [Azure CLI を使用した Azure Database for PostgreSQL の作成](quickstart-create-server-database-azure-cli.md)


## <a name="obtain-the-connection-string-values"></a>接続文字列の値の取得


Azure Portal から Azure Database for PostgreSQL の接続文字列の値を取得できます。 次の表に示すパラメーター値が必要です。

- "*名前*" 列 &ndash; psycopg2 に必要なパラメーター識別子が示されています。
- "*-記号*" 列 &ndash; Python サンプル プログラム *PythonDriver.py* に必要なパラメーター識別子が示されています。


| 名前 | -Symbol | 値の例 |
| :--  | :--     | :--           |
| host | -h | myazurepostgresql.database.windows.net |
| ユーザー | -U | myalias@myazurepostgresql |
| dbname | -d | postgres<br />"*(すべての PostgreSQL サーバーに **postgres** という名前のデータベースがあります)*" |
| ポート | -p | 5432 "*(多くの場合、この特定値 5432)*" |
| パスワード | -P | MySecretPassword |
||||


## <a name="the-python-sample-program"></a>Python サンプル プログラム


このセクションでは、Python サンプル プログラムのソース コードを示します。 プログラムは、この記事の後半で実行します。

```python
# PythonDriver.py
# Python version 2.7

import psycopg2
import sys
import getopt

def main():

    host = ""
    user = ""
    dbname = ""
    port = ""
    password = ""

    try:
        opts, args = getopt.getopt(sys.argv[1:], "h:U:d:p:P:", [])
    except getopt.GetoptError as exc:
        print str(exc)
        usage()
        exit(2)

    for o, a in opts:
        if o == "-h": host = str(a)
        if o == "-U": user = str(a)
        if o == "-d": dbname = str(a)
        if o == "-p": port = str(a)
        if o == "-P": password = str(a)

    conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)

    try:
        # Create a connection.  Raise an exception if cannot connect.
        conn = psycopg2.connect(conn_string) 
        cursor = conn.cursor()

        #cursor.execute("DROP TABLE testpy1;")
        #conn.commit()

        cursor.execute("CREATE TABLE testpy1 (id serial PRIMARY KEY, num integer, data varchar);")

        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (100, "First'row"))
        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (110, "Second_row"))
        conn.commit()

        cursor.execute("SELECT id, num, data FROM testpy1;")
        result = cursor.fetchone()

        # Optionally, you can comment these clean-up lines to leave
        # the testpy1 table available for your inspection by pgAdmin. 
        cursor.execute("DROP TABLE testpy1;")
        conn.commit()

    except Exception as exc:
        print "FAILED ", exc
        exit(1)

    print "SUCCESS: ", result

if __name__ == "__main__":
    main()
```


## <a name="command-lines-to-run-the-sample-program"></a>サンプル プログラムを実行するコマンド ライン


このセクションでは、Python サンプル プログラムをテストするときに使用するコマンド ラインと、実際の確認出力を示します。

前に説明したパラメーターについては、ここで示すサンプル値を、実際に使用している接続文字列の値に置き換える必要があります。

前述した Python サンプル プログラムを実行するための正確な構文は、多少異なることがあります。 正確な構文は、使用しているオペレーティング システムとコンソール タイプに依存します。


#### <a name="windows-cmdexe-console"></a>Windows cmd.exe コンソール


次のコード ブロックは、Python サンプル プログラムの実際のテスト実行を示しています。 ここではシンプルな cmd.exe コマンド ラインが使用され、 各 "^" の後に、行連結記号が入力されました。

1. Enter キーが押されます。
2. **More?** という語句が表示されます。
3. 行全体の別の部分が入力され、以降、同様に続きます。

この行連結手法は、コード サンプルが長くなり、読みにくくなるのを避けるために、または印刷するときに使用されます。

```cmd
set Prompt=[$P\]$_$+$G$G$S

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>> .\python.exe ".\_myalias\PythonDriver.py" ^
More?  -h myazurepostgresql.database.windows.net ^
More?  -p 5432 ^
More?  -d postgres ^
More?  -U myalias@myazurepostgresql ^
More?  -P mySecretPassword
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>>
```

プログラム実行の確認として **SUCCESS** 行が表示されます。

もう 1 つの方法として、コマンドライン全体を .bat ファイルに配置することもできます。 この .bat ファイルは、cmd.exe コマンド ラインから実行できます。


#### <a name="powershell-file"></a>PowerShell ファイル


PowerShell コンソールでは、コマンド ラインは行連結文字をサポートしていません。 そこで、この PowerShell セクションでは、コマンドを PowerShell ファイルに追加します。 その後、PowerShell コマンド ラインからファイルを実行します。

次のコードを *PythonDriverRun.ps1* という名前のファイルにコピーします。

```powershell
# PythonDriverRun.ps1
cd C:\Users\myalias\AppData\Local\Programs\Python\Python27\

.\python.exe `
 .\_myalias\PythonDriver.py `
 -h myazurepostgresql.database.windows.net `
 -p 5432 `
 -d postgres `
 -U myalias@myazurepostgresql `
 -P mySecretPassword
```

次に示すように、PythonDriverRun.ps1 を実行します。 プログラムが確認として出力する **SUCCESS** 行が表示されます。

```cmd
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> .\_myalias\PythonDriverRun.ps1
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >>
```

PowerShell コマンド ラインで直接 PythonDriverRun.ps1 を実行する場合は、"&" 文字とスペースを先頭に追加する必要があります。 "&" を先頭に追加しないと、確認メッセージの表示時間が短すぎて、メッセージを確認できません。

```
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> & .\python.exe ".\_myalias\PythonDriver.py" -h myazurepostgresql.database.windows.net -p 5432 -d postgres -U myalias@myazurepostgresql -P mySecretPassword
SUCCESS:  (1, 100, "First'row")
```


## <a name="install-pgadmin-to-inspect-your-server"></a>サーバーを検査する pgAdmin のインストール


PythonDriverRun.ps1 プログラムは、作成した testpy1 テーブルを終了時に削除することで、自身をクリーンアップします。 **DROP TABLE** ステートメントを発行するソース コード行を、"#" を使ってコメント アウトするオプションも用意されています。 このオプションを使用すると、テーブルはそのまま残されるため後で検査できます。

pgAdmin ツールでは、任意の PostgreSQL サーバーと、サーバー内のオブジェクトを検査することができます。 Microsoft SQL Server または Azure SQL Database のユーザーは、SQL Server Management Studio (SSMS) と pgAdmin の類似性を確認できます。

必要に応じて、**pgAdmin** をインストールして、サーバーと **testpy1** テーブルを検査してください。


#### <a name="1-install-pgadmin"></a>1.pgAdmin をインストールする


pgAdmin のインストール手順については、次を参照してください。

- [http://www.pgadmin.org/](http://www.pgadmin.org/)

単に pgAdmin.exe ではなく、**pgAdmin4.exe** が実行可能ファイルの名前である可能性もあります。

pgAdmin4.exe を Windows コンピューターで実行するには、コマンド ラインで次のようなコマンドを入力します。

`"C:\Program Files (x86)\pgAdmin 4\v1\runtime\pgAdmin4.exe"`


#### <a name="2-connect-pgadmin-to-your-server"></a>2.pgAdmin をサーバーに接続する

 
pgAdmin UI が表示されたら、**ブラウザー** ウィンドウを見つけます。 次に、右クリックによって **[サーバー]** > **[作成]** > **[サーバー]** の順に選択します。 ここで、"*作成*" という用語は、既存の PostgreSQL サーバー (任意の Azure Database for PostgreSQL サーバーを含む) への "*接続*" を確立することを意味します。

接続を確立すると、オブジェクトのツリーが**ブラウザー** ウィンドウに表示されます。


#### <a name="3-navigate-in-the-pgadmin-tree-to-your-table"></a>手順 3.pgAdmin ツリーでテーブルに移動する


テーブルを表示するには、ツリーの要素を次のように展開します。

- **[サーバー]** &gt; *[YourServerHere]* &gt; **[データベース]** &gt; [postgres] &gt; **[スキーマ]** &gt; [public] &gt; **[テーブル]** &gt; [testpy1]

![ツリーでテーブル testpy1 を示す pgAdmin](./media/connect-python/pgAdmin-postgresql-table-testpy1.jpg)


#### <a name="4-drop-the-testpy1-table"></a>4.testpy1 テーブルを削除する


最終的なクリーンアップについては、[testpy1] ノードを右クリックし、**[削除]/[ドロップ]** をクリックします。


## <a name="next-steps"></a>次のステップ

- [Azure SQL Database への Python での接続](../sql-database/sql-database-connect-query-python.md)
- [Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)

