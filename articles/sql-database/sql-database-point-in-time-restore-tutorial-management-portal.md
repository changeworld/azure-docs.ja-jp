<properties 
   pageTitle="Azure ポータルでポイント イン タイム リストアを使用して Azure SQL データベースを復元する" 
   description="ポイント イン タイム リストア, Microsoft Azure SQL データベース, データベースの復元, データベースの回復, Azure 管理ポータル、Azure ポータル" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="02/24/2015"
   ms.author="elfish; v-romcal"/>

# Azure ポータルでポイント イン タイム リストアを使用して Azure SQL データベースを復元する

> [AZURE.SELECTOR]
- [ポイントイン タイム リストア - PowerShell](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-powershell/)
- [ポイント イン タイム リストア - REST API](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-rest/)

## 概要

このチュートリアルでは、[Azure ポータル](http://manage.windowsazure.com/) でポイント イン タイム リストアを使用して Azure SQL データベースを復元する方法について説明します。Azure SQL データベースには Basic、Standard、Premium サービス レベルでポイント イン タイム リストアのセルフ サービスをサポートするバックアップが組み込まれています。

ポイント イン タイム リストアでは新しいデータベースを作成します。サービスでは、復元ポイントに使用されるバックアップを基にサービス レベルを自動的にを選択します。別のデータベースを作成するためには、論理サーバーに利用可能なクォータがあることを確認します。クォータの増加を要請する場合は、[Azure サポート](http://azure.microsoft.com/support/options/) までお問い合わせください。

## 制限事項とセキュリティ

* ポイントイン タイム リストアは、Basic、Standard、Premium サービス レベルのすべてで有効です。

* バックアップの保有期間は Basic で 7 日間、Standard で 14 日間、Premium で 35 日間になります。
 
* サブスクリプションの管理者や共同管理者のみが復元要求を送信できます。

* サーバー レベルの主なログインは復元されたデータベースの所有者になります。

* Web と Business Edition サービス レベルではポイント イン タイム リストアをサポートしません。
 
	* Web もしくは Business Edition のデータベースがある場合、データベースのコピーを使ってトランザクションの一貫性があるデータベースのコピーを取得し、そのデータベースのコピーを Microsoft Azure ストレージ アカウントにエクスポートできます。詳細については、[方法:Use Database Copy (データベースのコピーを使用する) (Azure SQL データベース) ](http://msdn.microsoft.com/library/azure/ff951631.aspx) と 「[方法:Use the Import and Export Service in Azure SQL Database (Azure SQL データベースでインポート/エクスポート サービスを使用する)](http://msdn.microsoft.com/library/azure/hh335292.aspx)」をご覧ください。

	* Web と Business Edition は 2015 年 9 月に終了します。詳しくは「[Web and Business Edition Sunset FAQ (Web と Business Edition の終了に関する FAQ)](http://msdn.microsoft.com/library/azure/dn741330.aspx)」をご覧ください。

## 方法:Azure ポータルでポイント イン タイム リストアを使用して Azure SQL データベースを復元する

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-SQL-Database-Using-Point-in-Time-Restore/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

1. Microsoft アカウントを使用して、Azure ポータルにサインインします。

2. 左側のナビゲーションで **[SQL DATABASES (SQL データベース)]** をクリックします。
  
3. **[DATABASES (データベース)]** 一覧で、復元するデータベースをクリックします。 

4. コマンド バーで、ページの下部にある、**[RESTORE (復元)]** をクリックします。これで「**Specify restore settings (復元の設定を指定する)**」ダイアロ グボックスが起動します。

5. **DATABASE NAME (データベース名)]** を選択します。既定ではデータベース名が選択されていますが、変更できます。

6. データベースを復元するポイント イン タイムを選びます。ポイント イン タイムは、データベースの保有期間内にする必要があります。
	
7. チェック マークをクリックして復元要求を送信します。

復元が完了するまで時間がかかる場合があります。復元の状態を監視するには、コマンド バーで、ページの右下にあるステータス アイコンをクリックし、**[DETAILS (詳細)]** をクリックします。

## 次のステップ

詳細については、次のトピックを参照してください。

[Azure SQL データベースの継続性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL データベースのバックアップと復元](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL データベースのポイント イン タイム リストア (ブログ)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)
<!--HONumber=47-->
 