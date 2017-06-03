---
title: "Java を使用して Azure SQL Database に接続する | Microsoft Docs"
description: "Azure SQL Database への接続とデータの照会に使用できる Java コード サンプルについて説明します。"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start connect, mvc
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 05/23/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 63ab7345b70d456d9d1ad23255d9ca7e777e161d
ms.contentlocale: ja-jp
ms.lasthandoff: 05/31/2017


---
# <a name="azure-sql-database-use-java-to-connect-and-query-data"></a>Azure SQL Database: Java を使って接続とデータの照会を行う

このクイック スタートでは、Mac OS、Ubuntu Linux、Windows の各プラットフォームから [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) を使用して Azure SQL Database に接続し、Transact-SQL ステートメントを使用してデータベース内のデータを照会、挿入、更新、削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、次のクイック スタートで作成されたリソースが出発点として使用されます。

- [DB の作成 - ポータル](sql-database-get-started-portal.md)
- [DB の作成 - CLI](sql-database-get-started-cli.md)
- [DB の作成 - PowerShell](sql-database-get-started-powershell.md)

## <a name="install-java-software"></a>Java ソフトウェアのインストール

このセクションの手順では、Java による開発には慣れていて、Azure SQL Database は初めて使用するユーザーを想定しています。 Java による開発の経験がない場合は、「[Build an app using SQL Server (SQL Server を使用してアプリを構築する)](https://www.microsoft.com/en-us/sql-server/developer-get-started/)」に移動し、**Java** を選択してから、使用しているオペレーティング システムを選択します。

### <a name="mac-os"></a>**Mac OS**
ターミナルを開き、Java プロジェクトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**brew** と **Maven** をインストールします。 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
ターミナルを開き、Java プロジェクトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**Maven** をインストールします。

```bash
sudo apt-get install maven
```

### <a name="windows"></a>**Windows**
公式インストーラーを使用して [Maven](https://maven.apache.org/download.cgi) をインストールします。 Maven を使用して、Java プロジェクトの依存関係の管理、ビルド、テスト、および実行を行うことができます。 

## <a name="get-connection-information"></a>接続情報の取得

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名、データベース名、ログイン情報が必要になります。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 
3. データベースの [**概要**] ページで、次の図に示すように、完全修飾サーバー名を確認します。 サーバー名をポイントすると、[**コピーするにはクリックします**] オプションが表示されます。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. サーバーのログイン情報を忘れた場合は、[SQL データベース サーバー] ページに移動して、サーバー管理者名を表示し、必要に応じてパスワードをリセットします。
5. **[データベース接続文字列の表示]** をクリックします。

6. 完全な **JDBC** 接続文字列を確認します。

    ![JDBC 接続文字列](./media/sql-database-connect-query-jdbc/jdbc-connection-string.png)    

### <a name="create-maven-project"></a>**Maven プロジェクトの作成**
ターミナルから新しい Maven プロジェクトを作成します。 
```bash
mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
```

**Microsoft JDBC Driver for SQL Server** を ***pom.xml*** 内の依存関係に追加します。 

```xml
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
</dependency>
```

## <a name="select-data"></a>データの選択

次のコードを使用して、カテゴリ別の上位 20 の製品を照会します。このコードでは、[connection](https://docs.microsoft.com/sql/connect/jdbc/working-with-a-connection) クラスと [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL ステートメントを使用します。 hostHame、dbName、ユーザー、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。 

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
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
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
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="insert-data"></a>データを挿入する

次のコードを使用して、SalesLT.Product テーブルに新しい製品を挿入します。このコードでは、[Prepared Statements](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) クラスと [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL ステートメントを使用します。 hostHame、dbName、ユーザー、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Insert data example:");
                System.out.println("=========================================");

                // Prepared statement to insert data
                String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, " 
                    + " StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

                java.util.Date date = new java.util.Date();
                java.sql.Timestamp sqlTimeStamp = new java.sql.Timestamp(date.getTime());

                try (PreparedStatement prep = connection.prepareStatement(insertSql)) {
                        prep.setString(1, "BrandNewProduct");
                        prep.setInt(2, 200989);
                        prep.setString(3, "Blue");
                        prep.setDouble(4, 75);
                        prep.setDouble(5, 80);
                        prep.setTimestamp(6, sqlTimeStamp);

                        int count = prep.executeUpdate();
                        System.out.println("Inserted: " + count + " row(s)");
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```
## <a name="update-data"></a>データの更新

次のコードを使用して、先ほど追加した新しい製品を更新します。このコードでは、[Prepared Statements](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) クラスと [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL ステートメントを使用して、Azure SQL Database のデータを更新します。 hostHame、dbName、ユーザー、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Update data example:");
                System.out.println("=========================================");

                // Prepared statement to update data
                String updateSql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?";

                try (PreparedStatement prep = connection.prepareStatement(updateSql)) {
                        prep.setString(1, "500");
                        prep.setString(2, "BrandNewProduct");

                        int count = prep.executeUpdate();
                        System.out.println("Updated: " + count + " row(s)")
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```



## <a name="delete-data"></a>データの削除

次のコードを使用して、先ほど追加した新しい製品を削除します。[Prepared Statements](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql)と [DELETE](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) Transact-SQL ステートメントを使用します。 hostHame、dbName、ユーザー、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Delete data example:");
                System.out.println("=========================================");

                // Prepared statement to delete data
                String deleteSql = "DELETE SalesLT.Product WHERE Name = ?";

                try (PreparedStatement prep = connection.prepareStatement(deleteSql)) {
                        prep.setString(1, "BrandNewProduct");

                        int count = prep.executeUpdate();
                        System.out.println("Deleted: " + count + " row(s)");
                }
        }        
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="next-steps"></a>次のステップ
- [最初の Azure SQL Database の設計](sql-database-design-first-database.md)
- [SQL Server 用 Microsoft JDBC ドライバー](https://github.com/microsoft/mssql-jdbc)
- [問題の報告/質問](https://github.com/microsoft/mssql-jdbc/issues)


