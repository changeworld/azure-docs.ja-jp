<properties 
	pageTitle="Elastic Database ツールの概要" 
	description="実行が容易なサンプル アプリケーションを含む、Azure SQL Database の Elastic Database ツール機能の基本説明です。" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="ddove" 
	editor="CarlRabeler"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2016" 
	ms.author="ddove"/>

# Elastic Database ツールの概要

このドキュメントでは、サンプル アプリを実行して開発作業について説明します。サンプルでは単純なシャーディング アプリケーションを作成し、Elastic Database ツールの主な機能について詳しく見て行きます。サンプルでは、[Elastic Database クライアント ライブラリ](sql-database-elastic-database-client-library.md)の機能が実演されます。

ライブラリをインストールするには、[Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) に移動します。ライブラリは、次に示すサンプル アプリと共にインストールされることに注意してください。

## 前提条件

1. C# と Visual Studio 2012 以上が必要です。「[Visual Studio のダウンロード](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)」で無償評価版をダウンロードしてください。
2. Nuget 2.7 以上。最新版を入手するには、「[Installing NuGet (NuGet のインストール)](http://docs.nuget.org/docs/start-here/installing-nuget)」を参照してください。

## サンプル アプリケーションのダウンロードと実行

**Azure SQL で使用する Elastic Database の概要**のページで示したサンプル アプリケーションでは、Azure SQL Elastic Database ツールを使用したシャーディング アプリケーションの開発エクスペリエンスにおける最も重要な側面を紹介しています。そこでは、主な使用事例として[シャード マップの管理](sql-database-elastic-scale-shard-map-management.md)、[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)、および[マルチシャード クエリ](sql-database-elastic-scale-multishard-querying.md)に焦点を当てています。サンプルをダウンロードして実行するには、次の手順を実行します。

1. Visual Studio を開き、**[ファイル]、[新規]、[プロジェクト]** の順に選択します。
2. ダイアログで、**[オンライン]** をクリックします。

    ![New Project>Online][2]
3. 次に、**[サンプル]** の **[Visual C#]** をクリックします。

    ![Click Visual C#][3]
4. 検索ボックスに「**elastic db**」と入力してサンプルを検索します。**Elastic DB Tools for Azure SQL - Getting Started** (Azure SQL で使用する Elastic DB ツール - 概要) というタイトルが表示されます。

    ![Search Box][1]
 
5. サンプルを選択し、新しいプロジェクトの名前と場所を選び、**[OK]** をクリックしてプロジェクトを作成します。
6. サンプル プロジェクトのソリューションで **app.config** ファイルを開き、ファイルの指示に従って使用する Azure SQL データベース サーバー名とログイン情報 (ユーザー名とパスワード) を追加します。
7. アプリケーションをビルドし、実行します。確認を求められたら、ソリューションの NuGet パッケージの復元を Visual Studio に許可してください。これで Elastic Database クライアント ライブラリの最新版が NuGet からダウンロードされます。
8. クライアント ライブラリの機能をより深く知るために、さまざまなオプションを試してみてください。アプリケーションで実行されたステップはコンソールに出力されますので、動作していたコードをじっくりと検討することができます。

    ![progress][4]

これで、最初のシャーディング アプリケーションを Elastic Database ツールを使用して正しく Azure SQL Database にビルドし、実行できました。Azure DB Server に Visual Studio または SQL Server Management Studio を接続して、サンプルで作成したシャードの内容を簡単に確認してください。新しいサンプル シャード データベースと、シャード マップ マネージャー データベースがサンプルで作成されていることがわかります。

> [AZURE.IMPORTANT] 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。[SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。


### コード サンプルの主要部

1. **シャードとシャード マップの管理**: このコードは、シャード、範囲、マッピングが **ShardMapManagerSample.cs** ファイルでどのように機能するかを示します。このトピックの詳細については、「[Shard Map Management (シャード マップの管理)](http://go.microsoft.com/?linkid=9862595)」を参照してください。
2. **データ依存ルーティング**: トランザクションの適切なシャードへのルーティングは、**DataDependentRoutingSample.cs** に示されます。詳細については、「[Data Dependent Routing (データ依存ルーティング)](http://go.microsoft.com/?linkid=9862596)」を参照してください。
3. **複数のシャードにまたがるクエリ実行**: 複数のシャードに対するクエリの実行は、**MultiShardQuerySample.cs** ファイルに示されます。詳細については、「[Multi-Shard Querying (マルチシャード クエリ実行)](http://go.microsoft.com/?linkid=9862597)」を参照してください。
4. **空のシャードの追加**: 新しい空のシャードの反復追加は、**AddNewShardsSample.cs** ファイルのコードによって実行されます。このトピックの詳細は、「[Shard Map Management (シャード マップの管理)](http://go.microsoft.com/?linkid=9862595)」に記載されています。

### 他の Elastic Scale の操作

1. **既存のシャードの分割**: シャードを分割する機能は、**分割/マージ ツール**を通じて提供されます。このツールの詳細については、「[分割/結合ツールの概要](sql-database-elastic-scale-overview-split-and-merge.md)」を参照してください。
2. **既存のシャードのマージ**: シャードのマージも**分割/マージ ツール**を使用して行われます。詳細については、「[分割/マージ ツールの概要](sql-database-elastic-scale-overview-split-and-merge.md)」を参照してください。


## コスト

Elastic Database ツールは無料です。Elastic Database ツールでは、Azure の利用料以外の追加料金は発生しません。

たとえば、サンプル アプリケーションは新しいデータベースを作成します。その場合のコストは、選択した Azure SQL DB データベースのエディションと、アプリケーションによる Azure の使用状況に応じて異なります。

料金情報については、「[SQL Database の料金詳細](https://azure.microsoft.com/pricing/details/sql-database/)」を参照してください。

## 次のステップ
Elastic Database ツールの詳細については、以下のページを参照してください。

* [Elastic Database ツールのドキュメント マップ](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)
-    コード サンプル:
    -    [Azure SQL で使用する Elastic DB - 概要](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Azure DB で使用する Elastic DB - Entity Framework との統合](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [スクリプト センターのシャードの弾力性](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
-    ブログ: [Elastic Scale の発表](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
-    チャンネル 9: [Elastic Scale の概要に関するビデオ](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
-    ディスカッション フォーラム: [Azure SQL Database フォーラム](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
-    パフォーマンスを測定するには: [シャード マップ マネージャーのパフォーマンス カウンター](sql-database-elastic-database-client-library.md)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 

<!---HONumber=AcomDC_0803_2016-->