<properties
	pageTitle="SQL Database 用の C# の再試行ロジック | Microsoft Azure"
	description="C# のサンプルには、信頼性の高い方法で Azure SQL Database を操作する再試行ロジックが含まれています。"
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/08/2016"
	ms.author="annemill"/>


# コード サンプル: SQL Database に接続するための C# の再試行ロジック



[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]



このトピックでは、カスタムの再試行ロジックを記述した C# コードのサンプルについて説明します。この再試行ロジックは、プログラムが数秒間待機、または数回再試行すると解消される傾向がある一時的なエラーや*一時的な障害*を適切に処理することを目的としています。


ローカルの Microsoft SQL Server への接続に使用する ADO.NET クラスも、Azure SQL Database に接続できます。ただし、ADO.NET クラスだけでは、運用環境での使に必要な堅牢性と信頼性をすべて実現することはできません。クライアント プログラムは、一時的な障害に直面する場合があります。クライアント プログラムは、それ自身で、何も行っていないかのように、適切にこの障害から回復し処理を続行する必要があります。


次に一時的な障害の例を 2 つ示します。


- インターネット経由の接続では、短時間のネットワークの停止の影響を受けます。この場合、停止後に接続をもう一度作成できます。
- クラウド コンピューティングでは、負荷分散が行われます。この負荷分散の処理に伴い、接続やクエリの試行が少しの間妨げられることがあります。


## A.一時的なエラーの特定


プログラムでは、一時的なエラーと永続的なエラーを区別する必要があります。プログラムのターゲット データベース名の綴りを間違えた場合は、"このようなデータベースは見つかりません" のようなエラーが発生します。このようなエラーは時間で解消することはないため、プログラムを再実行するたびに何度も発生します。

次のトピックには、一時的な障害に分類されるエラー番号の一覧が掲載されています。

- [SQL Database クライアント プログラムのエラー メッセージ](sql-database-develop-error-messages.md#bkmk_connection_errors)
  - *一時的なエラーや接続が失われるエラー*に関する最初のセクションを参照してください。


このトピックの後半の C# コード サンプルでは、**TransientErrorNumbers** というフィールドに一時的なエラーの番号がリストされます。



## B.C# コードのサンプル


現在のトピックの C# コードのサンプルには、一時的なエラーを処理するためのカスタムの検出と再試行ロジックが含まれています。サンプルは、.NET Framework 4.5.1 以降がインストールされていることを前提としています。


コード サンプルは、Azure SQL Database の操作に使用するテクノロジに関係なく適用されるいくつかの基本的なガイドラインや推奨事項に従っています。次のトピックで、一般的な推奨事項を確認できます。


- [SQL Database への接続: リンク、ベスト プラクティスと設計のガイドライン](sql-database-connect-central-recommendations.md)


C# のコード サンプルは、Program.cs という名前の 1 つのファイルで構成されます。次のセクションでコードについて説明します。


### B.1 コード サンプルの取得とコンパイル


次の手順で、サンプルをコンパイルができます。


1. [無償の Visual Studio Community エディション](https://www.visualstudio.com/products/visual-studio-community-vs)で、C# コンソール アプリケーション テンプレートから新しいプロジェクトを作成します。
  - [ファイル]、[新規作成]、[プロジェクト]、[インストール済み]、[テンプレート]、[Visual C#]、[Windows]、[従来のデスクトップ]、[コンソール アプリケーション] の順にクリックします。
  - プロジェクトに、「**RetryAdo2**」という名前を付けます。
2. [ソリューション エクスプローラー] ペインを開きます。
  - プロジェクトの名前を確認します。
  - Program.cs ファイルの名前を確認します。
3. Program.cs ファイルを開きます。
4. Program.cs ファイルの内容全体を次のコード ブロックのコードに置き換えます。
5. メニューで [ビルド]、[ソリューションのビルド] の順にクリックします。


#### 貼り付ける C# ソース コード


このコードを、**Program.cs** ファイルに貼り付けます。


次にサーバー名、パスワードなどの文字列を編集する必要があります。これらの文字列は、**GetSqlConnectionStringBuilder** という名前のメソッドにあります。



```
using System;   // C#
using G = System.Collections.Generic;
using S = System.Data.SqlClient;
using T = System.Threading;
   
namespace RetryAdo2
{
   public class Program
   {
      static public int Main(string[] args)
      {
         bool succeeded = false;
         int totalNumberOfTimesToTry = 4;
         int retryIntervalSeconds = 10;
   
         for (int tries = 1;
            tries <= totalNumberOfTimesToTry;
            tries++)
         {
            try
            {
               if (tries > 1)
               {
                  Console.WriteLine
                     ("Transient error encountered. Will begin attempt number {0} of {1} max...",
                     tries, totalNumberOfTimesToTry
                     );
                  T.Thread.Sleep(1000 * retryIntervalSeconds);
                  retryIntervalSeconds = Convert.ToInt32
                     (retryIntervalSeconds * 1.5);
               }
               AccessDatabase();
               succeeded = true;
               break;
            }
   
            catch (S.SqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred.", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (TestSqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred. (TESTING.)", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (Exception Exc)
            {
               Console.WriteLine(Exc);
               succeeded = false;
               break;
            }
         }
   
         if (succeeded == true)
         {
            return 0;
         }
         else
         {
            Console.WriteLine("ERROR: Unable to access the database!");
            return 1;
         }
      }
   
      /// <summary>
      /// Connects to the database, reads,
      /// prints results to the console.
      /// </summary>
      static public void AccessDatabase()
      {
         //throw new TestSqlException(4060); //(7654321);  // Uncomment for testing.
   
         using (var sqlConnection = new S.SqlConnection
                  (GetSqlConnectionString()))
         {
            using (var dbCommand = sqlConnection.CreateCommand())
            {
               dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";
   
               sqlConnection.Open();
               var dataReader = dbCommand.ExecuteReader();
   
               while (dataReader.Read())
               {
                  Console.WriteLine("{0}\t{1}",
                     dataReader.GetString(0),
                     dataReader.GetString(1));
               }
            }
         }
      }
   
      /// <summary>
      /// You must edit the four 'my' string values.
      /// </summary>
      /// <returns>An ADO.NET connection string.</returns>
      static private string GetSqlConnectionString()
      {
         // Prepare the connection string to Azure SQL Database.
         var sqlConnectionSB = new S.SqlConnectionStringBuilder();
   
         // Change these values to your values.
         sqlConnectionSB.DataSource = "tcp:myazuresqldbserver.database.windows.net,1433"; //["Server"]
         sqlConnectionSB.InitialCatalog = "MyDatabase"; //["Database"]
   
         sqlConnectionSB.UserID = "MyLogin";  // "@yourservername"  as suffix sometimes.
         sqlConnectionSB.Password = "MyPassword";
         sqlConnectionSB.IntegratedSecurity = false;
   
         // Adjust these values if you like. (ADO.NET 4.5.1 or later.)
         sqlConnectionSB.ConnectRetryCount = 3;
         sqlConnectionSB.ConnectRetryInterval = 10;  // Seconds.
   
         // Leave these values as they are.
         sqlConnectionSB.IntegratedSecurity = false;
         sqlConnectionSB.Encrypt = true;
         sqlConnectionSB.ConnectTimeout = 30;
   
         return sqlConnectionSB.ToString();
      }
   
      static public G.List<int> TransientErrorNumbers =
         new G.List<int> { 4060, 40197, 40501, 40613,
         49918, 49919, 49920, 11001 };
   }
   
   /// <summary>
   /// For testing retry logic, you can have method
   /// AccessDatabase start by throwing a new
   /// TestSqlException with a Number that does
   /// or does not match a transient error number
   /// present in TransientErrorNumbers.
   /// </summary>
   internal class TestSqlException : ApplicationException
   {
      internal TestSqlException(int testErrorNumber)
      { this.Number = testErrorNumber; }
   
      internal int Number
      { get; set; }
   }
}
```



## C.プログラムの実行


**RetryAdo2.exe** 実行可能ファイルにはパラメーターを入力しません。.exe ファイルを実行するには、次のようにします。

1. RetryAdo2.exe バイナリをコンパイルした場所にコンソール ウィンドウを開きます。
2. 入力パラメーターなしで RetryAdo2.exe を実行します。



```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```



## D.再試行ロジックのテスト方法

一時的なエラーをシミュレートして再試行ロジックをテストするには、いくつかの方法があります。


### D.1 テスト例外をスローする

コード サンプルには、以下のものが含まれています。

- **TestSqlException** という名前の小さい 2 番目のクラスに、**Number** という名前のプロパティがあります。
- `//throw new TestSqlException(4060);` のコメントを解除できます。

スローのコメントを解除して再コンパイルした後に **RetryAdo2.exe** を実行すると、次のような行が出力されます。



```
[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug]
>> RetryAdo2.exe
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 2 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 3 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 4 of 4 max...
4060: transient occurred. (TESTING.)
ERROR: Unable to access the database!

[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug]
>>
```



#### 永続的なエラーで再テストする


永続的なエラーがコードで正しく処理されることを証明するために、上のテストを再実行します。このとき、4060 などの実際の一時エラーの番号は使用しないでください。代わりに、無意味な番号 7654321 を使用します。プログラムではこれを永続的なエラーとして扱い、再試行は回避されます。



### D.2 ネットワークから切断する

1. クライアント コンピューターをネットワークから切断します。
  - デスクトップの場合、ネットワーク ケーブルを外します。
  - ラップトップの場合、ファンクション キーの組み合わせを押してネットワーク アダプターをオフにします。
2. RetryAdo2.exe を起動し、最初の一時的なエラーがコンソールに表示されるのを待ちます (おそらく 11001)。
3. RetryAdo2.exe が実行している間に、ネットワークに再接続します。
4. 続いて行われる再試行が成功したことをコンソールで確認します。


### D.3 一時的にスペルミスのあるサーバー名を使用する

1. 一時的に 40615 を別のエラー番号として **TransientErrorNumbers** に追加し、再コンパイルします。
2. `new S.SqlConnectionStringBuilder()` の行にブレークポイントを設定します。
3. *エディット コンティニュ*機能を使用して、数行下で意図的にサーバー名のスペルを間違えます。
  - プログラムを実行し、ブレークポイントに戻ります。
  - 40615 エラーが発生します。
4. スペルミスを修正します。
5. プログラムを実行すると正常に終了します。
6. 40615 を削除し、再コンパイルします。


## E.関連リンク

- [SQL Database のクライアント クイック スタート コード サンプル](sql-database-develop-quick-start-client-code-samples.md)
- [SQL Database を試す: C# を使用して SQL Database Library for .NET で SQL Database を作成する](sql-database-get-started-csharp.md)

<!---HONumber=AcomDC_0413_2016-->