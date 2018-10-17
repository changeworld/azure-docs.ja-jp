---
title: MySQL Workbench を使用して Azure Database for MariaDB に接続する
description: このクイックスタートでは、MySQL Workbench を使用して、Azure Database for MariaDB に接続のうえデータを照会するための手順を紹介します。
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: b3a4d00381361b5299e86b959d9775318ae81e88
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998244"
---
# <a name="azure-database-for-mariadb-use-mysql-workbench-to-connect-and-query-data"></a>Azure Database for MariaDB: MySQL Workbench を使用した接続とデータの照会
このクイックスタートでは、MySQL Workbench アプリケーションを使用して Azure Database for MariaDB に接続する方法を紹介します。 

## <a name="prerequisites"></a>前提条件
このクイックスタートでは、次のいずれかのガイドで作成されたリソースを出発点として使用します。
- [Azure portal を使用した Azure Database for MariaDB サーバーの作成](./quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Azure CLI を使用した Azure Database for MariaDB サーバーの作成](./quickstart-create-mariadb-server-database-using-azure-cli.md)

## <a name="install-mysql-workbench"></a>MySQL Workbench のインストール
[MySQL Web サイト](https://dev.mysql.com/downloads/workbench/)から MySQL Workbench をダウンロードしてお使いのコンピューターにインストールします。

## <a name="get-connection-information"></a>接続情報の取得
Azure Database for MariaDB に接続するために必要な接続情報を取得します。 完全修飾サーバー名とログイン資格情報が必要です。

1. [Azure Portal](https://portal.azure.com/) にログインします。

2. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、作成したサーバー (例: **mydemoserver**) を検索します。

3. サーバー名をクリックします。

4. サーバーの **[概要]** パネルから、**[サーバー名]** と **[サーバー管理者ログイン名]** を書き留めます。 パスワードを忘れた場合も、このパネルからパスワードをリセットすることができます。
 ![Azure Database for MariaDB サーバー名](./media/connect-workbench/1_server-overview-name-login.png)

## <a name="connect-to-server-using-mysql-workbench"></a>MySQL Workbench を使用したサーバーへの接続 
MySQL Workbench を使用して Azure Database for MariaDB サーバーに接続するには、次の手順を使用します。

1.  コンピューター上で MySQL Workbench アプリケーションを起動します。 

2.  **[Setup New Connection]\(新しい接続のセットアップ\)** ダイアログ ボックスで、次の情報を **[Parameters]\(パラメーター\)** タブに入力します。

    ![新しい接続のセットアップ](./media/connect-workbench/2-setup-new-connection.png)

    | **設定** | **推奨値** | **フィールドの説明** |
    |---|---|---|
    |   接続名 | Demo Connection | この接続のラベルを指定します。 |
    | 接続方法 | Standard (TCP/IP) | Standard (TCP/IP) で十分です。 |
    | ホスト名 | *サーバー名* | Azure Database for MariaDB を作成するときに使用したサーバー名の値を指定します。 この例におけるサーバーは、mydemoserver.mariadb.database.azure.com です。 例で示されているように、完全修飾ドメイン名 (\*.mariadb.database.azure.com) を使用します。 サーバー名を覚えていない場合は、前のセクションの手順に従って接続情報を取得します。  |
    | ポート | 3306 | Azure Database for MariaDB に接続する場合は、常にポート 3306 を使用します。 |
    | ユーザー名 |  *サーバー管理者ログイン名* | 前に Azure Database for MariaDB を作成したときに指定したサーバー管理者ログイン ユーザー名を入力します。 この例のユーザー名は myadmin@mydemoserver です。 ユーザー名を覚えていない場合は、前のセクションの手順に従って接続情報を取得します。 形式は *username@servername* です。
    | パスワード | パスワード | **[Store in Vault]\(コンテナーに保存\)** ボタンをクリックしてパスワードを保存します。 |

3.   **[Test Connection] \(接続のテスト)** をクリックして、すべてのパラメーターが正しく構成されているかをテストします。 

4.   **[OK]** をクリックして接続を保存します。 

5.   **[MySQL Connections]\(MySQL 接続\)** の一覧で、対象のサーバーに対応するタイルをクリックし、接続が確立するのを待ちます。

        新しい SQL タブが開き、クエリを入力できる空白のエディターが表示されます。
    
        > [!NOTE]
        > 既定では、SSL 接続のセキュリティは必須であり、Azure Database for MariaDB サーバーに適用されます。 通常、MySQL ワークベンチがサーバーに接続するために、SSL 証明書を使用した追加構成は必要ありませんが、SSL CA 証明書と MySQL Workbench のバインドをお勧めします。 SSL を無効にしたい場合は、Azure Portal にアクセスし、[接続のセキュリティ] ページをクリックして、[強制 SSL 接続] トグル ボタンをオフにして無効にします。

## <a name="create-table-insert-read-update-and-delete-data"></a>テーブルを作成し、データを挿入、読み取り、更新、削除する
1. いくつかのサンプル データを示すために、サンプル SQL コードをコピーして空の SQL タブに貼り付けます。

    このコードでは、quickstartdb という名前の空のデータベースを作成し、inventory という名前のサンプル テーブルを作成します。 いくつかの行を挿入した後、行を読み取ります。 次に、更新ステートメントによってデータを変更し、行を再度読み取ります。 最後に、1 つの行を削除した後、行を再度読み取ります。
    
    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;
    
    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    
    -- Read
    SELECT * FROM inventory;
    
    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;
    
    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    このスクリーンショットは、SQL Workbench に表示された SQL コードとその実行後の出力の例を示しています。
    
    ![サンプル SQL コードを実行するための MySQL Workbench の SQL タブ](media/connect-workbench/3-workbench-sql-tab.png)

2. サンプル SQL コードを実行するには、**[SQL File]\(SQL ファイル\)** タブのツールバーにある稲妻アイコンをクリックします。
3. ページ中央の **[結果グリッド]** セクションの 3 つのタブに結果が表示されていることに注目してください。 
4. ページの下部にある **[Output]\(出力\)** リストに注目してください。 各コマンドの状態が表示されています。 

これで、MySQL Workbench を使用して Azure Database for MariaDB に接続し、SQL 言語を使用してデータを照会できました。

<!--
## Next steps
> [!div class="nextstepaction"]
> [Migrate your database using Export and Import](./concepts-migrate-import-export.md)
-->