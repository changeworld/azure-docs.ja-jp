---
title: Java を使用してデータベースを照会する
description: Java を使用して Azure SQL Database または Azure SQL Managed Instance 内のデータベースに接続し、T-SQL ステートメントを使用してクエリを実行するプログラムを作成する方法について説明します。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: seo-java-july2019. seo-java-august2019, sqldbrb=2 
ms.openlocfilehash: 6be52d2d3472888607bbd6276b4794184bb11273
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267394"
---
# <a name="quickstart-use-java-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>クイック スタート:Java を使用して Azure SQL Database または Azure SQL Managed Instance 内のデータベースに対してクエリを実行する
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

このクイックスタートでは、Java を使用して Azure SQL Database または Azure SQL Managed Instance 内のデータベースに接続し、T-SQL ステートメントを使用してデータに対してクエリを実行します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

  || SQL Database | SQL Managed Instance | Azure VM 上の SQL Server |
  |:--- |:--- |:---|:---|
  | 作成| [ポータル](single-database-create-quickstart.md) | [ポータル](../managed-instance/instance-create-quickstart.md) | [ポータル](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 構成 | [サーバーレベルの IP ファイアウォール規則](firewall-create-server-level-portal-quickstart.md)| [VM からの接続](../managed-instance/connect-vm-instance-configure.md)|
  |||[オンプレミスからの接続](../managed-instance/point-to-site-p2s-configure.md) | [SQL Server インスタンスに接続する](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |データの読み込み|クイック スタートごとに読み込まれる Adventure Works|[Wide World Importers を復元する](../managed-instance/restore-sample-database-quickstart.md) | [Wide World Importers を復元する](../managed-instance/restore-sample-database-quickstart.md) |
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) の [BACPAC](database-import.md) ファイルから Adventure Works を復元またはインポートする| [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) の [BACPAC](database-import.md) ファイルから Adventure Works を復元またはインポートする|
  |||

- [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) 関連のソフトウェア

  # <a name="macos"></a>[macOS](#tab/macos)

  「[macOS で SQL Server を使用して Java アプリを作成する](https://www.microsoft.com/sql-server/developer-get-started/java/mac/)」の手順 **1.2** と **1.3** を使用して、Homebrew と Java をインストールした後、Maven をインストールします。

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  「[Ubuntu で SQL Server を使用して Java アプリを作成する](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/)」の手順 **1.2**、**1.3**、および **1.4** を使用して、Java をインストールし、Java Development Kit をインストールした後、Maven をインストールします。

  # <a name="windows"></a>[Windows](#tab/windows)

  「[Windows で SQL Server を使用して Java アプリを作成する](https://www.microsoft.com/sql-server/developer-get-started/java/windows/)」の手順 **1.2** と **1.3** を使用して、Java をインストールした後、Maven をインストールします。

  ---

> [!IMPORTANT]
> この記事のスクリプトは、**Adventure Works** データベースを使用するように記述されています。

> [!NOTE]
> 必要に応じて、Azure SQL Managed Instance を使用することを選択できます。
>
> 作成して構成するには、[Azure portal](../managed-instance/instance-create-quickstart.md)、[PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md)、または [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) を使用してから、[オンプレミス](../managed-instance/point-to-site-p2s-configure.md)または [VM](../managed-instance/connect-vm-instance-configure.md) 接続を設定します。
>
> データを読み込む方法については、[Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ファイルの [BACPAC を使用した復元](database-import.md)に関するページを参照するか、[Wide World Importers データベースの復元](../managed-instance/restore-sample-database-quickstart.md)に関するページを参照してください。

## <a name="get-server-connection-information"></a>サーバーの接続情報を取得する

Azure SQL Database のデータベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. **[SQL データベース]** を選択するか、 **[SQL Managed Instance]** ページを開きます。

3. **[概要]** ページで、Azure SQL Database 内のデータベースの **[サーバー名]** の横にある完全修飾サーバー名、または Azure SQL Managed Instance または Azure VM 上の SQL Server の **[ホスト]** の横にある完全修飾サーバー名 (または IP アドレス) を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。

> [!NOTE]
> Azure VM 上の SQL Server の接続情報については、[SQL Server への接続](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)に関するページをご覧ください。

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

## <a name="add-code-to-query-the-database"></a>データベースに対してクエリを実行するコードを追加する

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
   > このコード例では、Azure SQL Database で **AdventureWorksLT** サンプル データベースを使用します。

## <a name="run-the-code"></a>コードの実行

1. コマンド プロンプトでアプリを実行します。

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. 先頭から 20 行が返されることを確認して、アプリ ウィンドウを閉じます。

## <a name="next-steps"></a>次のステップ

- [Azure SQL Database で最初のデータベースを設計する](design-first-database-tutorial.md)  
- [SQL Server 用 Microsoft JDBC ドライバー](https://github.com/microsoft/mssql-jdbc)  
- [問題の報告/質問](https://github.com/microsoft/mssql-jdbc/issues)  
