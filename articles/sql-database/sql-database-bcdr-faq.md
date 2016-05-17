<properties 
   pageTitle="SQL Database のビジネス継続性に関する FAQ" 
   description="Azure SQL Database のビジネス継続性と障害復旧における組み込み機能とオプション機能に関する一般的な質問と回答です。" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/25/2016"
   ms.author="elfish"/>

# ビジネス継続性に関する FAQ

## 1\.サービス レベルをダウングレード/アップグレードする場合、復元ポイントの保有期間はどうなるのでしょうか。
低いパフォーマンス レベルにダウングレードする場合、復元ポイントの保有期間は、現在のデータベースのパフォーマンス レベルにおける保有期間までにただちに短縮されます。

データベースのサービス レベルをアップグレードする場合、保有期間はデータベースがアップグレードした後からのみ延長されます。

たとえば、データベースを P1 から S3 へダウングレードすると、保有期間は 35 日間から 14 日間にすぐに変更され、14 日よりも前のすべての復元ポイントは使用できなくなります。その後再び P1 にアップグレードする場合、保有期間は 14 日間から開始し、最大 35 日間まで増加します。

## 2\.ドロップしたデータベースの保有期間を教えてください。 
保有期間は、データベースが存在していた期間のサービス レベルや、存在していた日数のいずれか短い方によって決まります。

## 3\.削除されたサーバーの復元方法を教えてください。

現時点では、削除されたサーバーを復元するためのサポートはありません。

## 4\.データベースの復元にかかる時間を教えてください。

データベースの復元にかかる時間は、データベースのサイズ、トランザクション ログの数、ネットワーク帯域幅など複数の要因によって異なります。データベースのほとんどは、12 時間以内に復元します。

## 5\.データベースの復元ポイントの保有期間は変更できますか。

いいえ、できません。

## 6\.データベースで利用できる復元ポイントはどのように見つけますか。

ユーザー エラーから復旧する場合、現在の時刻が利用できる最新の復元ポイントになります。利用できる最も古い復元ポイントを検索するには、[Get Database](https://msdn.microsoft.com/library/dn505708.aspx) (*RecoveryPeriodStartDate*) を使用して、最も古い復元ポイント (geo レプリケーションの復元ポイント以外) を取得します。

障害から復旧する場合は、[Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) を使用して、最新の geo レプリケーションの復元ポイントを取得します。

## 7\.サーバーでデータベースを一括復元する方法を教えてください。

一括復元を実行する機能は組み込まれていません。このタスクを達成する 1 つの方法として、たとえば、[Azure SQL Database: Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) スクリプトがあります。

## 8\.アクティブ geo レプリケーションを使用する場合のレプリケーションの遅延とは何ですか。

[sys.dm\_geo\_replication\_link\_status](https://msdn.microsoft.com/library/mt575504.aspx) 動的管理ビュー (DMV) を使用して、前回のレプリケーション時刻や前回のレプリケーション遅延など、レプリケーション リンクに関する情報を取得します。

<!---HONumber=AcomDC_0504_2016-->