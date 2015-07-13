<properties 
   pageTitle="Azure ポータルでポイント イン タイム リストアを使用して Azure SQL データベースを復元する" 
   description="ポイント イン タイム リストア、Microsoft Azure SQL Database、データベースの復元、データベースの復旧、Azure の管理ポータル、Azure ポータル" 
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
   ms.date="03/18/2015"
   ms.author="elfish; v-romcal"/>

# Azure ポータルでポイント イン タイム リストアを使用して Azure SQL データベースを復元する

> [AZURE.SELECTOR]
- [Point in Time Restore - PowerShell](sql-database-point-in-time-restore-tutorial-powershell.md)
- [Point in Time Restore - REST API](sql-database-point-in-time-restore-tutorial-rest.md) 

## 概要

このチュートリアルでは、[Azure ポータル](http://manage.windowsazure.com)でポイント イン タイム リストアを使用して Azure SQL データベースを復元する方法について説明します。Azure SQL データベースには Basic、Standard、Premium サービス レベルでポイント イン タイム リストアのセルフ サービスをサポートするバックアップが組み込まれています。

ポイント イン タイム リストアでは新しいデータベースを作成します。サービスは、復元ポイントに使用したバックアップに基づいて、サービス層を自動的に選択します。論理サーバーに別のデータベースを作成するための利用可能なクォータがあることを確認します。クォータを増やす場合は、[Azure サポート](http://azure.microsoft.com/support/options/)にお問い合わせください。

## 制限事項とセキュリティ

* ポイントイン タイム リストアは、Basic、Standard、Premium サービス レベルのすべてで有効です。

* バックアップの保有期間は Basic で 7 日間、Standard で 14 日間、Premium で 35 日間になります。
 
* サブスクリプションの管理者や共同管理者のみが復元要求を送信できます。

* サーバー レベルの主なログインは復元されたデータベースの所有者になります。

* Web と Business Edition サービス レベルではポイント イン タイム リストアをサポートしません。
 
	* Web もしくは Business Edition のデータベースがある場合、データベースのコピーを使ってトランザクションの一貫性があるデータベースのコピーを取得し、そのデータベースのコピーを Microsoft Azure ストレージ アカウントにエクスポートできます。詳細については、「[方法: データベース コピーを使用する (Azure SQL データベース)](http://msdn.microsoft.com/library/azure/ff951631.aspx)」と「[方法: Azure SQL Database でインポート/エクスポート サービスを使用する](http://msdn.microsoft.com/library/azure/hh335292.aspx)」を参照してください。

	* Web と Business Edition は 2015 年 9 月に終了します。詳細については、「[Web および Business エディションの終了に関する FAQ](http://msdn.microsoft.com/library/azure/dn741330.aspx)」を参照してください。

## 方法: Azure ポータルでポイント イン タイム リストアを使用して Azure SQL データベースを復元する

> [AZURE.VIDEO restore-a-sql-database-using-point-in-time-restore]

1. Microsoft アカウントを使用して、[Azure ポータル](http://manage.windowsazure.com)にサインインします。

2. 左側のナビゲーションで、**[SQL データベース]** をクリックします。
  
3. **[データベース]** 一覧で、復元するデータベースをクリックします。

4. コマンド バーで、ページの下部にある **[復元]** をクリックします。これで **[復元の設定を指定する]** ダイアログ ボックスが起動します。

5. **[データベース名]** を選択します。既定ではデータベース名が選択されていますが、変更できます。

6. データベースを復元するポイント イン タイムを選びます。ポイント イン タイムは、データベースの保有期間内にする必要があります。
	
7. チェック マークをクリックして復元要求を送信します。

復元が完了するまで時間がかかる場合があります。復元の状態を監視するには、コマンド バーで、ページの右下にあるステータス アイコンをクリックし、**[詳細]** をクリックします。

## 次のステップ

詳細については、次のトピックを参照してください。

[Azure SQL データベースの継続性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL Database のバックアップと復元](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL データベースのポイント イン タイム リストア (ブログ)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

<!---HONumber=62-->