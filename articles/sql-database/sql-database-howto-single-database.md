---
title: Azure SQL Database を構成する方法 - シングルトン | Microsoft Docs
description: Azure SQL Database (Single Database) を構成して管理する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: d34853220e423e73c6ca8cf7c76ba616b815b8bd
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439749"
---
# <a name="how-to-use-single-database"></a>Single Database を使用する方法

このセクションでは、お客様の Azure SQL データベース (Single Database) を管理して構成するのに役立つさまざまなガイド、スクリプト、説明をご紹介します。

## <a name="migrate"></a>移行

- [SQL Database に移行する](sql-database-cloud-migrate.md) – Managed Instance への移行に関して推奨される移行プロセスとツールについて確認します。
- [移行後に SQL データベースを管理](sql-database-manage-after-migration.md)する方法について確認する。

## <a name="configure-features"></a>機能の構成

- [トランザクション レプリケーションを構成](replication-to-sql-database.md)して、データベース間でお客様のデータをレプリケートする。
- [脅威検出を構成](sql-database-threat-detection.md)して、SQL インジェクションや疑わしい場所からのアクセスなど、疑わしいアクティビティが Azure SQL Database によって特定されるようにする。
- [動的データ マスクを構成](sql-database-dynamic-data-masking-get-started-portal.md)して、お客様の機密データを保護する。
- データベース対して[バックアップ保有期間を構成](sql-database-long-term-backup-retention-configure.md)し、お客様のバックアップを Azure Blob Storage に保持する。 別の方法として、[Azure コンテナーを使用したバックアップ保有期間の構成 (非推奨)](sql-database-long-term-backup-retention-configure-vault.md) があります。
- [geo レプリケーションを構成](sql-database-geo-replication-portal.md)して、お客様のデータベースのレプリカを別のリージョンに保持する。
- [geo レプリカのセキュリティを構成する](sql-database-geo-replication-security-config.md)。

## <a name="monitor-and-tune-your-database"></a>データベースの監視と調整

- お客様のワークロードのパフォーマンスが Azure SQL Database によって最適化されるよう、[自動チューニングを有効にする](sql-database-automatic-tuning-enable.md)。
- [自動チューニングの電子メール通知を有効](sql-database-automatic-tuning-email-notifications.md)にして、チューニング推奨事項に関する情報を取得する。
- [パフォーマンスに関する推奨事項を適用](sql-database-advisor-portal.md)してお客様のデータベースを最適化する。
- Azure SQL Database から通知を受け取るために[アラートを作成](sql-database-insights-alerts-portal.md)する。
- アプリケーションとデータベースの間で接続に関する問題が発生したことに気付いた場合に、[接続のトラブルシューティング](sql-database-troubleshoot-common-connection-issues.md)を行う。 また、[接続の問題には Resource Health](sql-database-resource-health.md) を使用することもできます。
- [ファイル領域を管理](sql-database-file-space-management.md)して、お客様のデータベースのストレージ使用量を監視する。

## <a name="query-distributed-data"></a>分散データにクエリを実行する

- 複数のデータベース全体で[垂直方向にパーティション分割されたデータにクエリを実行](sql-database-elastic-query-getting-started-vertical.md)する。
- [スケールアウトされたデータ層全体をレポートする](sql-database-elastic-query-horizontal-partitioning.md)。
- [スキーマが異なるテーブル全体に対してクエリを実行する](sql-database-elastic-query-vertical-partitioning.md)。

## <a name="elastic-database-jobs"></a>Elastic Database ジョブ

- PowerShell を使用して Elastic Database ジョブを[作成および管理](elastic-jobs-powershell.md)する。
- Transact-SQL を使用して Elastic Database ジョブを[作成および管理](elastic-jobs-tsql.md)する。
- [古いエラスティック ジョブから移行する](elastic-jobs-migrate.md)。

## <a name="database-sharding"></a>データベース シャーディング

- [エラスティック データベース クライアント ライブラリをアップグレードする](sql-database-elastic-scale-upgrade-client-library.md)。
- [シャード化されたアプリを作成する](sql-database-elastic-scale-get-started.md)。
- [水平方向にシャード化されたデータにクエリを実行する](sql-database-elastic-query-getting-started.md)。
- [マルチシャード クエリ](sql-database-elastic-scale-multishard-querying.md)を実行する。
- [シャード化されたデータを移動する](sql-database-elastic-scale-configure-deploy-split-and-merge.md)。
- データベース シャードの[セキュリティを構成](sql-database-elastic-scale-split-merge-security-configuration.md)する。
- データベース シャードの現在のセットに[シャードを追加](sql-database-elastic-scale-add-a-shard.md)する。
- [シャード マップに関する問題を修正する](sql-database-elastic-database-recovery-manager.md)。
- [シャード化された DB を移行する](sql-database-elastic-convert-to-use-elastic-tools.md)。
- [カウンターを作成する](sql-database-elastic-database-perf-counters.md)。
- [Entity Framework を使用](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)して、シャード化されたデータにクエリを実行する。
- [Dapper Framework を使用](sql-database-elastic-scale-working-with-dapper.md)して、シャード化されたデータにクエリを実行する。

## <a name="next-steps"></a>次の手順
- [Managed Instance の攻略ガイド](sql-database-howto-managed-instance.md)について詳しく確認する
