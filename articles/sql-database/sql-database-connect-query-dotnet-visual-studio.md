---
title: Visual Studio で .NET および C# を使用して SQL Database に対してクエリを実行する | Microsoft Docs
description: Visual Studio を使用して、Azure SQL Database に接続し、それに対して Transact-SQL ステートメントを使用してクエリを実行する C# アプリを作成します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/11/2018
ms.openlocfilehash: 04a0abd0fba7ec53aebeb481ac80d36653d118b6
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384940"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>クイック スタート:Visual Studio で .NET と C# を使用して Azure SQL データベースに接続してクエリを実行する

このクイック スタートでは、Visual Studio で [.NET Framework](https://www.microsoft.com/net/) と C# コードを使用し、Azure SQL データベースに対して Transact-SQL ステートメントを使用してクエリを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- 使用しているコンピューターのパブリック IP アドレスを許可する[サーバーレベルのファイアウォール規則](sql-database-get-started-portal-firewall.md)。
  
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) Community、Professional、または Enterprise エディション。

## <a name="get-sql-server-connection-information"></a>SQL サーバーの接続情報を取得する

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-code-to-query-the-sql-database"></a>SQL データベースに対してクエリを実行するコードを作成する

1. Visual Studio で、**[ファイル]** > **[新規]** > **[プロジェクト]** の順に選択します。 
   
1. **[新しいプロジェクト]** ダイアログで、**[Visual C#]**、**[コンソール アプリ (.NET Framework)]** の順に選択します。
   
1. プロジェクトの名前に「*sqltest*」と入力して、**[OK]** を選択します。 新しいプロジェクトが作成されます。 
   
1. **[プロジェクト]** > **[NuGet パッケージの管理]** の順に選択します。 
   
1. **[NuGet パッケージ マネージャー]** で **[参照]** タブを選択し、**System.Data.SqlClient** を検索して選択します。
   
1. **[System.Data.SqlClient]** ページで **[インストール]** を選択します。 
   - プロンプトが表示されたら、**[OK]** を選択してインストールを続行します。 
   - **[ライセンスへの同意]** ウィンドウが表示された場合は **[同意する]** を選択します。
   
1. インストールが完了すると、**[NuGet パッケージ マネージャー]** を閉じることができます。 
   
1. コード エディターで **Program.cs** の内容を次のコードに置き換えます。 `<server>`、`<username>`、`<password>`、`<database>` の値に置き換えます。
   
   >[!IMPORTANT]
   >この例のコードでは、サンプル データ AdventureWorksLT を使用します。これは、データベースの作成時にソースとして選択できます。 データベースに別のデータがある場合は、SELECT クエリで独自のデータベースからのテーブルを使用します。 
   
   ```csharp
   using System;
   using System.Data.SqlClient;
   using System.Text;
   
   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
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
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>コードの実行

1. アプリを実行するには、**[デバッグ]** > **[デバッグの開始]** の順に選択するか、ツールバーの **[開始]** を選択するか、**F5** キーを押します。
1. データベースの上位 20 のカテゴリ/製品行が返されていることを確認し、アプリ ウィンドウを閉じます。

## <a name="next-steps"></a>次の手順

- Windows/Linux/macOS 上で [.NET Core を使用して Azure SQL データベースに接続し、クエリを実行する](sql-database-connect-query-dotnet-core.md)方法について学びます。  
- [Windows/Linux/macOS の .NET Core でのコマンド ラインの使用に関する概要](/dotnet/core/tutorials/using-with-xplat-cli)を把握します。
- [SSMS で初めての Azure SQL Database を設計](sql-database-design-first-database.md)する方法や [.NET で初めての Azure SQL Database を設計](sql-database-design-first-database-csharp.md)する方法についての情報を入手します。
- .NET の詳細については、[.NET のドキュメント](https://docs.microsoft.com/dotnet/)を参照してください。
- 再試行ロジックの例:[ADO.NET を使用して SQL に弾性的に接続する][step-4-connect-resiliently-to-sql-with-ado-net-a78n]。


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

