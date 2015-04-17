<properties 
	pageTitle="Azure SQL Database に対する C# による CREATE ASSEMBLY"
	description="最初に DLL ファイルをエンコードして長い 16 進数を含む文字列にした後、CREATE ASSEMBLY を Azure SQL Database に対して発行する C# ソース コードを用意します。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="genemi"/>


# C# による Azure SQL Database に対する CREATE ASSEMBLY


<!--
GeneMi , Latest edit = 2015-March-25  Wednesday  10:22am
Converting plain text "CREATE ASSEMBLY" into a link to the MSDN topic, ms189524.aspx. And ms186755.aspx for "CREATE FUNCTION".
-->


このトピックでは、Azure SQL Database に対して [CREATE ASSEMBLY](http://msdn.microsoft.com/library/ms189524.aspx) ステートメントを発行するために使用できる C# コード サンプルを示します。SQL Database の FROM 句は、データベースのホストとなるローカル コンピューター上のパスの単純な形式を受け付けません。そのための代替手段は、まずアセンブリ DLL のバイナリ ビットをエンコードして、16 進数を内容とする長い文字列にすることです。その後、その文字列を FROM 句の値として指定します。


### 前提条件


このトピックを理解するには、以下に関するある程度の知識が必要です。


- [CLR テーブル値関数](http://msdn.microsoft.com/library/ms131103.aspx)<br/>CREATE ASSEMBLY Transact-SQL ステートメントが、他のステートメントと共にオンプレミスの Microsoft SQL Server にどのように動作するかを説明します。


## A. 全体的な手法


1. 必要に応じて、それ以前の実行をクリーンアップするための DROP FUNCTION および DROP ASSEMBLY。
2. 独自のコードからコンパイルした Microsoft .NET Framework アセンブリ DLL ファイルの場所をメモしておきます。次のステップでその場所を指定することになります。 
3. このトピックに示されている C# ソース コードによる EXE を実行します。EXE に対して、DLL ファイルの場所を指定します。
 - バイナリ DLL をエンコードして、16 進数を内容とする長い文字列にします。
 - その 16 進文字列を FROM 句に指定した CREATE ASSEMBLY ステートメントを発行します。
4. [CREATE FUNCTION](http://msdn.microsoft.com/library/ms186755.aspx) により、アセンブリの中のメソッドを参照します。
5. T-SQL SELECT ステートメントにより、関数を呼び出してテストします。


前のリストには、下記のものに言及されていません。<br/>
**execute sp_configure 'clr enabled', 1;**<br/>
これは Microsoft SQL Server では必要ですが、Azure SQL Database では不要だからです。


再実行で必要な場合、関数とアセンブリをドロップするための T-SQL コードは、次のようになります。


    DROP FUNCTION fnCompareStringsCaseSensitive;
    DROP ASSEMBLY CreateAssemblyFunctions3;


## B. 参照する T-SQL 関数の単純なアセンブリ DLL


このセクションに示されている単純な C# コード サンプルをコンパイルすると、アセンブリ DLL ファイルになります。


このコード サンプルには、メソッド **CompareCaseSensitiveNet** が含まれています。これは、後で T-SQL CREATE FUNCTION ステートメントの中で参照されます。このメソッドには、**SqlFunction** という名前の .NET 属性による修飾が付されていることに注意してください。この属性で修飾したメソッドは、関数として T-SQL から呼び出すことができます。


	using           System;   // C#
	using SDSqTyp = System.Data.SqlTypes;
	using MSqServ = Microsoft.SqlServer.Server;
	
	namespace CreateAssemblyFunctions3
	{
	    public class SqlFunctionMethodsForSprocs
	    {
	        /// <summary> This method is referenced
	        /// by a T-SQL CREATE FUNCTION statement. </summary>
	        [MSqServ.SqlFunction(IsDeterministic = true, IsPrecise = true)]
	        static public SDSqTyp.SqlInt32 CompareCaseSensitiveNet(string strA, string strB)
	        {
	            return String.Compare(strA, strB, false);
	        }
	    }
	}


## C. CREATE ASSEMBLY を発行する EXE 用 C&#x23; コード サンプル


この C# サンプルからビルドされる EXE を実行すると、以下の順番で実行が進みます。


1. EXE のコマンド ラインの実行により、**Main** メソッドが呼び出されます。
2. Main から **ObtainHexStringOfAssembly** メソッドが呼び出されます。
 - このメソッドは、アセンブリを 16 進数として格納した SqlString を出力します。
3. Main から **SubmitCreateAssemblyToAzureSqlDb** メソッドが呼び出されます。
 - 主な入力は SqlString です。
 - 出力は、Azure SQL Database に対して送信される CREATE ASSEMBLY 呼び出しです。


			using             System;   // C#
			using SDat      = System.Data;
			using SDSClient = System.Data.SqlClient;
			using SGlo      = System.Globalization;
			using SIo       = System.IO;
			using STex      = System.Text;
			
			namespace CreateAssemblyFromHexString6
			{
			    /// <summary>
			    /// Run the Main method on your local computer console, so it can issue a
			    /// a CREATE ASSEMBLY statement to your Azure SQL Database server:
			    /// </summary>
			    class Program
			    {
			        /// <summary>
			        /// Calls the methods in the proper sequence.
			        /// </summary>
			        /// <param name="args">
			        /// Parameters for the cmd.exe command line
			        ///    run of CreateAssemblyFromHexString6.exe:
			        /// args[0] = FullDirPathFileNameOfAssembly.
			        /// args[1] = AssemblyName.
			        ///    For the CREATE ASSEMBLY assemblyName statement.
			        /// args[2] = Azure SQL Database - ServerName, including a suffix like .database.windows.net .
			        /// args[3] = Azure SQL Database - DatabaseName.
			        /// args[4] = Azure SQL Database - LoginName.
			        /// args[5] = Azure SQL Database - Password for login.
			        ///    (Better if from .config file.)
			        /// </param>
			        static int Main(string[] args)
			        {
			            int returnCode = 1; // Only 0 (zero) means Good Success.
			            string
			                fullPathNameAssemblyFile,
			                assemblyName,
			                AzureSqlDbServerName,
			                AzureSqlDbDatabaseName,
			                AzureSqlDbLoginName,
			                AzureSqlDbPassword;
			
			            try
			            {
			                fullPathNameAssemblyFile = args[0];
			                assemblyName             = args[1];
			                AzureSqlDbServerName     = args[2];
			                AzureSqlDbDatabaseName   = args[3];
			                AzureSqlDbLoginName      = args[4];
			                AzureSqlDbPassword       = args[5];
			
			                string hexStringOfAssembly = Program
			                    .ObtainHexStringOfAssembly(fullPathNameAssemblyFile);
			
			                Program.SubmitCreateAssemblyToAzureSqlDb(
			                    hexStringOfAssembly,
			                    assemblyName,
			                    AzureSqlDbServerName,
			                    AzureSqlDbDatabaseName,
			                    AzureSqlDbLoginName,
			                    AzureSqlDbPassword);
			
			                returnCode = 0;
			            }
			            catch (Exception ex)
			            {
			                Console.WriteLine("Bad, Failure.");
			                throw ex;
			            }
			            Console.WriteLine("Good, Success.");
			            return returnCode;
			        }
			
			        /// <summary> Encodes the binary bits of the assembly DLL into a 
			        /// string containing a hexadecimal number. </summary>
			        /// <param name="fullPathToAssembly"
			        /// >Full directory path plus the file name, to the .DLL file.</param>
			        /// <returns>A string containing a hexadecimal number that encodes
			        /// the binary bits of the .DLL file.</returns>
			        static private string ObtainHexStringOfAssembly
			            (string fullPathToAssembly)
			        {
			            STex.StringBuilder sbuilder = new STex.StringBuilder("0x");
			            using (SIo.FileStream fileStream = new SIo.FileStream(
			                fullPathToAssembly,
			                SIo.FileMode.Open, SIo.FileAccess.Read, SIo.FileShare.Read)
			                )
			            {
			                int byteAsInt;
			                while (true)
			                {
			                    byteAsInt = fileStream.ReadByte();
			                    if (-1 >= byteAsInt) { break; }
			                    sbuilder.Append(byteAsInt.ToString
			                        ("X2", SGlo.CultureInfo.InvariantCulture));
			                }
			            }
			            return sbuilder.ToString();
			        }
			
			        /// <summary>
			        /// Sends a Transact-SQL CREATE ASSEMBLY FROM hexString.
			        /// </summary>
			        static private void SubmitCreateAssemblyToAzureSqlDb(
			            string hexStringOfAssembly,
			            string assemblyName,
			            string AzureSqlDbServerName,
			            string AzureSqlDbDatabaseName,
			            string AzureSqlDbLoginName,
			            string AzureSqlDbPassword)
			        {
			            string sqlCreateAssembly = "CREATE ASSEMBLY [" + assemblyName
			                + "] FROM " + hexStringOfAssembly + ";";
			            STex.StringBuilder sbuilderConnection = new STex.StringBuilder();
			
			            sbuilderConnection.Append("Server=tcp:");
			            sbuilderConnection.Append(AzureSqlDbServerName);
			            sbuilderConnection.Append(",1433;");
			
			            sbuilderConnection.Append("Database=");
			            sbuilderConnection.Append(AzureSqlDbDatabaseName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Trusted_Connection=False;");
			            sbuilderConnection.Append("Connection Timeout=30;");
			
			            sbuilderConnection.Append("User ID=");
			            sbuilderConnection.Append(AzureSqlDbLoginName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Password=");
			            sbuilderConnection.Append(AzureSqlDbPassword);
			            sbuilderConnection.Append(";");
			
			            using (SDSClient.SqlConnection sqlConnection =
			                new SDSClient.SqlConnection())
			            {
			                SDSClient.SqlCommand sqlCommand;
			                sqlConnection.ConnectionString = sbuilderConnection.ToString();
			                sqlCommand = sqlConnection.CreateCommand();
			                sqlCommand.CommandType = SDat.CommandType.Text;
			                sqlCommand.CommandText = sqlCreateAssembly;
			                sqlConnection.Open();
			                sqlCommand.ExecuteNonQuery();
			            }
			            return;
			        }
			    }
			}


### C.1 コンパイルの参照とバージョン


この EXE ツールのサンプル コードのコンパイルおよびテストでは、次のものを使用しました。


- Visual Studio 2013, update 4
 - 使用したプロジェクト テンプレート型は、単純なコンソール アプリケーションです。
- .NET Framework 4.5。


この Visual Studio プロジェクトでは、コンパイルにおいて以下のアセンブリが参照されています。


- Microsoft.CSharp
- System
- System.Core
- System.Data
- System.Data.DataSetExtensions
- System.Xml
- System.Xml.Linq


### C.2 EXE 実行のコマンド ライン


以下のコード ブロックは、コンソールからこの EXE を実行するために入力するコマンド ラインの例を示すものです。ここでは、見やすくするため、コマンド ライン パラメーターを適当に改行しています。


	CreateAssemblyFromHexString6.exe
		C:\my\bin\debug\CreateAssemblyFunctions3.dll
		CreateAssemblyFunctions3
		myazuresqldbsvr2.database.windows.net
		myazuresqldbdab4
		myazurelogin
		Mypassword123


説明を簡単にするため、この例では、パスワードをコマンド ライン パラメーターとして渡しています。実際の設計では、CONFIG ファイルからパスワードを入手するような C# コードにするのが良いでしょう。


## D. CREATE FUNCTION ステートメントの実行


Azure SQL Database サーバーにアセンブリが格納された後、そのアセンブリの中のメソッドを参照する Transact-SQL CREATE FUNCTION ステートメントを実行する必要があります。


Transact-SQL の以下のコード ブロックには、若干の不要な SELECT ステートメントが含まれています。これは、データベース システムにアセンブリおよび関数用のレコードが含まれていることを確認するためのものです。最後に、関数を呼び出す SELECT があります。


	SELECT a11.*, am2.*
		FROM           sys.assemblies       AS a11
		    INNER JOIN sys.assembly_modules AS am2 ON am2.assembly_id = a11.assembly_id
		WHERE a11.name = 'CreateAssemblyFunctions3';
	GO
	
	CREATE FUNCTION fnCompareStringsCaseSensitive
	    (@strA nvarchar(128), @strB nvarchar(128))
	    returns int
	    AS EXTERNAL NAME
	        CreateAssemblyFunctions3
	            .[CreateAssemblyFunctions3.SqlFunctionMethodsForSprocs]
	                .CompareCaseSensitiveNet;
	GO
	SELECT * FROM sys.objects WHERE name = 'fnCompareStringsCaseSensitive';
	
	 -- Use the new function.
	SELECT dbo.fnCompareStringsCaseSensitive('BLUE', 'blue') as returnedValue;
	GO


前述の Transact-SQL コード ブロックの最後に、新しい関数を呼び出す SELECT ステートメントがあります。この SELECT ステートメントは、ストアド プロシージャに入れることもできます。


<!-- EndOfFile -->


<!--HONumber=49-->