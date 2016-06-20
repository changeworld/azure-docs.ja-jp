<properties
   pageTitle="クラウド ビジネス継続性 - geo リストア | Microsoft Azure"
   description="Azure SQL Database がどのようにクラウド ビジネス継続性とデータベース復旧をサポートし、ミッション クリティカルなクラウド アプリケーションの実行を維持できるようにするかについて説明します。"
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/10/2016"
   ms.author="sstein"/>

# 概要: SQL Database の geo リストア

geo リストアを使用すると、最新の日次バックアップから SQL データベースを復元できます。geo リストアは、追加コストなしにすべてのサービス階層で自動的に有効になります。geo リストアではソースとして geo 冗長バックアップが使用され、障害によってデータベースまたはデータセンターにアクセスできない場合でも、geo リストアを使用してデータベースを復旧できます。

geo リストアを開始すると新しい SQL データベースが作成されます。このデータベースは、任意の Azure リージョン内のサーバーに作成できます。


|タスク (ポータル) | PowerShell | REST () |
|:--|:--|:--|
| [異なるリージョンのコピーから SQL Database を復旧する](sql-database-geo-restore-portal.md) | [PowerShell](sql-database-geo-restore-powershell.md) | [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |



geo リストアは、データベースがホストされているリージョンでのインシデントのためにデータベースが利用できない場合にも既定の復旧オプションを提供します。[ポイントインタイム リストア](sql-database-point-in-time-restore.md)と同様に、geo リストアも geo 冗長 Azure Storage のデータベースのバックアップに依存します。geo レプリケーション バックアップ コピーからリストアするため、プライマリ リージョンにおけるストレージの障害に対する回復力があります。



## geo リストアの詳細

geo リストアはポイントインタイム リストアと同じテクノロジを使用しますが、1 つの重要な相違点があります。geo リストアでは、geo レプリケートされた Blob Storage (RA-GRS) の最新の日次バックアップのコピーからデータベースが復元されます。各アクティブ データベースについて、サービスは、週次の完全バックアップ、複数の日次差分バックアップ、および 5 分ごとに保存されるトランザクション ログを含むバックアップ チェーンを保持しています。これらの BLOB は geo レプリケートされ、これにより、プライマリ リージョンで大規模な障害が発生した後でも日次バックアップを利用できることが保証されます。次の図は、ストレージ コンテナーにコピーされる週次および日次バックアップの geo レプリケーションを示したものです。

![geo リストア](./media/sql-database-geo-restore/geo-restore-1.png)


リージョン内の大規模なインシデントにより、データベース アプリケーションが使用できなくなった場合、geo リストアを使用して、最新のバックアップから他の任意のリージョン内のサーバーにデータベースを復元できます。すべてのバックアップは geo レプリケートされ、バックアップが作成されてから異なるリージョンの Azure BLOB に geo レプリケートされるまでの間には、遅延がある場合があります。この遅延は最大 1 時間なので、障害発生時には、最大 1 時間分のデータが損失する可能性があります。つまり、RPO は最大 1 時間です。次に示すのは、最後の日次バックアップからのデータベースの復元です。


![geo リストア](./media/sql-database-geo-restore/geo-restore-2.png)



## geo リストアの復旧時間

復旧時間は、データベースのサイズ、データベースのパフォーマンス レベル、ターゲット リージョンで処理されている同時復元要求の数など、いくつかの要因によって影響を受けます。あるリージョンでの停止が長引いた場合、多数の geo リストア要求が他のリージョンによって処理されることがあります。多数の要求がある場合、そのリージョンでのデータベースの復旧時間が長くなる可能性があります。


## 概要

geo リストアはすべてのサービス階層で使用できますが、RPO と推定復旧時間 (ERT) が最も長い SQL Database で使用できる障害復旧ソリューションが最も基本です。最大サイズが 2 GB の Basic データベースでは、geo リストアは ERT が 12 時間である妥当な障害復旧ソリューションを提供します。さらに大きい Standard または Premium データベースでは、大幅に短い復旧間隔が必要な場合、またはデータ損失の可能性を下げる場合は、アクティブ geo レプリケーションの使用を検討する必要があります。アクティブ geo レプリケーションでは、継続的にレプリケートされるセカンダリへのフェールオーバーを開始することだけが必要なので、RPO と ERT が大きく短縮されます。詳細については、「[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)」を参照してください。

## その他のリソース

- [SQL Database BCDR の FAQ](sql-database-bcdr-faq.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [Overview: SQL Database Point-in-Time Restore (概要: SQL Database のポイントインタイム リストア)](sql-database-point-in-time-restore.md)
- [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)
- [クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md)

<!---HONumber=AcomDC_0608_2016-->