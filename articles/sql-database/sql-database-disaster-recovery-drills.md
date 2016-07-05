<properties 
   pageTitle="SQL Database の障害復旧訓練 | Microsoft Azure" 
   description="Azure SQL Database を使用して、ミッション クリティカルなビジネス アプリケーションがエラーと障害に対して回復力を保持するための障害復旧訓練を実行するためのガイダンスとベスト プラクティスについて説明します。" 
   services="sql-database" 
   documentationCenter="" 
   authors="mihaelablendea" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="06/16/2016"
   ms.author="mihaelab"/>

#障害復旧訓練の実行

復旧ワークフローのためのアプリケーションの対応状況の検証は、定期的に実行することをお勧めします。アプリケーションの動作、データの損失の影響やフェールオーバーによる中断を検証することをお勧めします。これは、ビジネス継続性の証明として、ほとんどの業界標準で必要条件ともなっています。

障害復旧の訓練は以下のもので構成されています。

- データ層の停止シミュレーション
- 復旧 
- 復旧後のアプリケーションの整合性の検証

[ビジネス継続性のためにアプリケーションをどのように設計](sql-database-business-continuity.md)したかによって、実行する訓練のワークフローは異なります。下記に、Azure SQL Database のコンテキストで障害復旧の訓練を行う際のベスト プラクティスについて説明します。

##地理リストア

障害復旧の訓練を実施する際のデータ損失の可能性を回避するために、運用環境のコピーから作成したテスト環境を使用して訓練を実行し、アプリケーションのフェールオーバーのワークフローの確認にもそれを使用することをお勧めします。
 
####障害のシミュレーション

障害をシミュレートするために、ソース データベースを削除したり、名前を変更したりすることができます。これによって、アプリケーションの接続エラーが発生します。

####復旧

- [こちら](sql-database-disaster-recovery.md)の説明に従って、データベースの地理リストアを別のサーバーに実行します。 
- アプリケーションの構成を変更して、復旧したデータベースに接続し、「[復旧後のデータベースの構成](sql-database-disaster-recovery.md)」のガイドに従って、復旧を完了します。

####検証

- 復旧後に、アプリケーションの整合性を検証して訓練を完了します (接続文字列、ログイン、基本的な機能のテスト、その他の標準的なアプリケーションのサインオフのプロシージャの検証など)。

##geo レプリケーション

geo レプリケーションを使用して保護されたデータベースの場合、訓練には、セカンダリ データベースへの計画されたフェールオーバーが含まれます。計画されたフェールオーバーでは、ロールが切り替わったときに、プライマリ データベースとセカンダリ データベースの同期を維持するようにします。計画外のフェールオーバーとは異なり、この操作ではデータは失われないため、訓練を運用環境で実行できます。

####障害のシミュレーション

障害をシミュレートするために、データベースに接続されている Web アプリケーションまたは仮想マシンを無効にできます。これにより、Web クライアントの接続エラーが発生します。

####復旧

- 障害復旧リージョンのアプリケーション構成が (完全にアクセス可能な新しいプライマリになる) 前のセカンダリをポイントしていることを確認します。 
- [計画されたフェールオーバー](sql-database-geo-replication-powershell.md#initiate-a-planned-failover)を実行して、セカンダリ データベースを新しいプライマリにします。
- 「[復旧後のデータベースの構成](sql-database-disaster-recovery.md)」のガイドに従って、復旧を完了します。

####検証

- 復旧後に、アプリケーションの整合性を検証して訓練を完了します (接続文字列、ログイン、基本的な機能のテスト、その他の標準的なアプリケーションのサインオフのプロシージャの検証など)。


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