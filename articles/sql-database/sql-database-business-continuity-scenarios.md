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
	ms.date="07/16/2016"
	ms.author="carlrab"
   ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Azure SQL Database のビジネス継続性のシナリオ

この記事では、障害復旧のシナリオ、およびビジネス継続性のためのアプリケーション設計シナリオをいくつか紹介します。

## 障害からの回復

障害が発生した場合については、[障害からの Azure SQL Database の回復](sql-database-disaster-recovery.md)に関する記事で、次のビジネス継続性ソリューションのいずれかを使用して、障害から回復する方法を説明します。

- [アクティブ geo レプリケーションを選択するとき](sql-database-geo-replication-overview.md)
- [geo リストア](sql-database-recovery-using.backups.md#geo-restore)

障害からの回復に必要な特定の手順および期間は、選択したビジネス継続性ソリューションによって異なります。ただし、どのビジネス継続性ソリューションを選択したかにかかわらず、復旧開始のタイミング、各ビジネス継続性ソリューションのデータベース復旧の手順、復旧後のデータベースの構成方法、および障害からの復旧を完了するための、復旧後のアプリケーションの構成方法について知っておく必要があります。

## エラーからの回復

ユーザー エラー、またはその他の意図しないデータ変更エラーが発生した場合については、[エラーからの Azure SQL Database の復旧](sql-database-user-error-recovery.md)に関する記事で、次のビジネス継続性ソリューションのいずれかを使用して、エラーから回復する方法を説明します。

- [ポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)
- [削除済みデータベースの復元](sql-database-recovery-using-backups.md#deleted-database-restore)

障害からの回復に必要な特定の手順および期間は、選択したビジネス継続性ソリューションによって異なります。ただし、どのビジネス継続性ソリューションかにかかわらず、各ビジネス継続性ソリューションで、エラーから回復する方法を知っておく必要があります。

## 障害に備えた障害復旧訓練の実行

ビジネス継続性ソリューションを効果的にするには、復旧ワークフローのためのアプリケーションの対応状況の検証を、定期的に実行することをお勧めします。アプリケーションの動作、データの損失の影響やフェールオーバーによる中断を検証することをお勧めします。これは、ビジネス継続性の証明として、ほとんどの業界標準で必要条件ともなっています。

障害復旧の訓練は以下のもので構成されています。

- データ層の停止シミュレーション
- 復旧
- 復旧後のアプリケーションの整合性の検証

「[障害復旧訓練の実行](sql-database-disaster-recovery-drills.md)」で、次のビジネス継続性ソリューションのいずれかを使用して、障害復旧訓練を実行する方法について説明します。

- [アクティブ geo レプリケーションを選択するとき](sql-database-geo-replication-overview.md)
- [geo リストア](sql-database-recovery-using-backups.md#geo-restore)

## アクティブ geo レプリケーションを使用したクラウド アプリケーションのローリング アップグレードの管理

SQL Database でのクラウド アプリケーションのアップグレードは中断が発生する操作になるため、ビジネス継続性の計画および設計の一環として、このシナリオを加える必要があります。[アプリケーションのアップグレードの管理](sql-database-manage-application-rolling-upgrade.md)に関する記事では、SQL Database で[geo レプリケーション](sql-database-geo-replication-overview.md)を使用してクラウド アプリケーションのローリング アップグレードを有効にする方法について説明します。この記事では、アップグレード処理を編成する 2 種類の方法を確認し、方法ごとに利点とトレードオフを説明します。

## アクティブ geo レプリケーションを使用したクラウド障害復旧用アプリケーションの設計

[クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)に関する記事では、SQL Database で[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)を使用して、リージョンの障害や致命的な停止に対して回復性の高いデータベース アプリケーションを設計する方法について説明します。この記事では、アプリケーションのデプロイメント トポロジ、目標とするサービス レベル アグリーメント、トラフィック待機時間、コストを検討してから、一般的なアプリケーション パターンを紹介したうえで、それぞれの利点とトレードオフについて説明します。

## エラスティック データベース プールを使用した、アプリケーションの障害復旧戦略

[エラスティック プールを使用した、障害復旧戦略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)に関する記事では、[エラスティック データベース プール](sql-database-elastic-pool.md)を使用した障害復旧シナリオを紹介します。

## 次のステップ

- ビジネス継続性の概要については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
- Azure SQL Database 自動バックアップの詳細については、「[SQL Database automated backups (SQL Database 自動バックアップ)](sql-database-automated-backups.md)」を参照してください。
- ビジネス継続性の設計および復旧シナリオについては、[継続性のシナリオ](sql-database-business-continuity-scenarios.md)に関する記事を参照してください。
- 自動バックアップを使用して復旧する方法については、[サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。
- より迅速な復旧オプションについては、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください。
- 自動バックアップを使用したアーカイブについては、[データベースのコピー](sql-database-copy.md)に関する記事を参照してください。

<!---HONumber=AcomDC_0727_2016-->