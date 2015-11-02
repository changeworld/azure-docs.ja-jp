<properties 
	pageTitle="SQL Database に接続する C# の再試行ロジック | Microsoft Azure" 
	description="C# のサンプルには、信頼性の高い方法で Azure SQL Database を操作する再試行ロジックが含まれています。" 
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
	ms.date="10/15/2015" 
	ms.author="genemi"/>


# コード サンプル: SQL Database に接続するための C# の再試行ロジック


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


このトピックでは、カスタムの再試行ロジックを記述した C# コードのサンプルについて説明します。この再試行ロジックは、プログラムが数秒間待機、または数回再試行すると解消される傾向がある一時的なエラーや*一時的な障害*を適切に処理することを目的としています。


ローカルの Microsoft SQL Server への接続に使用する ADO.NET クラスも、Azure SQL Database に接続できます。ただし、ADO.NET クラスだけでは、運用環境での使に必要な堅牢性と信頼性をすべて実現することはできません。クライアント プログラムは、一時的な障害に直面する場合があります。クライアント プログラムは、それ自身で、何も行っていないかのように、適切にこの障害から回復する必要があります。


次に一時的な障害の例を 2 つ示します。


- インターネット経由の接続では、短時間のネットワークの停止の影響を受けます。この場合、停止後に接続をもう一度作成できます。

- クラウド コンピューティングでは、負荷分散が行われます。この負荷分散の処理に伴い、接続やクエリの試行が少しの間妨げられることがあります。


## 一時的なエラーの特定


プログラムでは、一時的なエラーと永続的なエラーを区別する必要があります。プログラムのターゲット データベース名の綴りを間違えた場合は、"このようなデータベースは見つかりません" のようなエラーが発生します。このようなエラーは時間で解消することはないため、プログラムを再実行するたびに何度も発生します。


次のトピックには、一時的な障害に分類されるエラー番号の一覧が掲載されています。


- [SQL Database クライアント プログラムのエラー メッセージ](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - *一時的なエラーや接続が失われるエラー*に関する最初のセクションを参照してください。


## C# コードのサンプル


現在のトピックの C# コードのサンプルには、一時的なエラーを処理するためのカスタムの検出と再試行ロジックが含まれています。


コード サンプルは、Azure SQL Database の操作に使用するテクノロジに関係なく適用されるいくつかの基本的なガイドラインや推奨事項に従っています。次のトピックで、一般的な推奨事項を確認できます。


- [SQL Database への接続: リンク、ベスト プラクティスと設計のガイドライン](sql-database-connect-central-recommendations.md)


C# のコード サンプルは、Program.cs という名前の 1 つのファイルで構成されます。次のセクションでは、そのコードを貼り付けます。


### コード サンプルの取得とコンパイル


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
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;

namespace RetryAdo2
{
	class Program
	{
		static void Main(string[] args)
		{
			Program program = new Program();
			bool returnBool;

			returnBool = program.Run(args);
			if (returnBool == false)
			{
				Console.WriteLine("Something failed.  :-( ");
			}
			return;
		}

		bool Run(string[] _args)
		{
			C.SqlConnectionStringBuilder sqlConnectionSB;
			C.SqlConnection sqlConnection;
			D.IDbCommand dbCommand;
			D.IDataReader dataReader;
			X.StringBuilder sBuilder = new X.StringBuilder(256);
			int retryIntervalSeconds = 10;
			bool returnBool = false;

			Program program = new Program();

			for (int tries = 1; tries <= 5; tries++)
			{
				try
				{
					if (tries > 1)
					{
						H.Thread.Sleep(1000 * retryIntervalSeconds);
						retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
					}

					program.GetSqlConnectionStringBuilder(out sqlConnectionSB);

					sqlConnection = new C.SqlConnection(sqlConnectionSB.ToString());

					dbCommand = sqlConnection.CreateCommand();
					dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

					sqlConnection.Open();
					dataReader = dbCommand.ExecuteReader();

					while (dataReader.Read())
					{
						sBuilder.Length = 0;
						sBuilder.Append(dataReader.GetString(0));
						sBuilder.Append("\t");
						sBuilder.Append(dataReader.GetString(1));

						Console.WriteLine(sBuilder.ToString());
					}
					returnBool = true;
					break;
				}

				catch (C.SqlException sqlExc)
				{
					if (this.m_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
					{ continue; }
					else
					{ throw sqlExc; }
				}
			}
			return returnBool;
		}

		void GetSqlConnectionStringBuilder(out C.SqlConnectionStringBuilder _sqlConnectionSB)
		{
			// Prepare the connection string to Azure SQL Database.
			_sqlConnectionSB = new C.SqlConnectionStringBuilder();

			// Change these values to your values.
			_sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
			_sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
			_sqlConnectionSB["Password"] = "MyPassword";
			_sqlConnectionSB["Database"] = "MyDatabase";

			// Leave these values as they are.
			_sqlConnectionSB["Trusted_Connection"] = false;
			_sqlConnectionSB["Integrated Security"] = false;
			_sqlConnectionSB["Encrypt"] = true;
			_sqlConnectionSB["Connection Timeout"] = 30;
		}

		Program()   // Constructor.
		{
			int[] arrayOfTransientErrorNumbers =
				{ 4060, 40197, 40501, 40613, 49918, 49919, 49920
					//,11001   // 11001 for testing, pretend network error is transient.
				};
			m_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
		}

		private G.List<int> m_listTransientErrorNumbers;
	}
}
```


### プログラムの実行


**RetryAdo2.exe** 実行可能ファイルにはパラメーターを入力しません。次のように Visual Studio で .exe を実行します。


1. **Main** メソッドの **return;** ステートメントにブレークポイントを設定します。

2. 開始用の緑の矢印ボタンをクリックします。コンソール ウィンドウが表示されます。

3. **Main** の終わりでデバッガーが停止したら、コンソール ウィンドウに切り替えます。

4. 次と同様な 3 つの行を確認します。


```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```


## 再試行ロジックのテスト


次に示すのは、再試行ロジックをテストする便利な方法の 1 つです。


1. 一時的なエラーとしてと見なされる **SqlConnection.Number** 値のコレクションに、一時的に **11001** を追加します。

2. プログラムのコンパイル

3. クライアント コンピューターをネットワークから切断します。

4. ループ内にブレークポイントを設定して、デバッガーでプログラムを実行します。
 - 最初のループは、エラー 11001 で失敗します。

5. 2 つ目のループで、プログラムがブレークポイントで停止しているときに、ネットワークにコンピューターを再接続します。

6. プログラムの実行を再開します。プログラムは、2 つ目のループで正常に実行されます。


### 別のテスト方法


別の方法では、"test" のコマンドライン パラメーターの値を認識するよう、プログラムにロジックを追加できます。パラメーターに対応して、プログラムは次のように動作します。


1. SQL Database のサーバー名のスペルを間違えるよう、意味のない文字を一時的に追加します。

2. 一時的なエラーの一覧に **40615** を一時的に追加します。

3. 最初の再試行のループとなる、2 つ目のループの開始時に、プログラムは以下のように動作します。
 - サーバーのスペルミスを元に戻します。
 - 一時的なエラーの一覧から 40615 を削除します。


"Test" パラメーターを指定してプログラムを実行することにより、プログラムが最初のループで失敗し、2 つ目のループで正常に実行されることを確認します。


## 関連リンク


- [SQL Database のクライアント クイック スタート コード サンプル](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=Oct15_HO4-->