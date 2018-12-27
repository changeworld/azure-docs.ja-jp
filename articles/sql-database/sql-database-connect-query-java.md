---
title: Java を使用して Azure SQL Database に照会する | Microsoft Docs
description: Azure SQL データベースに接続して T-SQL ステートメントを使用してデータベースに照会するプログラムを Java を使用して作成する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/20/2018
ms.openlocfilehash: 5c12bd54c0ea96ac915fedab94f03cf044330dcf
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723297"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>クイック スタート: Java を使用して Azure SQL Database に照会する

この記事では、[Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) を使用して Azure SQL データベースに接続する方法を紹介します。 その後、T-SQL ステートメントを使用してデータを照会することができます。

## <a name="prerequisites"></a>前提条件

このサンプルを完了するには、次の前提条件を満たしている必要があります。

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- ご使用のコンピューターのパブリック IP アドレスに対する[サーバー レベルのファイアウォール規則](sql-database-get-started-portal-firewall.md)

- ご使用のオペレーティング システムに対応した、以下の Java 関連のソフトウェアをインストール済みであること。

  - **MacOS** では、Homebrew と Java をインストールした後、Maven をインストールします。 [手順 1.2. と手順 1.3.](https://www.microsoft.com/sql-server/developer-get-started/java/mac/) を参照してください。

  - **Ubuntu** では、Java と Java Development Kit をインストールした後、Maven をインストールします。 [手順 1.2.、手順 1.3.、手順 1.4.](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/) を参照してください。

  - **Windows** では、Java をインストールした後、Maven をインストールします。 [手順 1.2. と手順 1.3.](https://www.microsoft.com/sql-server/developer-get-started/java/windows/) を参照してください。

## <a name="get-database-connection"></a>データベース接続を取得する

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

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

## <a name="next-steps"></a>次の手順

- [最初の Azure SQL Database の設計](sql-database-design-first-database.md)  

- [SQL Server 用 Microsoft JDBC ドライバー](https://github.com/microsoft/mssql-jdbc)  

- [問題の報告/質問](https://github.com/microsoft/mssql-jdbc/issues)  
