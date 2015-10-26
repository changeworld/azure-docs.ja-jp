<properties 
   pageTitle="Azure ポータルで Geo-Restore を使用して Azure SQL データベースを回復する" 
   description="Geo-Restore, Microsoft Azure SQL データベース, データベースの復元, データベースの回復, Azure 管理ポータル, Azure ポータル" 
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
   ms.date="07/24/2015"
   ms.author="elfish; v-romcal"/>

# Azure ポータルで Geo-Restore を使用して Azure SQL データベースを回復する

> [AZURE.SELECTOR]
- [Geo-Restore - PowerShell](sql-database-geo-restore-tutorial-powershell.md)
- [Geo-Restore - REST API](sql-database-geo-restore-tutorial-rest.md)   

## 概要

このチュートリアルでは、[Azure ポータル](http://manage.windowsazure.com)で Geo-Restore を使用して Azure SQL データベースを回復する方法について説明します。Geo-Restore は、すべての Azure SQL データベース サービス レベル (Basic、Standard、Premium) に含まれる主要な災害回復保護です。

## 制限事項とセキュリティ

* Geo-Restore は、Basic、Standard、Premium サービス レベルで有効です。

* バックアップの保有期間は Basic で 7 日間、Standard で 14 日間、Premium で 35 日間になります。

* サブスクリプションの管理者や共同管理者のみが復元要求を送信できます。

* サーバー レベルの主なログインは復元されたデータベースの所有者になります。

* Web と Business Edition サービス レベルでは Geo-Restore はサポートされません。
 
	* Web もしくは Business Edition のデータベースがある場合、データベースのコピーを使ってトランザクションの一貫性があるデータベースのコピーを取得し、そのデータベースのコピーを Microsoft Azure ストレージ アカウントにエクスポートできます。詳細については、「[方法: データベース コピーを使用する (Azure SQL データベース)](http://msdn.microsoft.com/library/azure/ff951631.aspx)」と「[方法: Azure SQL Database でインポート/エクスポート サービスを使用する](http://msdn.microsoft.com/library/azure/hh335292.aspx)」を参照してください。

	* Web と Business Edition は 2015 年 9 月に終了します。詳細については、「[Web および Business エディションの終了に関する FAQ](http://msdn.microsoft.com/library/azure/dn741330.aspx)」を参照してください。

## 方法: Azure ポータルで Geo-Restore を使用して Azure SQL データベースを回復する

> [AZURE.VIDEO restore-a-sql-database-using-geo-restore]

1. Microsoft アカウントを使用して [Azure ポータル](http://manage.windowsazure.com)にサインインし、**[SQL データベース]** を選択します。

2. 左側のナビゲーションで、**[SQL データベース]** をクリックします。

3. ページの上部にある **[サーバー]** をクリックします。

4. **[サーバー]** 一覧で、**[低下]** サーバーをクリックします。

4. ページの上部にある、**[バックアップ]** をクリックします。

5. 復元するデータベースをクリックします。

6. コマンド バーで、ページの下部にある **[復元]** をクリックします。これで **[復元の設定を指定する]** ダイアログ ボックスが起動します。

7. データベースを復元する **[データベース名]** と** [対象サーバー]** を選択します。既定ではデータベース名が選択されていますが、変更できます。

9. チェック マークをクリックして復元要求を送信します。

復元が完了するまで時間がかかる場合があります。復元の状態を監視するには、コマンド バーで、ページの右下にあるステータス アイコンをクリックし、**[詳細]** をクリックします。

## 次のステップ

詳細については、次のトピックを参照してください。

[Azure ポータルでポイント イン タイム リストアを使用して Azure SQL データベースを復元する](sql-database-point-in-time-restore-tutorial-management-portal.md)

[Azure ポータルで削除した Azure SQL データベースを復元する](sql-database-restore-deleted-database-tutorial-management-portal.md)

[Azure SQL データベースの継続性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL Database のバックアップと復元](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL データベース Geo-Restore (ブログ)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

<!---HONumber=Oct15_HO3-->