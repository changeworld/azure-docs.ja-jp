<properties 
	pageTitle="コード サンプル: SQL Database に接続するための C# の再試行ロジック | Microsoft Azure" 
	description="C# のサンプルには、Azure SQL Database を操作するための堅牢な再試行ロジックが含まれています。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="genemi"/>


# コード サンプル: SQL Database に接続するための C# の再試行ロジック


<!--
sql-database-develop-csharp-retry-windows.md  ,  NEW
mgm4f20150723retryfrommsdn68
. . . .
Old Titles on MSDN:  Code sample: Retry logic for connecting to Azure SQL Database with ADO.NET
.
How to: Connect to Azure SQL Database by using ADO.NET
.
ShortId on MSDN was:  ee336243.aspx
Dx  4d7936fd-341c-4a37-8796-25e385ae6c5b
-->


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


このトピックでは、カスタムの再試行ロジックを記述した C# コードのサンプルについて説明します。この再試行ロジックは、プログラムが数秒間待機、または数回再試行すると解消される傾向がある一時的なエラーや*一時的な障害*を適切に処理することを目的としています。


ローカルの Microsoft SQL Server への接続に使用する ADO.NET クラスも、Azure SQL Database に接続できます。ただし、ADO.NET クラスだけでは、運用環境での使に必要な堅牢性と信頼性をすべて実現することはできません。クライアント プログラムは、一時的な障害に直面する場合があります。クライアント プログラムは、それ自身で、何も行っていないかのように、適切にこの障害から回復する必要があります。一時的な障害の 2 つの例


- インターネット経由の接続では、短時間のネットワークの停止の影響を受けます。この場合、停止後に接続をもう一度作成できます。

- クラウド コンピューティングでは、負荷分散が行われます。この負荷分散の処理に伴い、接続やクエリの試行が少しの間妨げられることがあります。


## 一時的なエラーの特定


プログラムでは、一時的なエラーと永続的なエラーを区別する必要があります。プログラムのターゲット データベース名の綴りを間違えた場合は、"このようなデータベースは見つかりません" のようなエラーが発生します。このようなエラーは時間で解消することはないため、プログラムを再実行するたびに何度も発生します。


次のトピックには、一時的な障害に分類されるエラー番号の一覧が掲載されています。


- [SQL Database クライアント プログラムのエラー メッセージ](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - *一時的なエラー、接続が失われるエラー*に関するセクションを参照してください。


## C# コードのサンプル


現在のトピックの C# コードのサンプルには、一時的なエラーを処理するためのカスタムの検出と再試行ロジックが含まれています。


コード サンプルは、Azure SQL Database の操作に使用するテクノロジに関係なく適用されるいくつかの基本的なガイドラインや推奨事項に従っています。次のトピックで、一般的な推奨事項を確認できます。


- [SQL Database への接続: リンク、ベスト プラクティスと設計のガイドライン](sql-database-connect-central-recommendations.md)


C# コードのサンプルは、2 つの .cs ファイルで構成され、そのコンテンツは後続のセクションに貼り付けられています。ファイル名は次のとおりです。


- `Program.cs`
- `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`


## コード サンプルのコンパイルと実行


次の手順で、サンプルをコンパイルができます。


1. Visual Studio で、C# コンソール アプリケーション テンプレートから新しいプロジェクトを作成します。

2. プロジェクトを右クリックし、このトピックでソース コードを提供している .cs を追加します。

3. `cmd.exe` コマンド ウィンドウで、次に示すように、プログラムを実行します。実行からの実際の出力も表示されます。


		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>> ConsoleApplication1.exe
		database_firewall_rules_table   245575913
		filestream_tombstone_2073058421 2073058421
		filetable_updates_2105058535    2105058535
		
		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>>


.cs ファイルの C# ソース コードは、以降のセクションに記載されています。


## Program.cs ファイル


デモ プログラムは、次の目的で設計されています。


- 接続の試行中の一時的な障害により、再試行が行われます。

- クエリ コマンドの実行中に一時的なエラーが発生すると、プログラムによって接続が破棄され、新しい接続が作成された後、クエリ コマンドが再試行されます。


Microsoft はこの設計を選択するかどうかについて、推奨も、非推奨もしていません。デモ プログラムは、使用可能な設計上の柔軟性をいくつか示しています。


### ソース コードの長さ


`Program.cs` ファイルが長くなっている主な理由は、例外をキャッチするロジックがあるためです。


`Program.cs` ファイルの[短いバージョン](#shorter_program_cs)については、このトピックの末尾に掲載しました。そちらでは、再試行ロジックと `Exception` 処理をすべて削除しています。


### コール スタック


`Main` メソッドは、`Program.cs` にあります。コール スタックは、次のように実行します。


1. `Main` が `ConnectAndQuery` を呼び出します。

2. `ConnectAndQuery` が `EstablishConnection` を呼び出します。

3. `EstablishConnection` が `IssueQueryCommand` を呼び出します。


### Program.cs のソース コード


	using     System;  // C#, pure ADO.NET, no Enterprise Library.
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using T = System.Threading;
	
	namespace ConsoleApplication1
	{
	    class Program
	    {
	        // Fields, shared among methods.
	        C.SqlConnection sqlConnection;
	        C.SqlConnectionStringBuilder scsBuilder;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        /// <summary>
	        /// Prepares values for a connection. Then inside a loop, it calls a method
	        /// that opens a connection. The called method calls yet another method
	        /// that issues a query.
	        /// The loop reiterates only if a transient error is encountered.
	        /// </summary>
	        void ConnectAndQuery()
	        {
	            int connectionTimeoutSeconds = 30;  // Default of 15 seconds is too short over the Internet, sometimes.
	            int maxCountTriesConnectAndQuery = 3;  // You can adjust the various retry count values.
	            int secondsBetweenRetries = 4;  // Simple retry strategy.
	
	            // [A.1] Prepare the connection string to Azure SQL Database.
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";  // @yourservername suffix sometimes.
	            this.scsBuilder["Password"] = "MyPassword";
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = connectionTimeoutSeconds;
	
	            //-------------------------------------------------------
	            // Preparations are complete.
	
	            for (int cc = 1; cc <= maxCountTriesConnectAndQuery; cc++)
	            {
	                try
	                {
	                    // [A.2] Connect, which proceeds to issue a query command.
	                    this.EstablishConnection();
	
	                    // [A.3] All has gone well, so let the program end.
	                    break;
	                }
	                catch (C.SqlException sqlExc)
	                {
	                    bool isTransientError;
	
	                    // [A.4] Check whether sqlExc.Number is on the whitelist of transients.
	                    isTransientError = Custom_SqlDatabaseTransientErrorDetectionStrategy
	                        .IsTransientStatic(sqlExc);
	
	                    if (isTransientError == false)  // Is a persistent error...
	                    {
	                        Console.WriteLine();
	                        Console.WriteLine("Persistent error suffered, SqlException.Number=={0}.  Will terminate.",
	                            sqlExc.Number);
	                        Console.WriteLine(sqlExc.ToString());
	
	                        // [A.5] Either the connection attempt or the query command attempt suffered a persistent SqlException.
	                        // Break the loop, let the hopeless program end.
	                        break;
	                    }
	
	                    // [A.6] The SqlException identified a transient fault from an attempt to issue a query command.
	                    // So let this method reloop and try again. However, we recommend that the new query
	                    // attempt should start at the beginning and establish a new connection.
	                    Console.WriteLine();
	                    Console.WriteLine("Transient error encountered.  SqlException.Number=={0}."
	                        + "  Program might retry by itself.", sqlExc.Number);
	                    Console.WriteLine("{0} = Attempts so far. Might retry.", cc);
	                    Console.WriteLine(sqlExc.Message);
	                }
	                catch (Exception exc)
	                {
	                    Console.WriteLine();
	                    Console.WriteLine("Unexpected exception type caught in Main. Will terminate.");
	
	                    // [A.7] The program must end, so re-throw the unrecognized error.
	                    throw exc;
	                }
	
	                // [A.8] Throw an application exception if transient SqlExceptions caused us
	                // to exceed our self-imposed maximum count of retries.
	                if (cc > maxCountTriesConnectAndQuery)
	                {
	                    Console.WriteLine();
	                    string mesg = String.Format(
	                        "Transient errors suffered in too many retries ({0}). Will terminate.",
	                        cc - 1);
	                    Console.WriteLine(mesg);
	
	                    // [A.9] To end the program, throw a new exception of a different type.
	                    ApplicationException appExc = new ApplicationException(mesg);
	                    throw appExc;
	                }
	                // Else, can retry.
	
	                // A very simple retry strategy, a brief pause before looping.
	                T.Thread.Sleep(1000 * secondsBetweenRetries);
	            } // for cc
	            return;
	        } // method ConnectAndQuery
	
	
	        /// <summary>
	        /// Open a connection, then call a method that issues a query.
	        /// </summary>
	        void EstablishConnection()
	        {
	            try
	            {
	                // [B.1] The 'using' statement will .Dispose() the connection.
	                // If you are working with a connection pool, you might want instead
	                // to merely .Close() the connection.
	                using (this.sqlConnection = new C.SqlConnection(this.scsBuilder.ToString()))
	                {
	                    // [B.2] Open a connection.
	                    sqlConnection.Open();
	                    // [B.3]
	                    this.IssueQueryCommand();
	                }
	            }
	            catch (Exception exc)
	            {
	                // [B.4] This re-throw means we discard the connection whenever
	                // any error occurs during query command, even for a transient error.
	                throw exc;  // [B.5] Let caller assess any exception, SqlException or any kind.
	            }
	            return;
	        } // method EstablishConnection
	
	
	        /// <summary>
	        /// Issue a query, then write the result rows to the console.
	        /// </summary>
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(512);
	
	            try
	            {
	                // [C.1] Use the connection to create a query command.
	                using (dbCommand = this.sqlConnection.CreateCommand())
	                {
	                    dbCommand.CommandText =
	                        @"SELECT TOP 3
	                              ob.name,
	                              CAST(ob.object_id as nvarchar(32)) as [object_id]
	                          FROM sys.objects as ob
	                          WHERE ob.type='IT'
	                          ORDER BY ob.name;";
	
	                    // [C.2] Issue the query command through the connection.
	                    using (dReader = dbCommand.ExecuteReader())
	                    {
	                        // [C.3] Loop through all returned rows, writing the data to the console.
	                        while (dReader.Read())
	                        {
	                            sBuilder.Length = 0;
	                            sBuilder.Append(dReader.GetString(0));
	                            sBuilder.Append("\t");
	                            sBuilder.Append(dReader.GetString(1));
	
	                            Console.WriteLine(sBuilder.ToString());
	                        }
	                    }
	                }
	            }
	            catch (Exception exc)
	            {
	                throw exc; // Let caller assess any exception.
	            }
	            return;
	        } // method IssueQueryCommand
	    } // class Program
	}


## Custom\_SqlDatabaseTransientErrorDetectionStrategy.cs コード ファイル


[`SqlDatabaseTransientErrorDetectionStrategy`](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.sqldatabasetransienterrordetectionstrategy.aspx) は Enterprise Library (EntLib) API のクラスです。現在のコード サンプルでは、EntLib クラスの概念を取り入れた次のカスタム クラスを使用します。


	using     System;
	using G = System.Collections.Generic;
	using C = System.Data.SqlClient;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// A custom alternative to class SqlDatabaeTransientErrorDetectionStrategy.
	    /// </summary>
	    public class Custom_SqlDatabaseTransientErrorDetectionStrategy
	    {
	        static private G.List<int> M_listTransientErrorNumbers;
	
	
	        /// <summary>
	        /// This method happens to match ITransientErrorDetectionStrategy of EntLib60.
	        /// </summary>
	        public bool IsTransient(Exception exc)
	        {
	            return IsTransientStatic(exc);
	        }
	
	
	        /// <summary>
	        /// For general use beyond formal Enterprise Library classes.
	        /// </summary>
	        static public bool IsTransientStatic(Exception exc)
	        {
	            bool returnBool = false;  // Assume is a persistent error.
	            C.SqlException sqlExc;
	
	            if (exc is C.SqlException)
	            {
	                sqlExc = exc as C.SqlException;
	                if (M_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
	                {
	                    returnBool = true;  // Error is transient, not persistent.
	                }
	            }
	            return returnBool;
	        }
	
	
	        /// <summary>
	        /// Lists the SqlException.Number values that are considered
	        /// to indicate transient errors.
	        /// </summary>
	        static Custom_SqlDatabaseTransientErrorDetectionStrategy()
	        {
	            int[] arrayOfTransientErrorNumbers =
	                {4060, 10928, 10929, 40197, 40501, 40613 };
	
	            M_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
	        }
	    } // class Custom_SqlDatabaseTransientErrorDetectionStrategy
	}


<a id="shorter_program_cs" name="shorter_program_cs"></a>


&nbsp;


## Program.cs の短いバージョン


このセクションのソース コードは、前に示した長い `Program.cs` ファイルを短くしたものです。こちらでは、再試行ロジックと例外処理がすべて削除されています。


この短いバージョンでは、ADO.NET の呼び出しが通常は動作することがわかっているため、ADO.NET の呼び出しを見やすくしてあります。通常であれば一時的な障害は発生しないため、例外はスローされません。これは、普通はスカイダイビングに予備のパラシュートが必要ないのと同じです。


	using     System;  // C#, pure ADO.NET, no retry logic, no Exception handling.
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	
	namespace ConsoleApplication1_dn864744
	{
	    class Program
	    {
	        C.SqlConnection sqlConnection;
	        C.SqlConnectionStringBuilder scsBuilder;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        void ConnectAndQuery()
	        {
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";
	            this.scsBuilder["Password"] = "MyPassword";
	            this.scsBuilder["Database"] = "MyDatabase";
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30;
	
	            this.EstablishConnection();
	        } // method ConnectAndQuery
	
	        void EstablishConnection()
	        {
	            using (this.sqlConnection = new C.SqlConnection(this.scsBuilder.ToString()))
	            {
	                sqlConnection.Open();
	                this.IssueQueryCommand();
	            }
	        } // method EstablishConnection
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(512);
	
	            using (dbCommand = this.sqlConnection.CreateCommand())
	            {
	                dbCommand.CommandText =
	                    @"SELECT TOP 3 ob.name, CAST(ob.object_id as nvarchar(32)) as [object_id]
	                        FROM sys.objects as ob
	                        WHERE ob.type='IT'
	                        ORDER BY ob.name;";
	
	                using (dReader = dbCommand.ExecuteReader())
	                {
	                    while (dReader.Read())
	                    {
	                        sBuilder.Length = 0;
	                        sBuilder.Append(dReader.GetString(0));
	                        sBuilder.Append("\t");
	                        sBuilder.Append(dReader.GetString(1));
	                        Console.WriteLine(sBuilder.ToString());
	                    }
	                }
	            }
	        } // method IssueQueryCommand
	    } // class Program
	}


## 関連リンク


- [SQL Database のクライアント クイック スタート コード サンプル](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=August15_HO6-->