<properties 
	pageTitle="SQL Database に接続する EntLib の再試行ロジック | Microsoft Azure"
	description="Enterprise Library はクラウド サービスのクライアント プログラムのための多くのタスクを容易にするよう設計されています。これには、一時的な障害用の再試行ロジックの統合も含まれています。"
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="genemi"/>


# コード サンプル: Enterprise Library 6 で提供される SQL Database に接続するための C&#x23; の再試行ロジック


> [AZURE.SELECTOR]
- [PHP](sql-database-develop-php-retry-windows.md)
- [C#](sql-database-develop-csharp-retry-windows.md)
- [C# EntLib6](sql-database-develop-entlib-csharp-retry-windows.md)


このトピックでは、Enterprise Library (EntLib) を使用した完全なサンプル コードについて説明します。EntLib は、Azure SQL Database などのクラウド サービスとやり取りするクライアント プログラムでの多くのタスクを容易にします。このサンプルでは、一時的な障害用の再試行ロジックを含める重要なタスクに重点を置いています。


EntLib クラスは、ランタイム エラーの次の 2 つのカテゴリを区別することを目的としています。

- 自動修正されないエラー (サーバー名のスペルミスなど)。
- 一時的な障害 (Azure システムの負荷分散時の数秒間のサーバーの一時停止、またその新しい接続の承認など)。


Enterprise Library 6 (EntLib60) は最新のバージョンで、2013 年 4 月にリリースされました。

- マイクロソフトはソース コードを公開しました。
- 今後、マイクロソフトはソース コードを維持する予定はありません。


## 前提条件


#### .NET Framework 4.0 以降


Microsoft .NET Framework 4.0 以降がインストールされている必要があります。この記事の執筆時点ではバージョン 4.6 が利用可能です。最新バージョンを使用することをお勧めします。


#### Visual Studio Community エディション (無料)


このサンプルからソース コードをコンパイルするための方法が必要です。[無料の Microsoft Visual Studio *Community* エディション](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)をインストールするという方法があります。


MSDN への電子メール アドレスの登録が必要になる場合があります。手順は次のようになります。


1. [MSDN に移動します](http://msdn.microsoft.com/)。
2. 上部近くにある **[MSDN サブスクリプション]** をクリックします。
3. **[今すぐサインアップ]** をクリックします。
4. フォームに情報を入力します。
5. 下部の **[アカウントの作成]** をクリックします。


#### Enterprise Library 6 (EntLib60)


次の方法で EntLib60 をインストールできます。


- Visual Studio で *NuGet* パッケージ マネージャー機能を使用します。
 - NuGet で、**enterpriselibrary** を検索します。


- [EntLib60 のホーム ドキュメンテーション トピック](http://msdn.microsoft.com/library/dn169621.aspx)で、**[Downloads (ダウンロード)]** という行を探し、[Microsoft Enterprise Library 6](http://go.microsoft.com/fwlink/?linkid=290898) をクリックしてバイナリの .DLL アセンブリ ファイルをダウンロードします。


EntLib60 には同じプレフィックス **Microsoft.Practices.EnterpriseLibrary.&#x2a;.dll** で名前が始まる複数の .DLL アセンブリ ファイルがありますが、このコード サンプルは次の 2 つのアセンブリのみを対象としています。

- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling**.dll
- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling.Data**.dll


## EntLib クラスの構成方法


EntLib クラスを使用して、他の EntLib クラスを構築します。このコード サンプルでの構築手順は次のとおりです。


1. **ExponentialBackoff** オブジェクトを作成します。
2. **SqlDatabaseTransientErrorDetectionStrategy** オブジェクトを作成します。
3. **RetryPolicy** オブジェクトを作成します。入力パラメーター:
 - **ExponentialBackoff** オブジェクト。
 - **SqlDatabaseTransientErrorDetectionStrategy** オブジェクト。
4. **ReliableSqlConnection** オブジェクトを作成します。入力パラメーター:
 - **String** オブジェクト - サーバー名とその他の接続情報を使用。
 - **RetryPolicy** オブジェクト
5. **RetryPolicy .ExecuteAction** メソッド経由で、呼び出して接続します。
6. **ReliableSqlConnection .CreateCommand** メソッドを呼び出します。
 - **System.SqlClient.Data.DbCommand** オブジェクト (ADO.NET の一部) が返されます。
7. **RetryPolicy .ExecuteAction** メソッド経由で、呼び出してクエリを実行します。


## コード サンプルのコンパイルと実行


Program.cs ソース コード サンプルについては、このトピックの後半で説明します。サンプルをコンパイル、実行する手順は次のとおりです。


1. Visual Studio で、C# コンソール アプリケーション テンプレートから新しいプロジェクトを作成します。

2. [ソリューション エクスプローラー] ウィンドウでほぼ空の Program.cs ファイルを編集します。そのためには、そのスターター コンテンツを、このトピックの後半で提供される Program.cs コードに置き換えます。

3. Visual Studio の [ビルド] > [ソリューションのビルド] メニューを使用して、プロジェクトをコンパイルします。

4. cmd.exe コマンド ウィンドウで、次に示すように、プログラムを実行します。実行からの実際の出力も表示されます。


```
[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>> EntLib60Retry.exe

database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535

[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>>
```


&nbsp;


## Program.cs ソース コード


この EntLib サンプルのすべてのソース コードは、次の Program.cs ファイルに含まれています。


```
using     System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;
using Y = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;

namespace EntLib60Retry
{
   class Program
   {
      static void Main(string[] args)
      {
         Program program = new Program();

         if (program.Run(args) == false)
         {
            Console.WriteLine("Something was unable to complete.  :-( ");
         }
      }

      bool Run(string[] _args)
      {
         int retryIntervalSeconds = 10;
         bool returnBool = false;

         this.InitializeEntLibObjects();

         for (int tries = 1; tries <= 3; tries++)
         {
            if (tries > 1)
            {
               H.Thread.Sleep(1000 * retryIntervalSeconds);
               retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
            }

            try
            {
               this.reliableSqlConnection = new Y.ReliableSqlConnection(
                  this.sqlConnectionSB.ToString(),
                  this.retryPolicy, null
                  );
               this.retryPolicy.ExecuteAction(this.OpenTheConnection_action);  // Open the connection.

               this.dbCommand = this.reliableSqlConnection.CreateCommand();
               this.dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

               // We retry connection .Open after transient faults, but
               // we do not retry commands that use the connection.
               this.IssueTheQuery();  // Run the query, loop through results.

               returnBool = true;
               break;
            }

            catch (C.SqlException sqlExc)
            {
               if (false == this.sqlDatabaseTransientErrorDetectionStrategy.IsTransient(sqlExc))
               {
                  throw sqlExc;
               }
            }
         }
         return returnBool;
      }

      void OpenTheConnection()  // Called by .ExecuteAction.
      {
         this.reliableSqlConnection.Open();
      }

      void IssueTheQuery()      // Called by .ExecuteAction.
      {
         X.StringBuilder sBuilder = new X.StringBuilder(256);

         this.dataReader = this.dbCommand.ExecuteReader();

         while (this.dataReader.Read())
         {
            sBuilder.Length = 0;
            sBuilder.Append(this.dataReader.GetString(0));
            sBuilder.Append("\t");
            sBuilder.Append(this.dataReader.GetString(1));

            Console.WriteLine(sBuilder.ToString());
         }
      }

      void InitializeEntLibObjects()
      {
         this.InitializeSqlConnectionStringBuilder();

         this.exponentialBackoff = new Y.ExponentialBackoff(
            "exponentialBackoff",
             3,                    // Maximum number of times to retry, then let exception bubble up.
             TimeSpan.FromMilliseconds(10000), // Minimum interval between retries.
             TimeSpan.FromMilliseconds(30000), // Maximum interval between retries.
             TimeSpan.FromMilliseconds( 2000)  // For random differences in interval between retries.
            );
         this.sqlDatabaseTransientErrorDetectionStrategy =
            new Y.SqlDatabaseTransientErrorDetectionStrategy();
         this.retryPolicy = new Y.RetryPolicy(
               this.sqlDatabaseTransientErrorDetectionStrategy,
               this.exponentialBackoff
               );
         this.OpenTheConnection_action = delegate() { this.OpenTheConnection(); };
      }

      void InitializeSqlConnectionStringBuilder()
      {
         // Prepare the connection string to Azure SQL Database.
         this.sqlConnectionSB = new C.SqlConnectionStringBuilder();

         // Change these values to your values.
         this.sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
         this.sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
         this.sqlConnectionSB["Password"] = "MyPassword";
         this.sqlConnectionSB["Database"] = "MyDatabase";

         // Leave these values as they are.
         this.sqlConnectionSB["Trusted_Connection"] = false;
         this.sqlConnectionSB["Integrated Security"] = false;
         this.sqlConnectionSB["Encrypt"] = true;
         this.sqlConnectionSB["Connection Timeout"] = 30;
      }

      Program()   // Constructor.
      {
         int[] arrayOfTransientErrorNumbers =
                { 4060, 10928, 10929, 40197, 40501, 40613, 49918, 49919, 49920
                    //,11001   // 11001 for testing, pretend network error is transient.
                    //,18456   // 18456 for testing, purposely misspell database name.
                };
         listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
      }

      // Fields.
      private G.List<int> listTransientErrorNumbers;

      private Y.ReliableSqlConnection reliableSqlConnection;
      private Y.ExponentialBackoff exponentialBackoff;
      private Y.SqlDatabaseTransientErrorDetectionStrategy
                   sqlDatabaseTransientErrorDetectionStrategy;
      private Y.RetryPolicy retryPolicy;

      private Action OpenTheConnection_action;

      private C.SqlConnectionStringBuilder sqlConnectionSB;
      private D.IDbCommand dbCommand;
      private D.IDataReader dataReader;
   }
}
```


&nbsp;


## 関連リンク


- [Enterprise Library 6 – 2013 年 4 月](http://msdn.microsoft.com/library/dn169621.aspx): 詳細情報への多数のリンクを提供します。
 - このトピック内の上部のボタンでは、[EntLib60 ソース コードをダウンロード](http://go.microsoft.com/fwlink/p/?LinkID=290898)して、ソース コードを確認できます。


- Microsoft による無料の電子ブック (.PDF 形式): [Developer's Guide to Microsoft Enterprise Library, 2nd Edition (Microsoft Enterprise Library 開発者ガイド、第 2 版)](http://www.microsoft.com/download/details.aspx?id=41145)。


- [Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling 名前空間](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.aspx)


- [Enterprise Library 6 クラス ライブラリ リファレンス](http://msdn.microsoft.com/library/dn170426.aspx)


- [コード サンプル: ADO.NET を使用して SQL Database に接続するための C# の再試行ロジック](sql-database-develop-csharp-retry-windows.md)


- [SQL Database のクライアント クイック スタート コード サンプル](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=AcomDC_1223_2015-->