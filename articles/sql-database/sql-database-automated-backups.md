<properties
   pageTitle="クラウドのビジネス継続性 - 組み込みバックアップ - SQL Database | Microsoft Azure"
   description="SQL Database 組み込みバックアップについて説明します。この機能を使用すると、Azure SQL Database を以前の時点にロールバックしたり、データベースを地理的リージョン内の新しいデータベースにコピーしたりすることができます (最大 35 日)。"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-features"
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# 概要: SQL Database 自動バックアップ

Azure SQL Database サービスは、自動バックアップ機能ですべてのデータベースを保護します。バックアップの保管期間は、Basic で 7 日間、Standard で 14 日間、Premium で 35 日間です。これらの自動バックアップを使用すると、誤ってデータを破損または削除した場合に、ポイントインタイム リストアを行ったり、削除したデータベースを復元したりすることができます。

データベースは自動的に取得されます。設定の選択や追加料金は必要ありません。この自動バックアップとポイントインタイム リストアは、原因にかかわらず、偶発的な破損や削除からデータベースを保護できるコスト ゼロ、管理作業ゼロの方法です。

## 自動バックアップのコスト

Microsoft Azure SQL Database は、バックアップ ストレージとして、プロビジョニングされている最大のデータベース ストレージの 200% までを追加のコストなしで提供します。たとえば、プロビジョニングされたデータベース サイズが 250 GB の Standard データベース インスタンスの場合、500 GB のバックアップ ストレージが追加のコストなしで提供されます。提供されるバックアップ ストレージをデータベースが超過している場合は、Azure サポートに連絡して保有期間を短縮するか、または読み取りアクセスのジオ (主要地域) 冗長ストレージ (RA-GRS) の標準料金をお支払いいただいてバックアップ ストレージを追加することができます。

## 自動バックアップの詳細

Basic、Standard、Premium のすべてのデータベースは、自動バックアップで保護されています。完全バックアップが毎週作成され、差分バックアップは毎日作成されます。ログのバックアップは 5 分間隔で実行されます。初回の完全バックアップは、データベースの作成直後にスケジュールされます。通常、完全バックアップにかかる時間は 30 分ですが、長くかかることもあります。データベースのサイズが大きな場合 (たとえば、データベースのコピーや、大きなデータベースからの復元で作成されたデータベースの場合)、初回の完全バックアップに長い時間がかかる可能性があります。初回の完全バックアップ以降のバックアップは、すべて自動的にスケジュールされ、バックグラウンドで自動的に管理されます。完全バックアップと差分バックアップの正確なタイミングは、全体的な負荷のバランスに応じてシステムが決定します。バックアップ ファイルは、障害復旧時に可用性を確保するために、読み取りアクセス権 (RA-GRS) を持つ geo 冗長ストレージ アカウントで格納されます。

## 次のステップ

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [削除されたデータベースの復元](sql-database-restore-deleted-database.md)
- [ポイントインタイム リストア](sql-database-point-in-time-restore.md)
- [geo リストア](sql-database-geo-restore.md)
- [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)
- [データベースのコピー](sql-database-copy.md)

## その他のリソース

- [障害からの回復](sql-database-disaster-recovery.md)
- [ユーザー エラーからの復旧](sql-database-user-error-recovery.md)
- [障害復旧訓練の実行](sql-database-disaster-recovery-drills.md)
- [クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->