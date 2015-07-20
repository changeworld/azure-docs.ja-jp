<properties
   pageTitle="復旧された Azure SQL データベースの最終処理を行う"
   description="ポイント イン タイム リストア、Microsoft Azure SQL Database、データベースの復元、データベースの復旧、Azure の管理ポータル、Azure ポータル"
   services="sql-database"
   documentationCenter=""
   authors="elfisher"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery"
   ms.date="04/13/2015"
   ms.author="elfish"/>

# 復旧された Azure SQL データベースの最終処理を行う

> [AZURE.SELECTOR]
- [Point in Time Restore - portal](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Point in Time Restore - PowerShell](sql-database-point-in-time-restore-tutorial-powershell.md)
- [Point in Time Restore - REST API](sql-database-point-in-time-restore-tutorial-rest.md)
- [Geo-Restore - portal](sql-database-geo-restore-tutorial-management-portal.md)
- [Geo-Restore - PowerShell](sql-database-geo-restore-tutorial-powershell.md)
- [Geo-Restore - REST API](sql-database-geo-restore-tutorial-rest.md)

## 概要

この記事では、新しく復旧された Azure SQL データベースを運用環境に戻す前に実行する必要のあるタスクのチェック リストを示します。このチェック リストは、geo レプリケーション フェールオーバー、削除したデータベースの復元、ポイント イン タイム リストア、または地理リストア操作で復旧されたデータベースに適用されます。

## 接続文字列を更新する

アプリケーションの接続文字列が新しく復旧されたデータベースを指し示していることを確認します。次のどちらかの条件に該当する場合は、接続文字列を更新します。

  + 復旧されたデータベースがソース データベース名と異なる名前を使用している。
  + 復旧されたデータベースがソース データベースと異なるサーバー上にある。

接続文字列を変更する方法の詳細については、「[プログラムで Azure SQL Database に接続するためのガイドライン](https://msdn.microsoft.com/library/azure/ee336282.aspx)」と [SQL Database への接続の中心となる推奨事項](sql-database-connect-central-recommendations.md)に関するページを参照してください。
 
## ファイアウォール ルールを変更する
サーバー レベルとデータベース レベルでファイアウォール ルールを確認し、クライアント コンピューターまたは Azure からサーバーと新しく復旧されたデータベースへの接続が有効になっていることを確認します。詳細については、「[Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx)」と「[方法: ファイアウォール設定を構成する (Azure SQL Database)](https://msdn.microsoft.com/library/azure/jj553530.aspx)」を参照してください。

## サーバー ログインとデータベース ユーザーを確認する

アプリケーションで使用するすべてのログインが、復旧されたデータベースをホストしているサーバー上に存在することを確認します。不足しているログインを再作成し、復旧されたデータベースに対する適切なアクセス許可を与えます。詳細については、[Azure SQL Database におけるデータベースとログインの管理](https://msdn.microsoft.com/library/azure/ee336235.aspx)に関するページを参照してください。

復旧されたデータベースの各データベース ユーザーに有効なサーバー ログインが関連付けられているかどうかを確認します。ユーザーを有効なサーバー ログインにマップするには、ALTER USER ステートメントを使用します。詳細については、「[ALTER USER](http://go.microsoft.com/fwlink/?LinkId=397486)」を参照してください。


## 製品利用統計情報アラートをセットアップする

既存のアラート ルール設定が復旧されたデータベースにマップされているかどうかを確認します。次のどちらかの条件に該当する場合は、設定を更新します。

  + 復旧されたデータベースがソース データベース名と異なる名前を使用している。
  + 復旧されたデータベースがソース データベースと異なるサーバー上にある。

データベースのアラート ルールの詳細については、「[方法: Azure でアラート通知を受け取り、アラート ルールを管理する](https://msdn.microsoft.com/library/azure/dn306638.aspx)」を参照してください。


## 監査を有効にする

データベースにアクセスするために監査が必要な場合は、データベースの復旧後に監査を有効にする必要があります。クライアント アプリケーションで *.database.secure.windows.net パターンのセキュリティで保護された接続文字列を使用している場合は、監査が必要であることを表しています。詳細については、[SQL Database 監査の使用](sql-database-auditing-get-started.md)に関するページを参照してください。
 

<!---HONumber=July15_HO2-->