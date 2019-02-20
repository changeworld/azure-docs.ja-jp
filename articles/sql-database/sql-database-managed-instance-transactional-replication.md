---
title: Azure SQL Database でのトランザクション レプリケーション | Microsoft Docs
description: Azure SQL Database で単一データベース、プールされたデータベース、インスタンス データベースを使用した SQL Server のトランザクション レプリケーションの使用について学習します。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: d0f9ea15b692d9aba2fde217805ea5e0ecfb4dfd
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993811"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Azure SQL Database での単一データベース、プールされたデータベース、インスタンス データベースを使用したトランザクション レプリケーション

トランザクション レプリケーションは、Azure SQL Database または SQL Server のテーブルからリモート データベースに配置されているテーブルにデータをレプリケートするための、Azure SQL Database、SQL Server の機能です。 この機能を使用すると、さまざまなデータベース内の複数のテーブルを同期させることができます。

## <a name="when-to-use-transactional-replication"></a>トランザクション レプリケーションを使用するタイミング

トランザクション レプリケーションは次のシナリオで役立ちます。

- データベース内の 1 つまたは複数のテーブルで行われた変更を発行して、これらの変更のためにサブスクライブした 1 つまたは複数の SQL Server または Azure SQL データベースに配布します。
- いくつかの分散データベースは同時状態を保持します。
- 変更を継続的に発行することによって、データベースを 1 つの SQL Server またはマネージド インスタンスから別のデータベースに移行します。

## <a name="overview"></a>概要

トランザクション レプリケーションの主要なコンポーネントは、次の図のとおりです。  

![SQL Database を使用したレプリケーション](media/replication-to-sql-database/replication-to-sql-database.png)

**パブリッシャー**は、ディストリビューターに更新プログラムを送信することで一部のテーブル (アーティクル) で加えられた変更を発行するインスタンスまたはサーバーです。 オンプレミスの SQL サーバーから Azure SQL データベースへの発行は、次のバージョンの SQL Server でサポートされます。

- SQL Server 2019 (プレビュー)
- SQL Server 2016 から SQL 2017
- SQL Server 2014 SP1 CU3 以上 (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 以上 (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Azure でのオブジェクトへの発行をサポートしていないその他のバージョンの SQL Server では、[データの再発行](https://docs.microsoft.com/sql/relational-databases/replication/republish-data)方法を利用して新しいバージョンの SQL Server にデータを移動することができます。 

**ディストリビューター**は、パブリッシャーからアーティクル内の変更を収集してサブスクライバーに配布するインスタンスまたはサーバーです。 ディストリビューターは、Azure SQL Database Managed Instance または SQL Server (パブリッシャーのバージョン以上の任意のバージョン) のいずれかになります。 

**サブスクライバー**は、パブリッシャーで加えられた変更を受信しているインスタンスまたはサーバーです。 Azure SQL Database または SQL Server データベースでは、サブスクライバーは単一データベース、プールされたデータベース、インスタンス データベースのいずれかになります。 単一データベースまたはプールされたデータベース上のサブスクライバーは、プッシュ サブスクライバーとして構成する必要があります。 

| Role | 単一データベースとプールされたデータベース | インスタンス データベース |
| :----| :------------- | :--------------- |
| **発行元** | いいえ  | はい | 
| **ディストリビューター** | いいえ  | はい|
| **プル サブスクライバー** | いいえ  | はい|
| **プッシュ サブスクライバー**| はい | はい|
| &nbsp; | &nbsp; | &nbsp; |

さまざまな[レプリケーションの種類](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication?view=sql-server-2017)があります。


| レプリケーション | 単一データベースとプールされたデータベース | インスタンス データベース|
| :----| :------------- | :--------------- |
| [**トランザクション**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | はい (サブスクライバーとしてのみ) | はい | 
| [**スナップショット**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | はい (サブスクライバーとしてのみ) | はい|
| [**マージ レプリケーション**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | いいえ  | いいえ |
| [**ピア ツー ピア**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | いいえ  | いいえ |
| **一方向** | はい | はい|
| [**双方向**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | いいえ  | はい|
| [**更新可能なサブスクリプション**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | いいえ  | いいえ |
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - 以前のバージョンを使用してレプリケーションを構成しようとすると、エラー番号 MSSQL_REPL20084 (プロセスはサブスクライバーに接続できませんでした) および MSSQ_REPL40532 (ログインによって要求されたサーバー \<name> を開くことができません。 ログインに失敗しました) のエラーが発生する可能性があります。
  > - Azure SQL Database のすべての機能を使用するには、最新バージョンの [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) および [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) を使用する必要があります。

## <a name="requirements"></a>必要条件

- 接続では、レプリケーション参加者間で SQL 認証を使用します。 
- レプリケーションで使用される作業ディレクトリの Azure ストレージ アカウント共有。 
- Azure ファイル共有にアクセスするために、Managed Instance サブネットのセキュリティ規則でポート 445 (TCP 送信) を開く必要があります。 
- ポート 1433 (TCP 送信) は、パブリッシャー/ディストリビューターがマネージド インスタンスにあり、サブスクライバーがオンプレミスにある場合は、開かれている必要があります。 

## <a name="common-configurations"></a>一般的な構成

一般に、パブリッシャーとディストリビューターは、クラウドかオンプレミスのいずれかに存在する必要があります。 次の構成がサポートされています。 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>マネージド インスタンス上のパブリッシャーとローカル ディストリビューター

![パブリッシャーおよびディストリビューターとしての 1 つのインスタンス ](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

パブリッシャーとディストリビューターは、1 つのマネージド インスタンス内に構成され、その他のマネージド インスタンス、単一データベース、プールされたデータベース、またはオンプレミスの SQL Server に変更が配布されます。 この構成では、パブリッシャー/ディストリビューターのマネージド インスタンスを [Geo レプリケーションと自動フェールオーバー グループ](sql-database-auto-failover-group.md)に対して構成することはできません。

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>マネージド インスタンス上のパブリッシャーとリモート ディストリビューター

この構成では、1 つのマネージド インスタンスにより多数のソースのマネージ インスタンスにサービスが提供され、マネージド インスタンス、単一データベース、プールされたデータベース、または SQL Server 上の 1 つまたは複数のターゲットに対する変更を配布できる、もう 1 つのマネージド インスタンス上に配置されたディストリビューターに、変更が発行されます。

![パブリッシャーおよびディストリビューターの独立したインスタンス](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

2 つのマネージド インスタンス上にパブリッシャーとディストリビューターが構成されています。 この構成では次のようになります。

- 2 つのマネージド インスタンスが同じ vNet 上にあります。
- 2 つのマネージド インスタンスが同じ場所にあります。
- 発行および配布されたデータベースをホストしているマネージド インスタンスは、[自動フェールオーバー グループを使用して Geo レプリケーション](sql-database-auto-failover-group.md)することはできません。

### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>オンプレミスのパブリッシャーおよびディストリビューターと、単一データベース、プールされたデータベース、インスタンス データベース上のサブスクライバー 

![サブスクライバーとしての Azure SQL DB](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
この構成では、Azure SQL Database (単一データベース、プールされたデータベース、インスタンス データベース) がサブスクライバーです。 この構成では、オンプレミスから Azure への移行がサポートされます。 サブスクライバーが単一データベースまたはプールされたデータベース上にある場合は、プッシュ モードにする必要があります。  

## <a name="next-steps"></a>次の手順

1. [マネージド インスタンスのトランザクション レプリケーションを構成します](replication-with-sql-database-managed-instance.md#configure-publishing-and-distribution-example)。 
1. [パブリケーションを作成します](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)。
1. Azure SQL Database サーバー名をサブスクライバーとして (`N'azuresqldbdns.database.windows.net` など)、Azure SQL Database 名を宛先データベースとして (**AdventureWorks** など) 使用して、[プッシュ サブスクリプションを作成します](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription)。 )


## <a name="see-also"></a>関連項目  

- [SQL Database へのレプリケーション](replication-to-sql-database.md)
- [マネージド インスタンスへのレプリケーション](replication-with-sql-database-managed-instance.md)
- [パブリケーションを作成する](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [プッシュ サブスクリプションを作成する](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [レプリケーションの種類](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [監視 (レプリケーション)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [サブスクリプションを初期化する](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
