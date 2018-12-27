---
title: Go を使用して Azure SQL Database に照会する | Microsoft Docs
description: Go を使用して Azure SQL Database に接続し Transact-SQL ステートメントによりデータを照会、変更するプログラムを作成します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: go
ms.topic: quickstart
author: David-Engel
ms.author: v-daveng
ms.reviewer: MightyPen
manager: craigg
ms.date: 12/07/2018
ms.openlocfilehash: 34b3ee54c48040eaa6f7b7569921678869baa84b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53092368"
---
# <a name="quickstart-use-go-to-query-an-azure-sql-database"></a>クイック スタート: Go を使用して Azure SQL Database に照会する

このクイック スタートでは、Azure SQL データベースに接続して Transact-SQL ステートメントを実行しデータの照会と変更を行うプログラムを [Go](https://godoc.org/github.com/denisenkom/go-mssqldb) を使って作成する方法について説明します。 [Go](https://golang.org/) は、シンプルで信頼性と効率性の高いソフトウェアを簡単に構築できるオープン ソース プログラミング言語です。  

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- お使いのコンピューターのパブリック IP アドレスに対して構成された[サーバーレベルのファイアウォール規則](sql-database-get-started-portal-firewall.md)。

- ご使用のオペレーティング システムがインストールされた Go とそれに関連するソフトウェア:

    - **MacOS**: Homebrew と GoLang をインストールします。 [手順 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/mac/) を参照してください。
    - **Ubuntu**:GoLang をインストールします。 [手順 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/ubuntu/) を参照してください。
    - **Windows**:GoLang をインストールします。 [手順 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/windows/) を参照してください。    

## <a name="sql-server-connection-information"></a>SQL Server の接続情報

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-go-project-and-dependencies"></a>Go プロジェクトと依存関係の作成

1. ターミナルから、**SqlServerSample** という名前の新しいプロジェクト フォルダーを作成します。 

   ```bash
   mkdir SqlServerSample
   ```

2. ディレクトリを **SqlServerSample** に変更し、Go の SQL Server ドライバーをインストールします。

   ```bash
   cd SqlServerSample
   go get github.com/denisenkom/go-mssqldb
   go install github.com/denisenkom/go-mssqldb
   ```

## <a name="create-sample-data"></a>サンプル データの作成

1. 任意のテキスト エディターで、**SqlServerSample** フォルダーに **CreateTestData.sql** という名前のファイルを作成します。 ファイルに、スキーマ、テーブルを作成して数行を挿入する次の T-SQL コードをコピーして貼り付けます。

   ```sql
   CREATE SCHEMA TestSchema;
   GO

   CREATE TABLE TestSchema.Employees (
     Id       INT IDENTITY(1,1) NOT NULL PRIMARY KEY,
     Name     NVARCHAR(50),
     Location NVARCHAR(50)
   );
   GO

   INSERT INTO TestSchema.Employees (Name, Location) VALUES
     (N'Jared',  N'Australia'),
     (N'Nikita', N'India'),
     (N'Tom',    N'Germany');
   GO

   SELECT * FROM TestSchema.Employees;
   GO
   ```

2. `sqlcmd` を使用してデータベースに接続し、新しく作成した SQL スクリプトを実行します。 サーバー、データベース、ユーザー名、パスワードを適切な値に置き換えてください。

   ```bash
   sqlcmd -S your_server.database.windows.net -U your_username -P your_password -d your_database -i ./CreateTestData.sql
   ```

## <a name="insert-code-to-query-sql-database"></a>SQL Database に照会するコードの挿入

1. **sample.go**という名前のファイルを **SqlServerSample** フォルダーに作成します。

2. ファイルを開き、次のコードを貼り付けます。 適切なサーバー、データベース、ユーザー名、パスワードを追加します。 この例では、GoLang Context メソッドを使用して、データベース サーバーにアクティブな接続があることを確認します。

   ```go
   package main

   import (
       _ "github.com/denisenkom/go-mssqldb"
       "database/sql"
       "context"
       "log"
       "fmt"
       "errors"
   )

   var db *sql.DB

   var server = "your_server.database.windows.net"
   var port = 1433
   var user = "your_username"
   var password = "your_password"
   var database = "your_database"

   func main() {
       // Build connection string
       connString := fmt.Sprintf("server=%s;user id=%s;password=%s;port=%d;database=%s;",
           server, user, password, port, database)

       var err error

       // Create connection pool
       db, err = sql.Open("sqlserver", connString)
       if err != nil {
           log.Fatal("Error creating connection pool: ", err.Error())
       }
       ctx := context.Background()
       err = db.PingContext(ctx)
       if err != nil {
           log.Fatal(err.Error())
       }
       fmt.Printf("Connected!\n")

       // Create employee
       createID, err := CreateEmployee("Jake", "United States")
       if err != nil {
           log.Fatal("Error creating Employee: ", err.Error())
       }
       fmt.Printf("Inserted ID: %d successfully.\n", createID)

       // Read employees
       count, err := ReadEmployees()
       if err != nil {
           log.Fatal("Error reading Employees: ", err.Error())
       }
       fmt.Printf("Read %d row(s) successfully.\n", count)

       // Update from database
       updatedRows, err := UpdateEmployee("Jake", "Poland")
       if err != nil {
           log.Fatal("Error updating Employee: ", err.Error())
       }
       fmt.Printf("Updated %d row(s) successfully.\n", updatedRows)

       // Delete from database
       deletedRows, err := DeleteEmployee("Jake")
       if err != nil {
           log.Fatal("Error deleting Employee: ", err.Error())
       }
       fmt.Printf("Deleted %d row(s) successfully.\n", deletedRows)
   }

   // CreateEmployee inserts an employee record
   func CreateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()
       var err error

       if db == nil {
           err = errors.New("CreateEmployee: db is null")
           return -1, err
       }

       // Check if database is alive.
       err = db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := "INSERT INTO TestSchema.Employees (Name, Location) VALUES (@Name, @Location); select convert(bigint, SCOPE_IDENTITY());"

       stmt, err := db.Prepare(tsql)
       if err != nil {
          return -1, err
       }
       defer stmt.Close()

       row := stmt.QueryRowContext(
           ctx,
           sql.Named("Name", name),
           sql.Named("Location", location))
       var newID int64
       err = row.Scan(&newID)
       if err != nil {
           return -1, err
       }

       return newID, nil
   }

   // ReadEmployees reads all employee records
   func ReadEmployees() (int, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("SELECT Id, Name, Location FROM TestSchema.Employees;")

       // Execute query
       rows, err := db.QueryContext(ctx, tsql)
       if err != nil {
           return -1, err
       }

       defer rows.Close()

       var count int

       // Iterate through the result set.
       for rows.Next() {
           var name, location string
           var id int

           // Get values from row.
           err := rows.Scan(&id, &name, &location)
           if err != nil {
               return -1, err
           }

           fmt.Printf("ID: %d, Name: %s, Location: %s\n", id, name, location)
           count++
       }

       return count, nil
   }

   // UpdateEmployee updates an employee's information
   func UpdateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("UPDATE TestSchema.Employees SET Location = @Location WHERE Name = @Name")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }

   // DeleteEmployee deletes an employee from the database
   func DeleteEmployee(name string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("DELETE FROM TestSchema.Employees WHERE Name = @Name;")

       // Execute non-query with named parameters
       result, err := db.ExecContext(ctx, tsql, sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }
   ```

## <a name="run-the-code"></a>コードの実行

1. コマンド プロンプトで、次のコマンドを実行します。

   ```bash
   go run sample.go
   ```

2. 出力を検証します。

   ```text
   Connected!
   Inserted ID: 4 successfully.
   ID: 1, Name: Jared, Location: Australia
   ID: 2, Name: Nikita, Location: India
   ID: 3, Name: Tom, Location: Germany
   ID: 4, Name: Jake, Location: United States
   Read 4 row(s) successfully.
   Updated 1 row(s) successfully.
   Deleted 1 row(s) successfully.
   ```

## <a name="next-steps"></a>次の手順

- [最初の Azure SQL Database の設計](sql-database-design-first-database.md)
- [Microsoft SQL Server 用 Go ドライバー](https://github.com/denisenkom/go-mssqldb)
- [問題の報告と質問](https://github.com/denisenkom/go-mssqldb/issues)

