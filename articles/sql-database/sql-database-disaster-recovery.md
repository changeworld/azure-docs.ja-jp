<properties 
   pageTitle="SQL Database の障害復旧 | Microsoft Azure" 
   description="Azure SQL Database のアクティブ geo レプリケーションと geo リストア機能を使用して、地域のデータ センターの停止や障害からデータベースを復旧する方法について説明します。" 
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
   ms.workload="data-management" 
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# Azure SQL Database を復元する、またはセカンダリにフェールオーバーする

Azure SQL Database は、障害から回復するために次の機能を備えています。

- [アクティブ geo レプリケーションを選択するとき](sql-database-geo-replication-overview.md)
- [geo リストア](sql-database-geo-restore.md)

災害とデータベースを復旧するための準備の詳細については、「[ビジネス継続性のための設計](sql-database-business-continuity-design.md)」をご覧ください。

## 復旧を開始するタイミング

復旧操作はアプリケーションに影響します。SQL 接続文字列を変更する必要があり、永続的にデータが失われる可能性があります。そのため、障害がアプリケーションの RTO よりも長くかかる可能性が高い場合にのみ行ってください。アプリケーションが実稼働環境にデプロイされている場合は、アプリケーションの健全性の定期的な監視を実行し、次のデータ ポイントを使用して、復旧が保証されていることをアサートします。

1.	接続の永続的な障害は、アプリケーション層からデータベースに渡ります。
2.	Azure ポータルは、幅広い影響のあるリージョンでのインシデントに関するアラートを示します。
3.	Azure SQL Database Server は機能低下としてマークされます。 

ダウンタイムに対するアプリケーションの許容度およびビジネス責任に応じて、次の回復オプションを検討することができます。

[Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) を使用して、最新の geo レプリケーションの復元ポイントを取得します。

## サービスの回復を待機する

Azure チームはできるだけ早くサービスが利用できるようになるように作業しますが、根本原因によっては数時間から数日かかることがあります。アプリケーションが長いダウンタイムを許容できる場合は、回復の完了を待つだけで済みます。この場合、ユーザーによる操作は必要ありません。現在のサービスの状態は、[Azure サービスの正常性ダッシュボード](https://azure.microsoft.com/status/)で確認できます。リージョンの回復後に、アプリケーションの可用性が復元されます。

## Geo レプリケートのセカンダリ データベースへのフェールオーバー

アプリケーションのダウンタイムによってビジネス責任が発生する場合は、アプリケーションで geo レプリケートされたデータベースを使用する必要があります。そうすれば、障害が発生した場合でも異なるリージョンでアプリケーションの可用性を迅速に復元できます。[geo レプリケーションを構成する](sql-database-geo-replication-portal.md)方法をご覧ください。

データベースの可用性を復元するには、サポートされているいずれかの方法を使用して、geo レプリケートされたセカンダリ データベースへのフェールオーバーを開始する必要があります。


geo レプリケートされたセカンダリ データベースへのフェールオーバーについては、次のいずれかを参照してください。

- [Azure ポータルを使用して Azure SQL Database の geo レプリケーションを構成する](sql-database-geo-replication-portal.md)
- [PowerShell を使用して Azure SQL Database の geo レプリケーションを構成する](sql-database-geo-replication-powershell.md)
- [Transact-SQL を使用して Azure SQL Database の geo レプリケーションを構成する](sql-database-geo-replication-transact-sql.md) 



## geo リストアを使用した復旧

アプリケーションのダウンタイムがビジネス責任にならない場合は、アプリケーション データベースを回復する方法として geo リストアを使用できます。geo リストアは、最新の geo 冗長バックアップからデータベースのコピーを作成します。

新しいリージョンへのデータベースの geo リストアについては、次のいずれかを参照してください。

- [Geo-Restore a database to a new region using Azure Portal (Azure ポータルを使用して新しいリージョンにデータベースを geo リストアする)](sql-database-geo-restore-portal.md)
- [Geo-Restore a database to a new region using PowerShell (PowerShell を使用して新しいリージョンにデータベースを geo リストアする)](sql-database-geo-restore-powershell.md) 


## 復旧後のデータベースの構成

geo レプリケーション フェールオーバーまたは geo リストアを使用して障害から復旧する場合は、通常のアプリケーション機能を再開できるように新しいデータベースへの接続が正しく構成されていることを確認する必要があります。復旧後のデータベースをすぐ運用できるようにするためのタスクのチェックリストを次に示します。

### 接続文字列を更新する

復旧後のデータベースは別のサーバーに存在するため、そのサーバーを示すようにアプリケーションの接続文字列を更新する必要があります。

接続文字列の変更の詳細については、[接続ライブラリ](sql-database-libraries.md)の適切な開発言語を参照してください。

### ファイアウォール規則を構成する

サーバーおよびデータベースで構成されているファイアウォール規則が、プライマリ サーバーとプライマリ データベースで構成されている規則と一致することを確認する必要があります。詳細については、「[方法: ファイアウォール設定を構成する (Azure SQL Database)](sql-database-configure-firewall-settings.md)」を参照してください。


### ログインとデータベース ユーザーを構成する

アプリケーションで使用するすべてのログインが、復旧されたデータベースをホストしているサーバー上に存在することを確認する必要があります。詳細については、[Security Configuration for Geo-Replication (geo レプリケーションのセキュリティ構成)](sql-database-geo-replication-security-config.md)に関するページを参照してください。

>[AZURE.NOTE] 障害復旧の訓練中に、サーバーのファイアウォール規則とログイン (およびそのアクセス許可) を構成してテストする必要があります。障害の間は、これらのサーバー レベル オブジェクトとその構成を使用できない場合があります。詳細については、「[障害復旧訓練の実行](sql-database-disaster-recovery-drills.md)」を参照してください。

### 製品利用統計情報アラートをセットアップする

既存のアラート ルールの設定を更新し、復旧されたデータベースおよび異なるサーバーにマップされるようにする必要があります。

データベースのアラート ルールの詳細については、「[アラート通知の受信](../azure-portal/insights-receive-alert-notifications.md)」および「[サービス正常性を追跡する](../azure-portal/insights-service-health.md)」を参照してください。

### 監査を有効にする

データベースにアクセスするために監査が必要な場合は、データベースの復旧後に監査を有効にする必要があります。クライアント アプリケーションで *.database.secure.windows.net パターンのセキュリティで保護された接続文字列を使用している場合は、監査が必要であることを表しています。詳細については、「[SQL Database 監査の使用](sql-database-auditing-get-started.md)」をご覧ください。


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