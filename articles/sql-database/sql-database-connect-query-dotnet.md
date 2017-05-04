---
title: ".NET (C#) を使用した Azure SQL Database への接続 | Microsoft Docs"
description: "Azure SQL Database への接続とデータの照会に使用できる .NET コード サンプルについて説明します。"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 7faca033-24b4-4f64-9301-b4de41e73dfd
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: andrela;sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 119ffa3ac31e0ea6e76f8232f13b4dd8667f78aa
ms.lasthandoff: 04/21/2017


---
# <a name="azure-sql-database-use-net-c-to-connect-and-query-data"></a>Azure SQL Database: .NET (C#) を使って接続とデータの照会を行う

このクイック スタートでは、Windows、Mac OS、Ubuntu Linux の各プラットフォームから [C# と ADO.NET](https://msdn.microsoft.com/library/kb9s9ks0.aspx) を使って Azure SQL データベースに接続し、Transact-SQL ステートメントを使ってデータベース内のデータを照会、挿入、更新、削除する方法について説明します。

このクイック スタートでは、次のクイック スタートで作成されたリソースが出発点として使用されます。

- [DB の作成 - ポータル](sql-database-get-started-portal.md)
- [DB の作成 - CLI](sql-database-get-started-cli.md)

## <a name="install-net"></a>.NET のインストール

このセクションの手順では、.NET による開発には慣れているが、Azure SQL Database を初めて使用するユーザーを想定しています。 .NET による開発の経験がない場合は、「[Build an app using SQL Server (SQL Server を使用してアプリを構築する)](https://www.microsoft.com/en-us/sql-server/developer-get-started/)」に移動し、**C#** を選択してから、使用しているオペレーティング システムを選択します。

### <a name="windows-net-framework-and-net-core"></a>**Windows .NET Framework と .NET Core**

Visual Studio 2017 Community は、さまざまな機能が用意された、拡張可能な無料の IDE です。Web およびデータベース アプリケーションやクラウド サービスだけでなく、Android、iOS、Windows 向けの最新のアプリケーションの作成に使用できます。 完全な .NET Framework をインストールすることも、.NET Core だけをインストールすることもできます。 どちらの場合も、このクイック スタートのコード スニペットは正しく動作します。 既にコンピューターに Visual Studio がインストールされている場合、次のいくつかの手順はスキップします。

1. [インストーラー](https://go.microsoft.com/fwlink/?LinkId=691978)をダウンロードします。 
2. インストーラーを実行し、表示されるインストールの指示に従って、インストールを完了します。

### <a name="mac-os"></a>**Mac OS**
ターミナルを開き、.NET Core プロジェクトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**brew**、**OpenSSL**、**.NET Core** をインストールします。 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

macOS では、.NET Core をインストールします。 [公式インストーラー](https://go.microsoft.com/fwlink/?linkid=843444)をダウンロードします。 このインストーラーは、ツールをインストールしてパスに配置します。これにより、コンソールから .NET を実行できるようになります。

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
ターミナルを開き、.NET Core プロジェクトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**.NET Core** をインストールします。

```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-dev-1.0.1
```

## <a name="get-connection-information"></a>接続情報の取得

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名、データベース名、ログイン情報が必要になります。

1. [Azure Portal](https://portal.azure.com/) にログインします。
2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 
3. データベースの **[概要]** ページで、次の図に示すように、完全修飾サーバー名を確認します。 サーバー名をポイントすると、**[コピーするにはクリックします]** オプションが表示されます。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Azure SQL Database サーバーのログイン情報を忘れた場合は、[SQL データベース サーバー] ページに移動して、サーバー管理者名を表示し、必要に応じてパスワードをリセットします。

5. **[データベース接続文字列の表示]** をクリックします。

6. 完全な **ADO.NET** 接続文字列を確認します。

    ![ADO.NET の接続文字列](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)
  
## <a name="add-systemdatasqlclient"></a>System.Data.SqlClient の追加
.NET Core を使用する際は、プロジェクトの ***csproj*** ファイルに System.Data.SqlClient を依存関係として追加します。

```xml
<ItemGroup>
    <PackageReference Include="System.Data.SqlClient" Version="4.3.0" />
</ItemGroup>
```

## <a name="select-data"></a>データの選択

1. 開発環境で、空のコード ファイルを開きます。
2. ```using System.Data.SqlClient``` をコード ファイルに追加します ([System.Data.SqlClient 名前空間](https://msdn.microsoft.com/library/system.data.sqlclient.aspx))。 

3. 次のコードを使用して、カテゴリ別で上位 20 の製品を照会します。ここでは、[SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL ステートメントと共に [SqlCommand.ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) コマンドを使用します。 サーバー、データベース、ユーザー、パスワードの適切な値を入力してください。

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                    sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                    sb.Append("JOIN [SalesLT].[Product] p ");
                    sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                    String sql = sb.ToString();

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="insert-data"></a>データを挿入する

次のコードを使用して、新しい製品を SalesLT.Product テーブルに挿入します。ここでは、[INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL ステートメントと共に [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) を使用します。 サーバー、データベース、ユーザー、パスワードの適切な値を入力してください。

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nInsert data example:");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("INSERT INTO [SalesLT].[Product] ([Name], [ProductNumber], [Color], [StandardCost], [ListPrice], [SellStartDate]) ");
                    sb.Append("VALUES (@Name, @ProductNumber, @Color, @StandardCost, @ListPrice, @SellStartDate);");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        command.Parameters.AddWithValue("@ProductNumber", "200989");
                        command.Parameters.AddWithValue("@Color", "Blue");
                        command.Parameters.AddWithValue("@StandardCost", 75);
                        command.Parameters.AddWithValue("@ListPrice", 80);
                        command.Parameters.AddWithValue("@SellStartDate", "7/1/2016");
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) inserted");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="update-data"></a>データの更新

次のコードを使用して、先ほど追加した新しい製品を更新します。ここでは、[UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL ステートメントと共に [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) を使用します。 サーバー、データベース、ユーザー、パスワードの適切な値を入力してください。

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nUpdate data example");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("UPDATE [SalesLT].[Product] SET ListPrice = @ListPrice WHERE Name = @Name;");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        command.Parameters.AddWithValue("@ListPrice", 500);
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) updated");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="delete-data"></a>データの削除

次のコードを使用して、先ほど追加した新しい製品を削除します。ここでは、[DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL ステートメントと共に [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) を使用します。 サーバー、データベース、ユーザー、パスワードの適切な値を入力してください。

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nDelete data example");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("DELETE FROM SalesLT.Product WHERE Name = @Name;");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) deleted");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="next-steps"></a>次のステップ

- .NET のドキュメントについては、「[.NET ドキュメント](https://docs.microsoft.com/dotnet/)」を参照してください。
- SQL Server Management Studio を使用して接続とクエリを実行するには、[SSMS を使用した接続とクエリ](sql-database-connect-query-ssms.md)に関するページを参照してください。
- Visual Studio を使用して接続とデータの照会を行うには、[Visual Studio Code を使った接続とデータの照会](sql-database-connect-query-vscode.md)に関するページを参照してください。
- PHP を使用して接続とデータの照会を行うには、[PHP を使った接続とデータの照会](sql-database-connect-query-php.md)に関するページを参照してください。
- Node.js を使用して接続とデータの照会を行うには、[Node.js を使った接続とデータの照会](sql-database-connect-query-nodejs.md)に関するページを参照してください。
- Java を使用して接続とデータの照会を行うには、[Java を使った接続とデータの照会](sql-database-connect-query-java.md)に関するページを参照してください。
- Python を使用して接続とデータの照会を行うには、[Python を使った接続とデータの照会](sql-database-connect-query-python.md)に関するページを参照してください。
- Ruby を使用して接続とデータの照会を行うには、[Ruby を使った接続とデータの照会](sql-database-connect-query-ruby.md)に関するページを参照してください。

