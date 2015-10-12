<properties
   pageTitle="作業開始: Azure SQL Data Warehouse への接続 | Microsoft Azure"
   description="SQL Data Warehouse に接続して、クエリを実行する"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# 作業開始: Azure SQL Data Warehouse への接続
このクイック スタートの記事では、次の 2 つのツールを使用して、SQL Data Warehouse のプロビジョニング済みインスタンスに接続し、そのインスタンスに対してクエリを実行する方法を紹介します。

- **Visual Studio** - SQL Data Warehouse との完全な互換性を備えた、Visual Studio の統合コード エディター兼デバッガーである、SQL Server Data Tools (SSDT) を使用すると、SQL Data Warehouse に簡単に接続し、クエリを実行できます。また、SQL Data Warehouse の管理も容易になります。  

> [AZURE.NOTE]SQL Data Warehouse では、少なくとも SSDT Preview バージョン 12.0.50623 以降が必要です。

- **sqlcmd** - sqlcmd は、シンプルな接続機能とクエリ実行機能が用意されているコマンド ライン ツールです。  

この記事の操作を行うことで、次の作業が完了します。

1. Visual Studio 2013 のインストールと更新。
2. SSDT での SQL Data Warehouse への接続。
3. SQL Data Warehouse データベースに対するクエリの実行。

>[AZURE.NOTE]ここでは、プロビジョニング ガイドを完了しているか、SQL Data Warehouse が利用できることを前提としています。SQL Data Warehouse のプロビジョニングが完了していない場合は、[プロビジョニングのクイック スタート](sql-data-warehouse-get-started-provision.md)を参照してください。

## 開発向け Visual Studio のセットアップ##
開発で Visual Studio を使う場合、SQL Data Warehouse チームでは、Visual Studio 2013 以降と SQL Server Data Tools を組み合わせて使用することをお勧めします。ここでは、Visual Studio の有効なバージョンがインストールされていない場合に、Visual Studio 2013 をダウンロードして更新する方法について簡単に説明します。

さらに詳しい情報が必要な場合、SSDT の一般的な質問については、[詳細な SSDT ドキュメント](https://msdn.microsoft.com/library/azure/hh272686.aspx)で解決できます。

### Visual Studio 2013 を入手する ###
Visual Studio 2013 の Web サイトにアクセスして、Visual Studio のコピーをダウンロードし、インストールします。その際、SQL Data Warehouse の無料エディションで十分であるという点に注意します。ニーズに応じて好きなものを選んでください。

<a href="https://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5" target="blank">Visual Studio 2013 を入手する</a>

### Visual Studio 2013 を更新する ###
Visual Studio 2013 はインストールされていますか。 以上です。 インストールされているバージョンが最新であることを確認するには、次の手順を実行します。その後、次の手順に進むことができます。

1. Visual Studio 2013 を開きます。
2. **[ツール]** メニューの **[拡張機能と更新プログラム]** をクリックします。
3. ツリー コントロールで **[更新プログラム]** および **[製品の更新プログラム]** に移動します。
4. 使用可能な更新プログラムがない場合は、そのまま **[拡張機能と更新プログラム]** ウィンドウを閉じ、このクイック スタートの次のタスクに進むことができます。

ただし、Visual Studio 自体の更新プログラムがある場合は、**[更新]** をクリックします。これにより、Visual Studio 2013 の最新の更新プログラムのダウンロードが開始されます。

**[拡張機能と更新プログラム]** ウィンドウを閉じ、次に進む前に Visual Studio 2013 も終了します。

5. **[実行]** をクリックして、Visual Studio 2013 に最新の更新プログラムをインストールします。

6. ライセンス条項に同意し、**[インストール]** をクリックして、任意のユーザー アカウント制御 (UAC) プロンプトを受け入れます。

7. **[起動]** をクリックして、インストールを完了します。

これで Visual Studio 2013 の更新は完了です。

### SSDT を更新する
> [AZURE.IMPORTANT]SQL Data Warehouse では、少なくとも SSDT Preview バージョン 12.0.50623 以降が必要です。

SSDT エンジニアは、新しい機能を持つプラグインを定期的に更新しています。このため、SSDT は時々更新する必要があります。繰り返しますが、これは非常に簡単なプロセスです。SSDT を更新する必要があるかどうかを確認するには、次の手順を実行します。

1. Visual Studio 2013 を開きます。  
2. **[ツール]** メニュー、**[拡張機能と更新プログラム]** の順に選択します。
3. ツリー コントロールで **[更新プログラム]** および **[製品の更新プログラム]** に移動します。
4. 使用可能な更新プログラムがない場合は、そのまま **[拡張機能と更新プログラム]** ウィンドウを閉じ、このクイック スタートの次のタスクに進むことができます。

ただし、"データベース ツール用の Microsoft SQL Server 更新プログラム" と呼ばれる更新プログラムがある場合は、**[更新]** をクリックします。これにより、SSDT の最新バージョンのダウンロードが開始されます。

5. **[インストール]** をクリックして、SSDT の最新バージョンをインストールします。

6. ライセンス条項に同意し、**[インストール]** をクリックします

7. **[閉じる]** をクリックし、SSDT の更新を完了します

8. **[拡張機能と更新プログラム]** ウィンドウを閉じます

これで、最新バージョンの Visual Studio 2013 と最新の SSDT 拡張機能がデスクトップにインストールされました。

> [AZURE.NOTE]現時点では、[SSDT Preview for Visual Studio 2013 バージョン 12.0.50623 以降](http://go.microsoft.com/fwlink/?LinkID=616714&clcid=0x409)をお勧めします

## Visual Studio 2013 に接続する
目的のバージョンの Visual Studio を実行している場合は、2 つの方法で SQL Data Warehouse インスタンスに接続できます。

### Visual Studio からの接続
接続を確立するには、SQL Server オブジェクト エクスプローラーを開き、接続情報を指定する必要があります。

1. Visual Studio を開きます。
2. **[表示]** メニューを選択し、ドロップダウン リストで **[SQL Server オブジェクト エクスプローラー]** を選択します。

> [AZURE.NOTE]必ず SQL Server オブジェクト エクスプローラーを選択してください。サーバー エクスプローラーでは*ありません*。この 2 つは名前が似ていますが、まったく別のコントロールです。隣同士に配置されているので、正しい方を選択したことを必ず確かめてください。

これで、SQL Server オブジェクト エクスプローラーが表示されます。


3. SQL Server オブジェクト エクスプローラーで、**[サーバーの追加]** をクリックします。

4. 論理サーバーを作成する場合、**[サーバーに接続]** ダイアログ ボックスに値を記入します。また、[オプション] ボタンをクリックし、接続する前に (SQL Data Warehouse インスタンス) に接続するデータベースを指定します。

必要に応じて、**[パスワードを保存する]** をオンにします。このボックスをオンにすると時間の節約になりますが、プロファイルに物理的にアクセスできるすべての人が、このアカウントを使用してクエリを実行できることに注意してください。

> [AZURE.NOTE]サーバー名には完全修飾名を使用する必要があります。したがって、サーバー名の値は、*ServerName*.database.windows.net という規則に従う必要があります。この *ServerName* は、論理サーバーに付けた名前です。

すべての資格情報を入力したら、**[接続]** をクリックします。

これで接続は完了です。SQL Server オブジェクト エクスプ ローラーでは SQL Data Warehouse 論理サーバーも登録しました。

### Azure ポータルからの接続
Azure ポータルから直接 Visual Studio を入手します。

1. [Azure ポータル](http://manage.windowsazure.com/)にサインインします。
2. **[参照]** ブレード内で、目的の SQL Data Warehouse インスタンスを選択します。
3. SQL Data Warehouse ブレードの上部にある **[Visual Studio で開く]** を選択します。
4. クライアント コンピューターの IP アドレスでファイアウォールを構成していない場合は、**[ファイアウォールの構成]** を選択します。

	a.**[ルール名]**、**[開始 IP]**、および **[終了 IP]** に入力します。

	b.ブレードの上部にある **[保存]** をクリックします。
5. **[Visual Studio で開く]** をクリックします。
6. Visual Studio が開き、資格情報を入力するよう求められます。
7. 資格情報を入力して接続すると、サーバーと Data Warehouse インスタンスが [SQL Server オブジェクト エクスプ ローラー] パネルに表示されます。  

## sqlcmd で SQL Data Warehouse に接続する

SQL Server または [Microsoft Command Line Utilities 11 For SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501) に含まれる [sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx) コマンド プロンプト ユーティリティを使用して、SQL Data Warehouse に接続することもできます。sqlcmd ユーティリティを使用すると、コマンド プロンプトで Transact-SQL ステートメント、システム プロシージャ、およびスクリプト ファイルを入力できます。

sqlcmd を使用するときに SQL Data Warehouse の特定のインスタンスに接続するには、コマンド プロンプトを開いて、**sqlcmd** の後に、SQL Data Warehouse データベースの接続文字列を入力する必要があります。接続文字列では、次のパラメーターを指定します。

+ **ユーザー (-U):** サーバー ユーザー。`<`User`>`@`<`Server Name`>`.database.windows.net の形式で指定します。
+ **パスワード (-P):** ユーザーに関連付けられているパスワード。
+ **サーバー (-S):** サーバー。`<`Server Name`>`.database.windows.net の形式で指定します。
+ **データベース (-D):** データベース名。
+ **引用符で囲まれた ID の有効化 (-I):** SQL Data Warehouse インスタンスに接続するには、引用符で囲まれた ID を有効にする必要があります。

したがって、SQL Data Warehouse インスタンスに接続するには、次のように入力します。

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

接続後、インスタンスに対してサポートされているすべての Transact-SQL ステートメントを発行できます。たとえば、以下のステートメントでは、[CREATE TABLE](https://msdn.microsoft.com/library/azure/dn268335.aspx) ステートメントを使用して、新しいテーブルを作成します。

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> CREATE TABLE table1 (Col1 int, Col2 varchar(20));
2> GO
3> QUIT
```

sqlcmd の詳細については、[sqlcmd のドキュメント](https://msdn.microsoft.com/library/azure/ms162773.aspx)を参照してください。

<!--NOTE: SQL DB does not support the -z/-Z parameters for changing users password with SQLCMD.  Do we support this? -->

## サンプル クエリの実行 ##

サーバーの登録が完了したので、次はクエリを記述してみましょう。

1. SSDT でユーザー データベースをクリックします。

2. **[新しいクエリ]** をクリックします。新しいウィンドウが開きます。

3. クエリを記述します。クエリ ウィンドウに次のコードを入力します。

	```
	SELECT  COUNT(*)
	FROM    sys.tables;
	```

4. クエリを実行します。

	クエリを実行するには、緑色の矢印をクリックするか、`CTRL`+`SHIFT`+`F5` のショートカット キーを使用します。

## 次のステップ ##
これで接続してクエリを実行し、[サンプル データを読み込んだり][]、[コードを開発したり][]できます。

[サンプル データを読み込んだり]: ./sql-data-warehouse-get-started-load-samples.md
[コードを開発したり]: ./articles/sql-data-warehouse-overview-develop/

<!---HONumber=Oct15_HO1-->