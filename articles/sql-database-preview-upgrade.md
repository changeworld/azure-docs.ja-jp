<properties 
	pageTitle="最新の SQL データベースの更新プログラムの V12 (プレビュー) にアップグレードする" 
	description="最新の SQL データベースの更新プログラムの V12 (プレビュー) にアップグレードする" 
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



# 最新の SQL データベースの更新プログラムの V12 (プレビュー) にアップグレードする


最新の SQL データベースの更新プログラム V12 に[サインアップ](https://portal.azure.com)すると  Microsoft Azure で次世代の SQL Database の機能を活用できるようになります。使用を開始するには、Microsoft Azure のサブスクリプションが必要です。[Azure の無料評価版](http://azure.microsoft.com/pricing/free-trial)にサインアップして[料金](http://azure.microsoft.com/pricing/details/sql-database)の情報をご確認ください。 

## 最新の SQL Database 更新プログラムでサーバーをアップグレードする方法 ##

| アップグレード  | スクリーン ショット |
| :--- | :--- |
| 1.[http://portal.azure.com/](http://portal.azure.com/) にサインインします。 | ![New Azure Portal][1] |
| 2.**[参照]** をクリックします。 | ![Browse Services][2] |
| 3.	**[SQL Server]** をクリックします。SQL Server 名の一覧が表示されます。 | ![Select SQL Server service][3] |
| 4.SQL Database 更新プログラムを有効にした新しいサーバーにコピーするサーバーを選択します。 | ![Shows a list of SQL Servers][4] |
| 5.**[最新の SQL Database 更新プログラム V12]** をクリックします。 | ![Latest preview feature][5] |
| 6.**[このサーバーをアップグレード]** をクリックします。 | ![Upgrades the SQL Server to the preview][6] |

> [AZURE.NOTE] **重要**:アップグレード オプションを選択すると、サーバーとそのサーバー内のデータベースで SQL Database 更新プログラム V12 の機能が有効になり、元に戻すことができなくなります。サーバーを SQL Database 更新プログラム V12 にアップグレードするには、Basic、Standard、Premium のいずれかのサービス階層が必要です。サービス階層の詳細については、「[Upgrade SQL Database Web/Business Databases to New Service Tiers (SQL Database の Web/Business データベースを新しいサービス階層にアップグレードする)](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/)」をご覧ください。

> **重要**:SQL データベースの更新プログラムの V12 (プレビュー) では、Geo レプリケーションはサポートされていません。詳細については、「[Azure SQL データベースの更新プログラムの V12 (プレビュー) へのアップグレードの計画と準備をする](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade)」をご覧ください。


**[このサーバーをアップグレード]** オプションをクリックすると、開いているブレードに、検証プロセスに関するメッセージが表示されます。 

- 検証プロセスにより、データベースのサービス階層がチェックされ、Geo レプリケーションが有効になっているかどうかがチェックされます。検証が完了するとブレードに結果が表示されます。 
- 検証プロセスが完了すると、SQL Database 更新プログラム V12 へのアップグレード要件を満たすためのアクションが必要なデータベース名の一覧が表示されます。**SQL Database 更新プログラム V12 にアップグレードするには、これらの各データベースでアクションを実行する必要があります**。
- 各データベース名をクリックすると、新しいブレードに、現在の使用状況に基づいた推奨のサービス料金レベルが表示されます。また、異なる価格レベルを参照して、お使いの環境に最適な価格レベルを選択できます。Geo レプリケーションが設定されているすべてのデータベースを再構成して、レプリケーションを停止する必要があります。 
- 十分なデータが見つからない場合は、推奨の価格レベルが表示されませんのでご注意ください。 

| アクション | スクリーン ショット |
| :--- | :--- |
| 7.サーバーのアップグレード準備のためのアクションが完了したら、アップグレードするサーバー名を入力して **[OK]** をクリックします。 | ![Confirm the server name to upgrade][7] |
| 8.アップグレード処理が始まります。アップグレードには最大 24 時間かかることがあります。その間、このサーバー上のすべてのデータベースはオンラインのままですが、サーバーとデータベースの管理アクションは制限されます。プロセスが完了すると、「**有効**」ステータスがサーバー ブレードに表示されます。 | ![Confirms preview features are enabled][8] |
 

# 関連リンク  #

-  [SQL Database 更新プログラム V12 (プレビュー) の新機能](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [最新の SQL Database 更新プログラム V12 (プレビュー) へのアップグレードの計画と準備をする](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)


<!--Image references-->
[1]: ./media/sql-database-preview-upgrade/firstscreenportal.png
[2]: ./media/sql-database-preview-upgrade/browse.png
[3]: ./media/sql-database-preview-upgrade/sqlserver.png
[4]: ./media/sql-database-preview-upgrade/sqlserverlist.png
[5]: ./media/sql-database-preview-upgrade/latestprview.png
[6]: ./media/sql-database-preview-upgrade/upgrade.png
[7]: ./media/sql-database-preview-upgrade/typeservername.png
[8]: ./media/sql-database-preview-upgrade/enabled.png
<!--HONumber=47-->
