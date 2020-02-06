---
title: Java を使用してデータベースを照会する
description: Azure SQL データベースに接続して T-SQL ステートメントを使用してデータベースに照会するプログラムを Java を使用して作成する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-java-july2019. seo-java-august2019
ms.openlocfilehash: 034f92ca3b7552373ae69148d09d58d3a5dd166a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768653"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>クイック スタート:Java を使用して Azure SQL データベースに照会する

このクイックスタートでは、Java を使用して Azure SQL データベースに接続し、T-SQL ステートメントを使用してデータを照会します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure SQL データベース](sql-database-single-database-get-started.md)
- [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) 関連のソフトウェア

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  「[macOS で SQL Server を使用して Java アプリを作成する](https://www.microsoft.com/sql-server/developer-get-started/java/mac/)」の手順 **1.2** と **1.3** を使用して、Homebrew と Java をインストールした後、Maven をインストールします。

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  「[Ubuntu で SQL Server を使用して Java アプリを作成する](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/)」の手順 **1.2**、**1.3**、および **1.4** を使用して、Java と Java Development Kit をインストールした後、Maven をインストールします。

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  「[Windows で SQL Server を使用して Java アプリを作成する](https://www.microsoft.com/sql-server/developer-get-started/java/windows/)」の手順 **1.2** と **1.3** を使用して、Java をインストールした後、Maven をインストールします。

  ---

> [!IMPORTANT]
> この記事のスクリプトは、**Adventure Works** データベースを使用するように記述されています。

> [!NOTE]
> オプションで、Azure SQL マネージド インスタンスの使用を選択できます。
>
> 作成して構成するには、[Azure portal](sql-database-managed-instance-get-started.md)、[PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)、または [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) を使用して、[オンサイト](sql-database-managed-instance-configure-p2s.md)または [VM](sql-database-managed-instance-configure-vm.md) 接続を設定します。
>
> データを読み込む方法については、[Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ファイルの [BACPAC を使用した復元](sql-database-import.md)に関するページを参照するか、[Wide World Importers データベースの復元](sql-database-managed-instance-get-started-restore.md)に関するページを参照してください。

## <a name="get-sql-server-connection-information"></a>SQL サーバーの接続情報を取得する

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. **[SQL データベース]** を選択するか、 **[SQL マネージド インスタンス]** ページを開きます。

3. **[概要]** ページで、単一データベースの場合は **[サーバー名]** の横の完全修飾サーバー名を確認し、マネージド インスタンスの場合は **[ホスト]** の横の完全修飾サーバー名を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。 

## <a name="create-the-project"></a>プロジェクトを作成する

1. コマンド プロンプトから *sqltest* という新しい Maven プロジェクトを作成します。

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. フォルダーを *sqltest* に移動し、任意のテキスト エディターで *pom.xml* を開きます。 次のコードを使用して、プロジェクトの依存関係に **Microsoft JDBC Driver for SQL Server** を追加します。

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. さらに *pom.xml* で、次のプロパティをプロジェクトに追加します。 properties セクションがない場合は、dependencies の後に追加してください。

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. *pom.xml* を保存して閉じます。

## <a name="add-code-to-query-database"></a>データベースに照会するためのコードを追加する

1. Maven プロジェクトに *App.java* というファイルがあらかじめ存在している必要があります (

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*)。

1. そのファイルを開いて、その内容を次のコードに置き換えます。 そのうえで、サーバー、データベース、ユーザー、パスワードの適切な値を入力してください。

    ```java
    package com.sqldbsamples;

    import java.sql.Connection;
    import java.sql.Statement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.DriverManager;

    public class App {

        public static void main(String[] args) {

            // Connect to database
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
            Connection connection = null;

            try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName "
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";

                try (Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql)) {

                    // Print results from select statement
                    System.out.println("Top 20 categories:");
                    while (resultSet.next())
                    {
                        System.out.println(resultSet.getString(1) + " "
                            + resultSet.getString(2));
                    }
                    connection.close();
                }
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

   > [!NOTE]
   > このコード例では、Azure SQL に **AdventureWorksLT** サンプル データベースを使用します。

## <a name="run-the-code"></a>コードの実行

1. コマンド プロンプトでアプリを実行します。

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. 先頭から 20 行が返されることを確認して、アプリ ウィンドウを閉じます。

## <a name="next-steps"></a>次のステップ

- [最初の Azure SQL データベースの設計](sql-database-design-first-database.md)  

- [SQL Server 用 Microsoft JDBC ドライバー](https://github.com/microsoft/mssql-jdbc)  

- [問題の報告/質問](https://github.com/microsoft/mssql-jdbc/issues)  
