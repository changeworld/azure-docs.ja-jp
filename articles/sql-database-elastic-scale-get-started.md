<properties title="Get Started with  Azure SQL Database Elastic Scale" pageTitle="Azure SQL Database Elastic Scale の概要" description="サンプル アプリの簡単な実行方法を含めた Azure SQL Database Elastic Scale 機能の基本的な説明." metaKeywords="sharding scaling, Azure SQL DB sharding, elastic scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Azure SQL Database Elastic Scale の概要 (プレビュー)

オンデマンドによる容量の拡大や縮小は、クラウド コンピューティングで約束された主要な機能の一つです。この約束を実現するために、これまではクラウド アプリケーションのデータベース層での冗長で複雑な作業が必要でした。ここ数年、業界では、一般にはシャーディングとして知られる、確立された設計パターンの利用が増えてきました。一般的なシャーディング パターンによって問題を解決できますが、シャーディングを使用してアプリケーションを構築し管理するには、アプリケーションのビジネス ロジックと無関係に大規模なインフラストラクチャ投資が必要となります。 

Azure SQL データベースの Elastic Scale (プレビュー) は、業界標準のシャーディング手法によってアプリケーションのデータ層をスケール アウトおよびスケール インできるだけでなく、シャーディングされたクラウド アプリケーションの開発および管理を大幅に合理化できます。Elastic Scale の開発者機能と管理機能はいずれも、.Net のライブラリ セットと、Azure サブスクリプションでホストが可能な Azure サービス テンプレートを通じて提供されるもので、高度にスケーラブルなアプリケーションの管理が実現します。Azure DB Elastic Scale によってシャーディングという側面でのインフラトラクチャを実装することで、開発者はアプリケーションのビジネス ロジックに集中して取り組むことができます。 

このドキュメントでは、Azure SQL DB Elastic Scale の開発者エクスペリエンスを紹介します。 

Elastic Scale の機能の詳細については、「[Azure SQL Database Elastic Scale Overview (Azure SQL Database Elastic Scale の概要)](http://go.microsoft.com/?linkid=9862592)」を参照してください。

Elastic Scale のすべてのトピックの一覧については、「[Azure SQL Database Elastic Scale Topics (Azure SQL Database Elastic Scale のトピック)]」を参照してください。(./sql-database-elastic-scale-documentation-map.md)

## Elastic Scale のサンプル アプリケーション

次に説明するサンプルでは、単純なシャーディング アプリケーションを作成し、Elastic Scale の主な機能について詳しく見て行きます。このアプリケーションをダウンロードして実行するには、下記またはチャンネル 9 のビデオ「[Elastic Scale with Azure SQL Database: Getting Started (Azure SQL データベースで使用する Elastic Scale: 概要)](http://go.microsoft.com/?linkid=9862983)」の手順を実行します。 

###前提条件
サンプル アプリケーションを実行するには、Visual Studio を使用する必要があり、また、Azure 上で実行される Azure SQL データベースへのアクセス権を持っている必要があります。Azure サブスクリプションをまだ取得していない場合でも、[試用サブスクリプション](http://azure.microsoft.com/ja-jp/pricing/free-trial/)でサインアップできます。
####Visual Studio および Nuget

1. C# と Visual Studio 2012 以上が必要です。「[Visual Studio のダウンロード](http://www.visualstudio.com/ja-jp/downloads/download-visual-studio-vs.aspx)」で無償評価版をダウンロードしてください。
2. Nuget 2.7 以上。最新版を入手するには、「[Installing NuGet (NuGet のインストール)]」を参照してください。(http://docs.nuget.org/docs/start-here/installing-nuget)
####Azure SQL データベースの作成

* 詳細については、「[Microsoft Azure SQL データベースの概要](http://azure.microsoft.com/ja-jp/documentation/articles/sql-database-get-started/)」を参照してください。

##サンプル アプリケーションのダウンロードと実行
「**Elastic Scale with Azure SQL Database - Getting Started (Azure SQL データベースで使用する Elastic Scale - 概要)**」のサンプル アプリケーションは、Azure SQL DB Elastic Scale を使用したシャーディング アプリケーションの開発エクスペリエンスにおける最も重要な側面を表しています。ここでは、主な使用例として、[シャード マップ管理](http://go.microsoft.com/?linkid=9862595)、[データ依存ルーティング](http://go.microsoft.com/?linkid=9862596) 、および[マルチシャード クエリ実行](http://go.microsoft.com/?linkid=9862597)について説明しています。サンプルをダウンロードして実行するには、次の手順を実行します。 

1. Visual Studio を開き、**[ファイル]、[新規]、[プロジェクト]** の順に選択します。
2. ダイアログで、**[オンライン]** をクリックします。

    ![New Project>Online][2]
3. 次に、**[サンプル]** の **[Visual C#]** をクリックします。

    ![Click Visual C#][3]
4. 検索ボックスに「**Elastic Scale**」と入力してサンプルを検索します。**Elastic Scale with Azure SQL Database - Getting Started** (Azure SQL データベースで使用する Elastic Scale - 概要) というタイトルが表示されます。

    ![Search Box][1]
 
5. サンプルを選択し、新しいプロジェクトの名前と場所を選び、**[OK]** をクリックしてプロジェクトを作成します。
6. サンプル プロジェクトのソリューションで **app.config** ファイルを開き、ファイルの指示に従って使用する Azure SQL データベース サーバー名とログイン情報 (ユーザー名とパスワード) を追加します。
7. アプリケーションをビルドし、実行します。確認を求められたら、ソリューションの NuGet パッケージの復元を Visual Studio に許可してください。これで Elastic Scale クライアント ライブラリの最新版が NuGet からダウンロードされます。
8. Elastic Scale の機能をより深く知るために、さまざまなオプションを試してみてください。アプリケーションで実行されたステップはコンソールに出力されますので、動作していたコードをじっくりと検討することができます。

    ![progress][4]

これで、初めての Elastic Scale アプリケーションを正しく Azure SQL DB にビルドし、実行できました。Azure DB Server に SQL Server Management Studio を接続して、サンプルで作成したシャードの内容を簡単に確認してください。新しいサンプル シャード データベースと、シャード マップ マネージャー データベースがサンプルで作成されていることがわかります。

**注** SQL Server Management Studio を持っていない場合は、「[SQL Server Management Studio を使用した Azure SQL Database の管理](http://azure.microsoft.com/ja-jp/documentation/articles/sql-database-manage-azure-ssms/)」で、このツールの入手に関する手順を参照してください。  

### コード サンプルの主要部

1. **シャードとシャード マップの管理**:このコードは、シャード、範囲、マッピングが **ShardMapManagerSample.cs** ファイルでどのように機能するかを示します。このトピックの詳細については、「[Shard Map Management (シャード マップの管理)](http://go.microsoft.com/?linkid=9862595)」を参照してください。  
2. **データ依存ルーティング**:トランザクションの適切なシャードへのルーティングは、**DataDependentRoutingSample.cs** に示されます。詳細については、「[Data Dependent Routing (データ依存ルーティング)](http://go.microsoft.com/?linkid=9862596)」を参照してください。 
3. **複数のシャードにまたがるクエリ実行**:複数のシャードに対するクエリの実行は、**MultiShardQuerySample.cs** ファイルに示されます。詳細については、「[Multi-Shard Querying (マルチシャード クエリ実行)](http://go.microsoft.com/?linkid=9862597)」を参照してください。
4. **空のシャードの追加**:新しい空のシャードの反復追加は、
**AddNewShardsSample.cs** ファイルのコードによって実行されます。このトピックの詳細については、「[Shard Map Management (シャード マップの管理)](http://go.microsoft.com/?linkid=9862595)」を参照してください。

### 他の Elastic Scale の操作

1. **既存のシャードの分割**:シャードを分割する機能は、**Split または Merge サービス**を通じて提供されます。このサービスの詳細については、「[Elastic Scale Security Configurations (Elastic Scale セキュリティの構成)](http://go.microsoft.com/?linkid=9862795)」を参照してください。
2. **既存のシャードのマージ**:シャードのマージも **Split サービスまたは Merge サービス**を使用して行われます。詳細については、「[Elastic Scale Security Configurations (Elastic Scale セキュリティの構成)](http://go.microsoft.com/?linkid=9862795)」を参照してください。   


## コスト

Elastic Scale ライブラリとサービス テンプレートは無料です。Elastic Scale では、Azure の利用料以外の追加料金は発生しません。 

たとえば、サンプル アプリケーションは新しいデータベースを作成します。その場合のコストは、選択した Azure SQL DB データベースのエディションと、アプリケーションによる Azure の使用状況に応じて異なります。

料金情報については、「[SQL Database の料金詳細](http://azure.microsoft.com/ja-jp/pricing/details/sql-database/)」を参照してください。

## 次のステップ
Elastic Scale の機能の詳細については、以下のページを参照してください。

* [Elastic Scale の学習ページ](./sql-database-elastic-scale-documentation-map.md) 
-    コード サンプル: 
    -    [Elastic Scale with Azure SQL Database - Getting Started (Azure SQL データベースで使用する Elastic Scale - 概要)](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Elastic Scale with Azure DB - Entity Framework Integration (Azure DB で使用する Elastic Scale - Entity Framework による統合](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [スクリプト センター「Azure SQL Database Elastic Scale: Shard Elasticity (Azure SQL データベース Elastic Scale: シャードの弾力性)」](http://go.microsoft.com/?linkid=9862617)
-    ブログ:[Introducing Elastic Scale preview for Azure SQL Database (Azure SQL データベースで使用する Elastic Scale プレビューの紹介)](http://go.microsoft.com/?linkid=9862608)
-    チャンネル 9:[ビデオ「Azure SQL Database Elastic Scale」](http://go.microsoft.com/?linkid=9862609)
-    ディスカッション フォーラム:[Microsoft Azure フォーラム](http://social.msdn.microsoft.com/forums/azure/en-US/home?forum=ssdsgetstarted)


<!--Anchors-->
[Elastic Scale のサンプル アプリケーション]: #The-Elastic-Scale-Sample-Application
[サンプル アプリケーションのダウンロードと実行]: #Download-and-Run-the-Sample-App
[コスト]: #Cost
[次のステップ]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png

<!--HONumber=35_1-->
