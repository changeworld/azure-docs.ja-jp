<properties 
	pageTitle="SQL Database の更新プログラムの V12 でデータベースを作成する" 
	description="Azure SQL Database の更新プログラムの V12 でデータベースを作成する方法を示しています" 
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
	ms.date="04/28/2015" 
	ms.author="sonalm"/>


# SQL Database V12 でデータベースを作成する


<!--
True author is: authors="sonalmm" , ms.author="sonalm".
-->


SQL Database V12 [(一部の地域ではプレビュー)](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable) に[サインアップ](https://portal.azure.com)すると、Microsoft Azure 上の次世代の SQL Database の機能を活用できるようになります。使用を開始するには、Microsoft Azure のサブスクリプションが必要です。Azure の[無料評価版](http://azure.microsoft.com/pricing/free-trial)にサインアップして[料金](http://azure.microsoft.com/pricing/details/sql-database)の情報をご確認ください。


| データベースを作成する | スクリーン ショット |
| :--- | :--- |
| 1\.[http://portal.azure.com/](http://portal.azure.com/) にサインインします。 | ![新しい Azure ポータル][1] |
| 2\.ページの左下の **[新規]** をクリックします。 | ![新しいサービスの開始][2]|
| 3\.**[SQL Database]** をクリックします。| ![選択するさまざまなサービス][3] |
| 4\.**[SQL Database]** ブレードが開きます。**[名前]** フィールドに、データベース名を指定します。 | ![データベースを名前を付けます][4] |
| 5\.**[SQL Database]** ブレードで、**[サーバー]** をクリックします。**[サーバー]** ブレードが開きます。このブレードでは、新しいサーバーを作成するか、既存のサーバーを使用するかを選択できます。| ![サーバーの種類を選択します][4] |
|5a.**[既存のサーバーを使用する]** オプションを選択した場合は、使用するサーバーを選び、**[選択]** をクリックします。次に、手順 6 以降のすべての処理を完了します。| ![一覧からサーバーを選択します][5]| 
|5b.**[新しいサーバーを作成する]** を選択すると、**[新しいサーバー]** ブレードが開きます。サーバー名、サーバー管理ログイン、パスワードを指定します。**[場所]** をクリックしてサーバーの場所を選択します。 | ![サーバーの新しいオプションの作成を完了します][9]| 
|5c. **[新しいサーバー]** ブレードでは、V12 更新プログラムを適用した新しいサーバーを作成するオプションを選択できます。V12 サーバーの機能の詳細については、「[Azure SQL Database V12 の新機能](sql-database-v12-whats-new.md)」をご覧ください。| ![V12 サーバーを選択します][6]|
|5d.**[新しいサーバー]** ブレードで必要な選択をして **[OK]** をクリックします。**[SQL Database]** ブレードが再び表示され、データベース作成のための残りの操作を完了できます。 | ![新しいサーバー ブレードのアクションを完了します][8]|
|6\.**[ソースの選択]** をクリックします。データベースを作成するために選択できるソースの種類として、空のデータベース、サンプル データベース、データベースのバックアップがあります。| ![データベースのソースを選択します][10]|
|7\.次に、**[SQL Database]** ブレードで **[料金レベル]** をクリックします。推奨の料金レベルの一つを選択することも、利用可能な料金レベルを**すべて参照する**こともできます。選択したら、**[選択]** をクリックします。<p>料金レベルの詳細については、「[SQL Database Web/Business データベースを新しいサービス階層にアップグレードする](./sql-database-upgrade-new-service-tiers/)」と「[Azure SQL Database サービス階層とパフォーマンス レベル](sql-database-service-tiers.md)」をご覧ください。 |![料金レベルを選択します][7]
| 8\.次に、**[SQL Database]** ブレードで **[オプションの構成]** をクリックし、必要な選択をして **[OK]** をクリックします。 
| 9\.既存のサーバーを選択すると、**[リソース グループ]** と **[サブスクリプション]** が既に選択されています。 **[SQL Database]** ブレードで、**[リソース グループ]** と **[サブスクリプション]** の隣にロックされたアイコンが表示されます。新しいサーバーを作成する場合は、リソース グループを選択するか、作成します。詳細については、「[リソース グループを使用した Azure リソースの管理](resource-group-overview.md)」をご覧ください。|![リソース グループを指定します][11]
| 10\.**[作成]** をクリックします。SQL Database V12 の機能を持った新しいデータベースが作成されます。 |![新しいデータベースを作成します][12]

## 関連リンク

- [SQL Database V12 の新機能](sql-database-v12-whats-new.md)
- [Azure SQL Database V12 へのアップグレードの計画と準備をする](sql-database-v12-plan-prepare-upgrade.md)

<!--Image references-->
[1]: ./media/sql-database-create/firstscreenportal.png
[2]: ./media/sql-database-create/new.png
[3]: ./media/sql-database-create/sqldatabase.png
[4]: ./media/sql-database-create/databasename.png
[5]: ./media/sql-database-create/useexistingserver.PNG
[6]: ./media/sql-database-create/v12server.PNG
[7]: ./media/sql-database-create/pricingtierdetails.png
[8]: ./media/sql-database-create/finishnewserverblade.png
[9]: ./media/sql-database-create/createnewserver.png
[10]: ./media/sql-database-create/selectsource.png
[11]: ./media/sql-database-create/resourcegroup.png
[12]: ./media/sql-database-create/create.png

 

<!---HONumber=Oct15_HO3-->