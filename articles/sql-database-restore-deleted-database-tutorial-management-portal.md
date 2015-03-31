<properties 
   pageTitle="Azure ポータルで削除した Azure SQL データベースを復元する" 
   description="Microsoft Azure SQL データベース, 削除したデータベースの復元, 削除したデータベースの回復, Azure 管理ポータル, Azure ポータル" 
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

# Azure ポータルで削除した Azure SQL データベースを復元する

> [AZURE.SELECTOR]
- [削除済みデータベースの復元 - PowerShell](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-powershell/)
- [削除済みデータベースの復元 - REST API](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-rest/)

## 概要

このチュートリアルでは、[Azure ポータル](http://manage.windowsazure.com/) で削除した Azure SQL データベースを復元する方法について説明します。保有期間中に削除したデータベースを削除した時点に復元ができます。保有期間は、データベースのサービス レベルによって決まります。

削除した Azure SQL データベースを復元すると新しいデータベースが作成されます。サービスでは、復元ポイントに使用されるバックアップを基にサービス レベルを自動的にを選択します。別のデータベースを作成するためには、論理サーバーに利用可能なクォータがあることを確認します。クォータの増加を要請する場合は、[Azure サポート](http://azure.microsoft.com/support/options/) までお問い合わせください。

## 制限事項とセキュリティ

* 削除した Azure SQL データベースの復元は Basic、Standard、Premium のすべてのサービス レベルで有効です。 

* バックアップの保有期間は Basic で 7 日間、Standard で 14 日間、Premium で 35 日間になります。

* サブスクリプションの管理者や共同管理者のみが復元要求を送信できます。

* サーバー レベルの主なログインは復元されたデータベースの所有者になります。
 
* Web と Business Edition サービス レベルでは削除した SQL データベースの復元をサポートしません。
 
	* Web もしくは Business Edition のデータベースがある場合、データベースのコピーを使ってトランザクションの一貫性があるデータベースのコピーを取得し、そのデータベースのコピーを Microsoft Azure ストレージ アカウントにエクスポートできます。詳細については、[方法:Use Database Copy (データベースのコピーを使用する) (Azure SQL データベース) ](http://msdn.microsoft.com/library/azure/ff951631.aspx) と 「[方法:Use the Import and Export Service in Azure SQL Database (Azure SQL データベースでインポート/エクスポート サービスを使用する)](http://msdn.microsoft.com/library/azure/hh335292.aspx)」をご覧ください。
	* Web と Business Edition は 2015 年 9 月に終了します。詳しくは「[Web and Business Edition Sunset FAQ (Web と Business Edition の終了に関する FAQ)](http://msdn.microsoft.com/library/azure/dn741330.aspx)」をご覧ください。

## 方法:Azure ポータルで削除した Azure SQL データベースを復元する

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-Deleted-SQL-Database/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

1. Microsoft アカウントを使用して、Azure ポータルにサインインします。

2. 左側のナビゲーションで **[SQL DATABASES (SQL データベース)]** をクリックします。

3. ページの上部にある **[DELETED DATABASES (削除したデータベース)]** をクリックします。**RESTORABLE DELETED DATABASES (復元可能な削除済みデータベース)** の一覧が表示します。 

4. 復元するデータベースをクリックします。

6. コマンド バーで、ページの下部にある、**[Restore (復元)]** をクリックします。これで「**Specify restore settings (復元の設定を指定する)**」ダイアロ グボックスが起動します。 

7. **DATABASE NAME (データベース名)]** を選択します。既定ではデータベース名が選択されていますが、変更できます。   

	* 削除したデータベースは、削除したサーバーからのみ、削除した時点のポイント イン タイムで復元できます。   

8. チェック マークをクリックして復元要求を送信します。

復元が完了するまで時間がかかる場合があります。復元の状態を監視するには、コマンド バーで、ページの右下にあるステータス アイコンをクリックし、**[DETAILS (詳細)]** をクリックします。

## 次のステップ

詳細については、次のトピックを参照してください。 

[Azure SQL データベースの継続性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL データベースのバックアップと復元](http://msdn.microsoft.com/library/azure/jj650016.aspx)
<!--HONumber=47-->
