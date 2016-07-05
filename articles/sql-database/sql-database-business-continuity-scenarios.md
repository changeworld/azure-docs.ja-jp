<properties
	pageTitle="Azure SQL Database のビジネス継続性のシナリオ | Microsoft Azure"
	description="Azure SQL Database のビジネス継続性のシナリオ"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/16/2016"
	ms.author="carlrab"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Azure SQL Database のビジネス継続性のシナリオ

> [AZURE.SELECTOR]
- [ビジネス継続性](sql-database-business-continuity.md)
- [シナリオ](sql-database-business-continuity-scenarios.md)
- [ポイントインタイム リストア](sql-database-point-in-time-retore.md)
- [削除済みデータベースの復元](sql-database-restore-deleted-database.md)
- [geo リストア](sql-database-geo-restore.md)
- [アクティブ geo レプリケーションを選択するとき](sql-database-geo-replication)

この記事では、いくつかの Azure SQL Database ビジネス継続性シナリオを紹介します。

## 障害からの回復

「[Azure SQL Database を復元する、またはセカンダリにフェールオーバーする](sql-database-disaster-recovery.md)」では、次の機能を使用して障害から回復する方法について説明します。

- [アクティブ geo レプリケーションを選択するとき](sql-database-geo-replication-overview.md)
- [geo リストア](sql-database-geo-restore.md)

この記事では、回復を開始する状況、各機能を使用して回復する方法、回復後にデータベースを構成する方法、および回復後にアプリケーションを構成する方法について説明します。

## ユーザー エラーからの復旧

「[ユーザー エラーからの Azure SQL Database の復旧](sql-database-user-error-recovery.md)」では、 次の機能を使用して、ユーザー エラーや予期しないデータ変更から回復する方法について説明します。

- [ポイントインタイム リストア](sql-database-point-in-time-restore.md) 
- [削除済みデータベースの復元](sql-database-restore-deleted-database.md)

## 障害復旧の訓練の実行

「[障害復旧訓練の実行](sql-database-disaster-recovery-drills.md)」では、次の機能を使用して、障害復旧の訓練を実行する方法について説明します。

- [アクティブ geo レプリケーションを選択するとき](sql-database-geo-replication-overview.md)
- [geo リストア](sql-database-geo-restore.md)

復旧ワークフローのためのアプリケーションの対応状況の検証は、定期的に実行することをお勧めします。アプリケーションの動作、データの損失の影響やフェールオーバーによる中断を検証することをお勧めします。これは、ビジネス継続性の証明として、ほとんどの業界標準で必要条件ともなっています。

障害復旧の訓練は以下のもので構成されています。

- データ層の停止シミュレーション
- 復旧 
- 復旧後のアプリケーションの整合性の検証

## 障害復旧後のセキュリティ管理

「[障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」では、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)を構成し、制御するための認証要件と、セカンダリ データベースへのユーザー アクセスを設定するために必要な手順について説明します。また、[geo リストア](sql-database-geo-restore.md)使用後に復旧されたデータベースへのアクセスを有効にする方法についても説明します。

## アクティブ geo レプリケーションを使用したクラウド アプリケーションのローリング アップグレードの管理

「[SQL Database アクティブ geo レプリケーションを使用したクラウド アプリケーションのローリング アップグレードの管理 ](sql-database-manage-application-rolling-upgrade.md)」では、SQL Database の [geo レプリケーション](sql-database-geo-replication-overview.md)を使用して、クラウド アプリケーションのローリング アップグレードを有効にする方法について説明します。アップグレードは中断を伴う作業であるため、ビジネス継続性の計画と設計の一部として組み込む必要があります。この記事では、アップグレード処理を編成する 2 種類の方法を確認し、方法ごとに利点とトレードオフを説明します。

## アクティブ geo レプリケーションを使用したクラウド障害復旧用アプリケーションの設計

「[SQL Database のアクティブ geo レプリケーションを使用したクラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)」では、SQL Database の[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)を使用して、リージョンの障害や致命的な停止に対して回復性の高いデータベース アプリケーションを設計する方法について説明します。この記事では、アプリケーションのデプロイメント トポロジ、目標とするサービス レベル アグリーメント、トラフィック待機時間、コストを検討してから、一般的なアプリケーション パターンを紹介したうえで、それぞれの利点とトレードオフについて説明します。

## エラスティック データベース プールを使用した、アプリケーションの障害復旧戦略

「[SQL Database エラスティック プールを使用した、アプリケーションの障害復旧戦略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)」では、[エラスティック データベース プール](sql-database-elastic-pool.md)を使用した障害復旧シナリオを紹介します。

## 次のステップ

- 障害復旧のためのアクティブ geo レプリケーションの使用および構成の方法については、「[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)」をご覧ください。
- 障害復旧のための geo リストアの使用方法については、「[geo リストア](sql-database-geo-restore.md)」をご覧ください。

## その他のリソース

- [概要: SQL Database を使用したクラウド ビジネス継続性とデータベース障害復旧](sql-database-business-continuity.md)
- [Overview: SQL Database Point-in-Time Restore (概要: SQL Database のポイントインタイム リストア)](sql-database-point-in-time-restore.md)
- [geo リストア](sql-database-geo-restore.md)
- [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)
- [クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md)
- [geo レプリケーションのセキュリティ構成](sql-database-geo-replication-security-config.md)
- [SQL Database BCDR の FAQ](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0622_2016-->