<properties
	pageTitle="SQL Database の使用"
	description="クラウドで提供される Microsoft のリレーショナル データベース管理サービス (RDBMS) である Azure SQL Database を利用し、Azure ポータルと AdventureWorks サンプル データベースを使って初めてのクラウド データベースを数分で作成します。"
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
	ms.topic="hero-article"
	ms.date="07/15/2015"
	ms.author="genemi"/>


# 最初の Azure SQL データベースを作成する


この記事では、サンプルの Azure SQL データベースをわずか 5 分で作成する方法を示します。学習内容:


- [Azure ポータル](http://portal.azure.com/)を使用して、論理サーバーをプロビジョニングする。
- サンプル データが設定されたデータベースを作成する。
- データベースにアクセスできる IP アドレスを構成するための、データベースのファイアウォール ルールを設定する。


このチュートリアルでは、Azure サブスクリプションを取得していることを前提としています。Azure サブスクリプションをまだ取得していない場合は、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)にサインアップできます。


## 手順 1. サインインする


1. [Azure ポータル](http://portal.azure.com/)にサインインします。
2. **[新規]**、**[データ + ストレージ]**、**[SQL データベース]** の順にクリックします。


![New SQL Database][1]


## 手順 2. 論理サーバーを作成する



1. [SQL Database] ブレードで、データベースの**名前**を選択します (この例では、**AdventureWorks**)。
2. データベースの論理サーバーを作成するには、**[サーバー]** をクリックし、**[新しいサーバーの作成]** をクリックします。


## 手順 3. サーバーを構成する


1. **[サーバー]** ブレードで、覚えやすい一意の**サーバー名**を入力します。
2. **[サーバー管理者のログイン]** に「**AdventureAdmin**」と入力します。
3. **[パスワード]** と **[パスワードの確認]** に適切な値を入力します。
4. **[場所]** で、適切な地理上の場所を選びます。通常は、アプリケーションの実行場所に近い場所を選びます。
5. **[OK]** をクリックします。


![Create server][2]


## 手順 4. データベースを作成する


1. [SQL Database] ブレードで、**[ソースの選択]** をクリックし、データベースのソースを指定します。
 - この手順を省略すると、空のデータベースが作成されます。
2. **[サンプル]** を選びます。
 - 標準のサンプル データベースがコピーされ、**AdventureWorks** という名前のデータベースが作成されます。
 - Azure SQL Database では、AdventureWorks の*軽量スキーマ* エディションが使用されます。
3. ブレードの下部にある **[作成]** をクリックします。


## 手順 5. ファイアウォールを構成する


次の手順では、データベースにアクセスできる IP アドレスの範囲を指定する方法を示します。


![Browse server][3]


1. 画面の左側にあるリボンで、**[参照]** をクリックし、**[SQL サーバー]** をクリックします。
2. 選択可能な項目の中から、先ほど作成した SQL サーバーをクリックします。
3. **[設定]** をクリックし、**[ファイアウォール]** をクリックします。
4. このリンクをクリックして、[Bing](http://www.bing.com/search?q=my%20ip%20address) から現在の IP アドレスを取得します。
5. [ファイアウォール設定] で、**[ルール名]** にルール名を入力し、前の手順で取得したパブリック IP アドレスを **[開始 IP]** ボックスと **[終了 IP]** ボックスに入力します。
6. 作業が完了したら、ページの上部にある **[保存]** をクリックします。


![ファイアウォール][4]


## 次のステップ


これで、データベースに接続できる小さなクライアント プログラムを作成する準備が整いました。クイック スタート用のコード サンプルについては、次のいずれかのリンクをクリックしてください。


- [C# を使用して SQL データベースに接続し、照会する](sql-database-connect-query.md)
- *近日公開予定:* SQL Database のクライアント開発とクイック スタート サンプル


<!-- Media references. -->
[1]: ./media/sql-database-get-started/GettingStarted_NewDB.PNG
[2]: ./media/sql-database-get-started/GettingStarted_CreateServer.png
[3]: ./media/sql-database-get-started/GettingStarted_BrowseServer.png
[4]: ./media/sql-database-get-started/GettingStarted_FireWall.png
 

<!---HONumber=July15_HO3-->