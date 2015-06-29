<properties
	pageTitle="C# を使用して SQL Database に接続して照会します"
	description="ADO.NET を使用して、Azure SQL Database のクラウド サービスの AdventureWorks Database に接続して対話する C# クライアントのサンプル コード。"
	services="sql-database"
	documentationCenter=""
	authors="ckarst"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="04/14/2015"
	ms.author="cakarst"/>


# C&#x23; を使用して SQL Database に接続し、照会する


このトピックでは、ADO.NET を使用して、既存の AdventureWorks SQL Database に接続する方法を説明する C# サンプル コードについて説明します。このサンプルは、データベースを照会し、結果セットを表示するコンソール アプリケーションにコンパイルします。


## 前提条件


- Azure SQL Database 上の既存の AdventureWorks データベース。[数分に 1 つずつ作成します](sql-database-get-started.md)。
- [.NET Framework を使用する Visual Studio](https://www.visualstudio.com/ja-jp/visual-studio-homepage-vs.aspx)


## 手順 1: コンソール アプリケーション


1. Visual Studio を使用して、C# コンソール アプリケーションを作成します。


![接続と照会](./media/sql-database-connect-query/ConnectandQuery_VisualStudio.png)


## 手順 2: SQL サンプル コード


1. 以下のサンプル コードをコピーして、コンソール アプリケーションに貼り付けます。


> [AZURE.WARNING]サンプル コードは、理解しやすいようにできるだけ短く設計されています。サンプルは実稼働環境で使用するためのものではありません。


このコードは実稼働用ではありません。実稼働準備コードを実装する場合は、次のものが業界のベスト プラクティスと考えられます。


- 例外処理。
- 一時的なエラーのためのロジックを再試行してください。
- 構成ファイル内のパスワードの安全な格納。



### C# サンプルのソース コード


このソース コードを、**Program.cs** ファイルに貼り付けます。


	using System;  // C#
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Data.SqlClient;

	namespace ConnectandQuery_Example
	{
		class Program
		{
			static void Main()
			{
				string SQLConnectionString = "[Your_Connection_String]";
				// Create a SqlConnection from the provided connection string.
				using (SqlConnection connection = new SqlConnection(SQLConnectionString))
				{
					// Begin to formulate the command.
					SqlCommand command = new SqlCommand();
					command.Connection = connection;

					// Specify the query to be executed.
					command.CommandType = System.Data.CommandType.Text;
						command.CommandText =
						@"SELECT TOP 10
						CustomerID, NameStyle, Title, FirstName, LastName
						FROM SalesLT.Customer";

					// Open connection to database.
					connection.Open();

					// Read data from the query.
					SqlDataReader reader = command.ExecuteReader();
					while (reader.Read())
					{
						// Formatting will depend on the contents of the query.
						Console.WriteLine("Value: {0}, {1}, {2}, {3}, {4}",
							reader[0], reader[1], reader[2], reader[3], reader[4]);
					}
				}
				Console.WriteLine("Press any key to continue...");
				Console.ReadKey();
			}
		}
	}


## 手順 3: データベースの接続文字列を検索します。


1. [Azure プレビュー ポータル](http://portal.azure.com/)を開きます。
2. **[参照]** > **[SQL Database]** > **[“Adventure Works” Database]**> **[プロパティ]** > **[データベース接続文字列を表示する]** をクリックします。


![ポータル](./media/sql-database-connect-query/ConnectandQuery_portal.png)


データベース接続文字列のブレードに、ADO.NET、ODBC、PHP、JDBC 向けの適切な接続文字列が表示されます。


## 手順 4: 実際の接続情報に置き換えます


- 貼り付けたソース コードで、*[Your_Connection_String]* プレースホルダーを接続文字列に置き換え、その文字列の *your_password_here* を実際のパスワードに置き換えます。


## 手順 5: アプリケーションを実行します


1. アプリケーションを構築して、実行するには、**[デバッグ]** > **[デバッグの開始]** をクリックします
2. プログラムは、コンソール ウィンドウにクエリ結果を出力します。
 

<!---HONumber=58_postMigration-->