<properties
	pageTitle="C# クエリによる SQL Database への接続 | Microsoft Azure"
	description="SQL データベースへのクエリおよび接続を実行するプログラムを C# で作成します。IP アドレス、接続文字列、セキュリティで保護されたログイン、および無料版の Visual Studio に関する情報。"
	services="sql-database"
	keywords="C# データベース クエリ, C# クエリ, データベースへの接続, SQL C#"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="02/05/2016"
	ms.author="genemi"/>


# SQL Database へのクエリおよび接続を実行するプログラムを C&#x23; で作成する

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

クラウド内の Azure SQL データベースへのクエリおよび接続を実行するプログラムを C# で作成する方法を説明します。

この記事では、Azure SQL Database、C#、および ADO.NET を初めて使用するユーザー向けに、すべての手順を説明します。Microsoft SQL Server と C# の使用経験が豊富なユーザーなら、一部の手順をスキップして SQL Database に固有の手順のみに集中することができます。


## 前提条件


C# クエリ コード サンプルを実行するには、以下のものが必要です。


- Azure アカウントとサブスクリプション。[無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。


- Azure SQL Database サービス上の **AdventureWorksLT** デモンストレーション データベース。
 - [デモ データベースの作成](sql-database-get-started.md)は数分で完了します。


- Visual Studio 2013 Update 4 以降。現在 Microsoft では、Visual Studio Community を*無料*で提供しています。
 - [Visual Studio Community のダウンロード](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [その他の無料版 Visual Studio の選択肢](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - または、このトピックで後述する[手順](#InstallVSForFree)に従い、[Azure ポータル](https://portal.azure.com/)のガイドを利用して Visual Studio をインストールすることができます。


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## 手順 1. 無料版の Visual Studio Community をインストールする


Visual Studio のインストールが必要な場合、以下の方法でインストールできます。

- 無料ダウンロードやその他の選択肢が提供されている Visual Studio 製品ページにブラウザーでアクセスして、無料版の Visual Studio Community をインストールする。または
- 次の説明に従い、[Azure ポータル](https://portal.azure.com/)のガイドを利用してダウンロード ページにアクセスする。


### Azure ポータルから Visual Studio へ


1. [Azure ポータル](https://portal.azure.com/) (http://portal.azure.com/) からログインします。

2. **[すべて参照]**、**[SQL データベース]** の順にクリックします。データベースを検索するためのブレードが開きます。

3. ブレード上部付近にあるフィルターのテキスト ボックスに **AdventureWorksLT** データベースの名前を入力します。

4. お使いのサーバー上のデータベースに関する行が表示されたら、その行をクリックします。データベースに関するブレードが開きます。

5. 操作がしやすいように、1 つ前までの各ブレードで最小化のコントロールをクリックします。

6. データベース ブレードの上部付近にある **[Visual Studio で開く]** をクリックします。Visual Studio のインストール場所へのリンクを備えた、Visual Studio に関する新しいブレードが開きます。

	![[Visual Studio で開く] ボタン][20-OpenInVisualStudioButton]

7. **[Community (無料)]** リンク、またはそれに類似したリンクをクリックします。新しい Web ページが追加されます。

8. 新しい Web ページ上のリンクを使用して Visual Studio をインストールします。

9. Visual Studio のインストール後に、**[Visual Studio で開く]** ブレードで **[Visual Studio で開く]** をクリックします。Visual Studio が開きます。

10. Visual Studio では、**[SQL Server オブジェクト エクスプローラー]** ペインで使用するために、ダイアログの接続文字列フィールドに接続文字列を入力するよう求められます。
 - **[Windows 認証]** ではなく **[SQL Server 認証]** を選択します。
 - **AdventureWorksLT** データベースの指定も忘れずに行ってください (ダイアログ ボックスで **[オプション]**、**[接続プロパティ]** の順にクリック)。

11. **SQL Server オブジェクト エクスプローラー**で、データベースのノードを展開します。


## 手順 2. Visual Studio で新しいプロジェクトを作成する


Visual Studio で、スタート テンプレート ([C#]、[Windows]、**[コンソール アプリケーション]** の順に選択) を基にした新しいプロジェクトを作成します。


1. **[ファイル]**、**[新規作成]**、**[プロジェクト]** の順にクリックします。**** ダイアログが表示されます。

2. **[インストール済み]** で、[C#]、[Windows] と展開すると、中央のペインに **[コンソール アプリケーション]** が表示されます。

	![[新しいプロジェクト] ダイアログ][30-VSNewProject]

2. **[名前]** に「**ConnectAndQuery\_Example**」と入力します。**[OK]** をクリックします。


## 手順 3. 構成処理のアセンブリ参照を追加する


C# のサンプルでは .NET Framework アセンブリの **System.Configuration.dll** を使用しているため、これに参照を追加しましょう。


1. **[ソリューション エクスプローラー]** ペインで **[参照設定]** を右クリックし、**[参照の追加]** をクリックします。**[参照マネージャー]** ウィンドウが表示されます。

2. **[アセンブリ]**、**[フレームワーク]** の順に展開します。

3. **System.Configuration** までスクロールしてからそれをクリックして強調表示します。チェック ボックスがオンになっていることを確認します。

4. **[OK]** をクリックします。

5. メニューから **[ビルド]**、**[ソリューションのビルド]** の順にクリックしてプログラムをコンパイルします。


## 手順 4. 接続文字列を取得する


[Azure ポータル](https://portal.azure.com/)を使用して、SQL データベースへの接続で必要な接続文字列をコピーします。

最初は、Visual Studio を Azure SQL Database の **AdventureWorksLT** データベースに接続するために使用します。


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


## 手順 5. 接続文字列を App.config ファイルに追加する


1. Visual Studio で、[ソリューション エクスプローラー] ペインから App.config ファイルを開きます。

2. 次の App.config コード サンプルに示すように、**&#x3c;configuration&#x3e; &#x3c;/configuration&#x3e;** 要素を追加します。
 - *{your\_placeholders}* を実際の値に置き換えます。

```
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>

		<connectionStrings>
			<clear />
			<add name="ConnectionString4NoUserIDNoPassword"
			connectionString="Server=tcp:{your_serverName_here}.database.windows.net,1433; Database={your_databaseName_here}; Connection Timeout=30; Encrypt=True; TrustServerCertificate=False;"
			/>
		</connectionStrings>
	</configuration>
```

3. App.config への変更を保存します。

4. [ソリューション エクスプローラー] ペインで **[App.config]** ノードを右クリックし、**[プロパティ]** をクリックします。

5. **[出力ディレクトリにコピー]** を **[常にコピーする]** に設定します。
 - これにより、&#x2a;.exe ファイルのビルド先ディレクトリにある &#x2a;.exe.config ファイルの内容が、App.config ファイルの内容に置き換わります。この置き換えは、&#x2a;.exe を再コンパイルするたびに発生します。
 - &#x2a;.exe.config ファイルは、C# サンプル プログラムの実行時に読み取られます。

	![[出力ディレクトリにコピー] = [常にコピーする]][50-VSCopyToOutputDirectoryProperty]


## 手順 6. C# サンプル コード内に貼り付ける


1. Visual Studio で、**[ソリューション エクスプローラー]** ペインから **Program.cs** ファイルを開きます。

	![C# サンプル コード内に貼り付けます。][40-VSProgramCsOverlay]

2. 次の C# サンプル コードを貼り付けて、Program.cs 内のすべてのスタート コードを上書きします。
 - コード サンプルを短くする場合は、接続文字列全体をリテラルとして変数 **SQLConnectionString** に割り当てることができます。その後は、**GetConnectionStringFromExeConfig** と **GatherPasswordFromConsole** の 2 つのメソッドを削除できます。


```
using System;  // C#
using G = System.Configuration;   // System.Configuration.dll
using D = System.Data;            // System.Data.dll
using C = System.Data.SqlClient;  // System.Data.dll
using T = System.Text;

namespace ConnectAndQuery_Example
{
	class Program
	{
		static void Main()
		{
			string connectionString4NoUserIDNoPassword,
				password, userName, SQLConnectionString;

			// Get most of the connection string from ConnectAndQuery_Example.exe.config
			// file, in the same directory where ConnectAndQuery_Example.exe resides.
			connectionString4NoUserIDNoPassword = Program.GetConnectionStringFromExeConfig
				("ConnectionString4NoUserIDNoPassword");
			// Get the user name from keyboard input.
			Console.WriteLine("Enter your User ID, without the trailing @ and server name: ");
			userName = Console.ReadLine();
			// Get the password from keyboard input.
			password = Program.GatherPasswordFromConsole();

			SQLConnectionString = "Password=" + password + ';' +
				"User ID=" + userName + ";" + connectionString4NoUserIDNoPassword;

			// Create an SqlConnection from the provided connection string.
			using (C.SqlConnection connection = new C.SqlConnection(SQLConnectionString))
			{
				// Formulate the command.
				C.SqlCommand command = new C.SqlCommand();
				command.Connection = connection;

				// Specify the query to be executed.
				command.CommandType = D.CommandType.Text;
				command.CommandText = @"
					SELECT TOP 9 CustomerID, NameStyle, Title, FirstName, LastName
					FROM SalesLT.Customer;  -- In AdventureWorksLT database.
					";
				// Open a connection to database.
				connection.Open();

				// Read data returned for the query.
				C.SqlDataReader reader = command.ExecuteReader();
				while (reader.Read())
				{
					Console.WriteLine("Values:  {0}, {1}, {2}, {3}, {4}",
						reader[0], reader[1], reader[2], reader[3], reader[4]);
				}
			}
			Console.WriteLine("View the results here, then press any key to finish...");
			Console.ReadKey(true);
		}
		//----------------------------------------------------------------------------------

		static string GetConnectionStringFromExeConfig(string connectionStringNameInConfig)
		{
			G.ConnectionStringSettings connectionStringSettings =
				G.ConfigurationManager.ConnectionStrings[connectionStringNameInConfig];

			if (connectionStringSettings == null)
			{
				throw new ApplicationException(String.Format
					("Error. Connection string not found for name '{0}'.",
					connectionStringNameInConfig));
			}
				return connectionStringSettings.ConnectionString;
		}

		static string GatherPasswordFromConsole()
		{
			T.StringBuilder passwordBuilder = new T.StringBuilder(32);
			ConsoleKeyInfo key;
			Console.WriteLine("Enter your password: ");
			do
			{
				key = Console.ReadKey(true);
				if (key.Key != ConsoleKey.Backspace)
				{
					passwordBuilder.Append(key.KeyChar);
					Console.Write("*");
				}
				else  // Backspace char was entered.
				{
					// Retreat the cursor, overlay '*' with ' ', retreat again.
					Console.Write("\b \b");
					passwordBuilder.Length = passwordBuilder.Length - 1;
				}
			}
			while (key.Key != ConsoleKey.Enter); // Enter key will end the looping.
			Console.WriteLine(Environment.NewLine);
			return passwordBuilder.ToString();
		}
	}
}
```


### プログラムをコンパイルする


1. Visual Studio で、メニューから **[ビルド]**、**[ソリューションのビルド]** の順にクリックしてプログラムをコンパイルします。


### サンプル プログラム内の操作の概要


1. SQL 接続文字列の大部分を構成ファイルから読み取ります。

2. キーボードから入力されるユーザー名とパスワードを収集し、それを追加して接続文字列を完成させます。

3. 接続文字列と ADO.NET クラスを使用し、SQL Database の **AdventureWorksLT** デモンストレーション データベースに接続します。

4. SQL の **SELECT** を実行して **SalesLT** テーブルから読み取ります。

5. 返された行をコンソールに出力します。


C# サンプルのコードは常に短く記述するようにしています。しかし、ユーザーの皆様からのご要望を尊重して、このサンプルには構成ファイルを読み取るためのコードを追加しました。ご要望のとおり、実稼働品質のプログラムでは、.exe にハードコーディングしたリテラルではなく、構成ファイルを使用することが必要です。


> [AZURE.WARNING] コードの簡潔さを優先するため、この学習用サンプルには、例外処理と再試行ロジックのためのコードが含まれていません。ただし、クラウド データベースとやり取りをする実稼働プログラムには、両方のコードを加える必要があります。
>
> [こちら](sql-database-develop-csharp-retry-windows.md)のリンクから、再試行ロジックを備えたコード サンプルが確認できます。


## 手順 7. 使用できる IP アドレスの範囲をサーバーのファイアウォールに追加する


クライアント コンピューターの IP アドレスを SQL Database のファイアウォールに追加しない限り、クライアントの C# プログラムが SQL Database に接続することはできません。プログラムを実行しても失敗します。実行に失敗すると、必要な IP アドレスが記載された簡単なエラー メッセージが表示されます。


IP アドレスは、[Azure ポータル](https://portal.azure.com/)から追加できます。



[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]



詳細については、<br/>「[ファイアウォール設定の構成方法 (Azure SQL Database)](sql-database-configure-firewall-settings.md)」を参照してください。



## 手順 8. プログラムを実行する


1. Visual Studio で、メニューから **[デバッグ]**、**[デバッグ開始]** の順にクリックして C# のクエリ プログラムを実行します。コンソール ウィンドウが表示されます。

2. 指示に従って、ユーザー名とパスワードを入力します。
 - 一部の接続ツールでは、ユーザー名に "@{サーバー名}" を追加する必要がありますが、ADO.NET の場合、このサフィックスは省略可能です。このサフィックスを入力する必要はありません。

3. 数行のデータが表示されます。


## 関連リンク


- [SQL Database のクライアント クイック スタート コード サンプル](sql-database-develop-quick-start-client-code-samples.md)

- Azure VM でクライアント プログラムを実行する場合は、「<br/>[Ports beyond 1433 for ADO.NET 4.5, and SQL Database V12 (ADO.NET 4.5、SQL Database V12 における 1433 以外のポート)](sql-database-develop-direct-route-ports-adonet-v12.md)」を参照し、1433 以外の TCP ポートについて学んでください。



<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png

[30-VSNewProject]: ./media/sql-database-connect-query/connqry-vs-new-project-f.png

[40-VSProgramCsOverlay]: ./media/sql-database-connect-query/connqry-vs-program-cs-overlay-g.png

[50-VSCopyToOutputDirectoryProperty]: ./media/sql-database-connect-query/connqry-vs-appconfig-copytoputputdir-h.png

<!---HONumber=AcomDC_0211_2016-->