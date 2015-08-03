<properties 
	pageTitle=".NET (C#) からの SQL Database の使用" 
	description="このクイック スタートのコード サンプルを使用して、C# で最新のアプリケーションを構築し、Azure SQL Database を使用してクラウドの強力なリレーショナル データベースでバックアップします。"
	services="sql-database" 
	documentationCenter="" 
	authors="tobbox" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="tobiast"/>


# .NET (c#) からの SQL Database 使用 


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


## 必要条件

### .NET Framework

.NET framework は、Windows にプレインストールされています。Linux と Mac OS X では、.NET Frameworkを [Mono プロジェクト](http://www.mono-project.com/)でダウンロードすることができます。

### SQL Database

「[作業の開始](sql-database-get-started.md)」ページで、サンプル データベースを作成し、接続文字列を取得する方法についてご確認ください。

## SQL Database に接続する

[System.Data.SqlClient.SqlConnection クラス](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) は SQL Databaseへの接続に使用します。
	
```
using System.Data.SqlClient;

class Sample
{
  static void Main()
  {
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
	  {
		  conn.Open();	
	  }
  }
}	
```

## クエリを実行し、結果セットを取得します 

[System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) と [SqlDataReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqldatareader.aspx) クラスは、SQL Database へのクエリの結果セットを取得するために使用できます。System.Data.SqlClient はオフラインの [System.Data.DataSet](https://msdn.microsoft.com/library/system.data.dataset.aspx) のデータの取得もサポートしています。
	
```
using System;
using System.Data.SqlClient;

class Sample
{
	static void Main()
	{
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
		{
			var cmd = conn.CreateCommand();
			cmd.CommandText = @"
					SELECT 
						c.CustomerID
						,c.CompanyName
						,COUNT(soh.SalesOrderID) AS OrderCount
					FROM SalesLT.Customer AS c
					LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID
					GROUP BY c.CustomerID, c.CompanyName
					ORDER BY OrderCount DESC;";

			conn.Open();	
		
			using(var reader = cmd.ExecuteReader())
			{
				while(reader.Read())
				{
					Console.WriteLine("ID: {0} Name: {1} Order Count: {2}", reader.GetInt32(0), reader.GetString(1), reader.GetInt32(2));
				}
			}					
		}
	}
}

```

## 行を挿入する、パラメーターを渡す、生成されたプライマリ キー値を取得する 

SQL Database の [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) プロパティと [SEQUENECE](https://msdn.microsoft.com/library/ff878058.aspx) オブジェクトは、[プライマリ キー](https://msdn.microsoft.com/library/ms179610.aspx)値の自動生成に使用できます。この例では、[INSERT ステートメント](https://msdn.microsoft.com/library/ms174335.aspx)を実行し、[SQL インジェクション](https://msdn.microsoft.com/magazine/cc163917.aspx)から保護されているパラメーターを安全に渡し、自動生成されたプライマリ キー値を取得する方法について説明しています。

[System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) クラスの[ExecuteScalar](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executescalar.aspx) メソッドは、ステートメントを実行し、このステートメントによって返される最初の列と行を取得するために使用できます。INSERT ステートメントの[OUTPUT](https://msdn.microsoft.com/library/ms177564.aspx)句は、呼び出し元のアプリケーションに設定された結果セットとして挿入された値を返すために使用できます。OUTPUT は、[UPDATE](https://msdn.microsoft.com/library/ms177523.aspx)、[DELETE](https://msdn.microsoft.com/library/ms189835.aspx)、[MERGE](https://msdn.microsoft.com/library/bb510625.aspx) ステートメントにもサポートされています。1 行より多く挿入されている場合は、[ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) メソッドを使用して挿入されたすべての行の値を取得します。
	
```
class Sample
{
    static void Main()
    {
		using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
        {
            var cmd = conn.CreateCommand();
            cmd.CommandText = @"
                INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) 
                OUTPUT INSERTED.ProductID
                VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP)";

            cmd.Parameters.AddWithValue("@Name", "SQL Server Express");
            cmd.Parameters.AddWithValue("@Number", "SQLEXPRESS1");
            cmd.Parameters.AddWithValue("@Cost", 0);
            cmd.Parameters.AddWithValue("@Price", 0);

            conn.Open();

            int insertedProductId = (int)cmd.ExecuteScalar();

            Console.WriteLine("Product ID {0} inserted.", insertedProductId);
        }
    }
}
```

 

<!---HONumber=July15_HO4-->