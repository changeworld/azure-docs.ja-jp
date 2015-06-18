<properties 
	pageTitle="最新の SQL データベースの更新プログラムの V12 (プレビュー) を作成する" 
	description="最新の SQL データベースの更新プログラムの V12 (プレビュー) を作成する" 
	services="sql-database" 
	documentationCenter="" 
	authors="sonalmm" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="12/11/2014" 
	ms.author="sonalm"/>


# 最新の SQL データベースの更新プログラムの V12 (プレビュー) を作成する

最新の SQL Database 更新プログラム V12 に[サインアップ](https://portal.azure.com)すると、Microsoft Azure で次世代の SQL Database の機能を活用できるようになります。使用を開始するには、Microsoft Azure のサブスクリプションが必要です。[Azure の無料評価版](http://azure.microsoft.com/pricing/free-trial)にサインアップして[料金](http://azure.microsoft.com/pricing/details/sql-database)の情報をご確認ください。 


| データベースを作成する | スクリーン ショット |
| :--- | :--- |
| 1.[http://portal.azure.com/](http://portal.azure.com/) にサインインします。 | ![New Azure Portal][1] |
| 2.ページの左下の **[新規]** をクリックします。 | ![Initiate New service][2]|
| 3.**[SQL データベース]** をクリックします。| ![Different services to select from][3] |
| 4.**[SQL データベース]** ブレードが開きます。**[名前]** フィールドに、データベース名を指定します。 | ![Name the database][4] |
| 5.**[SQL データベース]** ブレードで、**[サーバー]** をクリックします。**[サーバー]** ブレードが開きます。このブレードでは、新しいサーバーを作成するか、既存のサーバーを使用するかを選択できます。| ![select type of server][4] |
|5a.**[既存のサーバーを使用する]** オプションを選択した場合は、使用するサーバーを選び、**[選択]** をクリックします。次に、手順 6 以降のすべての処理を完了します。| ![select a server from the list][5]| 
|5b.   **[新しいサーバーを作成する]** を選択すると、**[新しいサーバー]** ブレードが開きます。サーバー名、サーバー管理ログイン、パスワードを指定します。**[場所]** をクリックして   サーバーの場所を選択します。 | ![Complete create new server options][9]| 
|5c. **[新しいサーバー]** ブレードでは、V12 更新プログラムを適用した新しいサーバーを作成するオプションを選択できます。V12 サーバーの機能の詳細については、「[Azure SQL Database の新機能](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)」をご覧ください。| ![Select V12 server][6]|
|5d.**[新しいサーバー]** ブレードで必要な選択をして **[OK]** をクリックします。**[SQL データベース]** ブレードが再び表示され、データベース作成のための残りの操作を完了できます。 | ![Complete New Server blade actions][8]|
|6.**[ソースの選択]** をクリックします。データベースを作成するために選択できるソースの種類として、空のデータベース、サンプル データベース、データベースのバックアップがあります。| ![Select the source for the database][10]|
|7.次に、**[SQL データベース]** ブレードで **[料金レベル]** をクリックします。推奨の料金レベルを選択することも、利用可能な料金レベルをすべて参照することもできます。選択したら、**[選択]** をクリックします。 <p> 料金レベルの詳細については、「[SQL データベース Web/Business データベースを新しい料金レベルにアップグレードする](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/)」と「[Azure SQL データベース サービス階層とパフォーマンス レベル](http://msdn.microsoft.com/library/azure/dn741336.aspx)」をご覧ください。 |![Select a pricing tier][7]
| 8.次に、**[SQL データベース]** ブレードで **[オプションの構成]** をクリックし、必要な選択をして **[OK]** をクリックします。 
| 9.**[SQL データベース]** ブレードで、**[リソース グループ]** をクリックします。新しいリソース グループを作成するか、一覧から既存のリソース グループを選択します。**[OK]** をクリックします。詳細については、「[リソース グループを使用した Azure リソースの管理](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups)」をご覧ください。|![Specify Resource group][11]
| 10.**[SQL データベース]** ブレードで、**[サブスクリプション]** をクリックします。**[サブスクリプション]** ブレードが開きます。選択したサーバーをサポートしている Azure サブスクリプションを選択します。サブスクリプションの選択に応じて、サーバーの設定が変わる場合があることに注意してください。| ![Select subscription.][13]
| 11.**[作成]** をクリックします。SQL Database 更新プログラム V12 の機能を持った新しいデータベースが作成されます。 |![Creates a new database][12]

# 関連リンク  #

-  [Azure SQL Database の新機能](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [Azure SQL Database V12 へのアップグレードの計画と準備をする](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)

<!--Image references-->
[1]: ./media/sql-database-preview-create/firstscreenportal.png
[2]: ./media/sql-database-preview-create/new.png
[3]: ./media/sql-database-preview-create/sqldatabase.png
[4]: ./media/sql-database-preview-create/databasename.png
[5]: ./media/sql-database-preview-create/useexistingserver.PNG
[6]: ./media/sql-database-preview-create/v12server.PNG
[7]: ./media/sql-database-preview-create/pricingtierdetails.png
[8]: ./media/sql-database-preview-create/finishnewserverblade.png
[9]: ./media/sql-database-preview-create/createnewserver.png
[10]: ./media/sql-database-preview-create/selectsource.png
[11]: ./media/sql-database-preview-create/resourcegroup.png
[12]: ./media/sql-database-preview-create/create.png
[13]: ./media/sql-database-preview-create/subscription.PNG


<!--HONumber=47-->
 