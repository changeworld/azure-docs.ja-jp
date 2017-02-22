---        
title: "Azure SQL Database チュートリアルの確認 | Microsoft Docs"
description: "SQL Database の機能について説明します。"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 04c0fd7f-d260-4e43-a4f0-41cdcd5e3786
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 60bcd02d24e2084b9020ce56ef6a9f8268c6b1b5
ms.openlocfilehash: 23d4c00f9e4c719e2124ad3c8733269712a796f0

        
---
 
# <a name="explore-azure-sql-database-tutorials"></a>Azure SQL Database チュートリアルの確認
次の表のリンクで、記載されている各機能領域の概要とその領域のクイック スタート チュートリアルを提供します。 実世界のシナリオに基づく、完全なソリューションで SQL Database の利用方法を実演するソリューション中心のクイック スタートについては、「 [Azure SQL Database Solution Quick Starts](sql-database-solution-quick-starts.md)」 (Azure SQL Database ソリューション クイック スタート) を参照してください。

## <a name="create-servers-databases-and-server-level-firewall-rules"></a>サーバー、データベース、サーバーレベルのファイアウォール規則を作成する
以下のチュートリアルでは、サーバー、データベース、サーバーレベルのファイアウォール規則を作成します。また、サーバーやデータベースへの接続とクエリの実行について学習します。

| チュートリアル | 説明 |
| --- | --- | 
| [クイック スタート チュートリアル: 初めての Azure SQL Database](sql-database-get-started.md) | このクイック スタート チュートリアルを完了すると、サンプル データベースと空のデータベースが Azure リソース グループで実行され、論理サーバーに接続された状態になります。 また、2 つのサーバーレベルのファイアウォール規則もあり、サーバーレベルのプリンシパルが指定の&2; つの IP アドレスからサーバーにログインできるように構成されます。 最後に、Azure Portal でデータベースをクエリし、SQL Server Management Studio を使用して接続、クエリする方法を学習します。 |
| [チュートリアル: PowerShell を使用した Azure SQL データベースのプロビジョニングとアクセス](sql-database-get-started-powershell.md) | このチュートリアルを完了すると、サンプル データベースと空のデータベースが Azure リソース グループで実行され、論理サーバーに接続された状態になります。 また、サーバーレベルのプリンシパルが指定の IP アドレス (または IP アドレス範囲) からサーバーにログインできるようにサーバーレベルのファイアウォール規則が構成されます。 |
| [Use C# to create a SQL database with the SQL Database Library for .NET (C# を使用して SQL Database Library for .NET で SQL データベースを作成する)](sql-database-get-started-csharp.md)| このチュートリアルでは、C# を使用して、SQL Database サーバー、ファイアウォール規則、SQL データベースを作成します。 また、Active Directory (AD) アプリケーションと、C# アプリの認証に必要なサービス プリンシパルも作成します。 |
|  | |

## <a name="backups-long-term-retention-and-database-recovery"></a>バックアップ、長期リテンション期間、データベースの復旧
次のチュートリアルでは、[データベース バックアップ](sql-database-automated-backups.md)の使用、[長期のバックアップ リテンション期間](sql-database-long-term-retention.md)、[バックアップを使用したデータベースの復旧](sql-database-recovery-using-backups.md)について学習します。

| チュートリアル | Description |
| --- | --- | 
| [Azure Portal を使用したバックアップおよび復元](sql-database-get-started-backup-recovery.md) | このチュートリアルでは、Azure Portal を使用して、バックアップの表示、特定の時点への復旧、長期的なバックアップ リテンション期間の構成、Azure Recovery Services コンテナー内のバックアップからの復旧を実行する方法を学習します
| [PowerShell を使用したバックアップおよび復元](sql-database-get-started-backup-recovery-powershell.md) | このチュートリアルでは、PowerShell を使用して、バックアップの表示、特定の時点への復旧、長期的なバックアップ リテンション期間の構成、Azure Recovery Services コンテナー内のバックアップからの復旧を実行する方法を学習します
|  | |

## <a name="sharded-databases"></a>シャード データベース
次のチュートリアルでは、[シャード マップ マネージャーでデータベースをスケールアウトする](sql-database-elastic-scale-shard-map-management.md)方法について学習します。

| チュートリアル | Description |
| --- | --- | 
| [シャード化されたアプリケーションを作成する](sql-database-elastic-scale-get-started.md) |このチュートリアルでは、単純なシャーディング アプリケーションを使用して、エラスティック データベース ツールの機能を使用する方法を学習します。 |
| [split-merge サービスのデプロイ](sql-database-elastic-scale-configure-deploy-split-and-merge.md) |このチュートリアルでは、シャード化されたデータベース間でデータを移動する方法を学習します。 |
|  | |

## <a name="elastic-database-jobs"></a>エラスティック データベース ジョブ
以下のチュートリアルでは、[エラスティック データベース ジョブ](sql-database-elastic-jobs-overview.md)の使用について学習します。

| チュートリアル | 説明 |
| --- | --- | 
| [Azure SQL Database エラスティック ジョブの概要](sql-database-elastic-jobs-getting-started.md) |このチュートリアルでは、関連データベース グループの管理ジョブを作成および管理する方法を学習します。 |
|  | |

## <a name="elastic-queries"></a>エラスティック クエリ
以下のチュートリアルでは、[エラスティック クエリ](sql-database-elastic-query-overview.md)の実行について学習します。

| チュートリアル | Description |
| --- | --- | 
| [エラスティック クエリの作成](sql-database-elastic-query-getting-started-vertical.md) | このチュートリアルでは、単一の接続ポイントを使用して複数のデータベースにまたがる T-SQL クエリを実行する方法を学習します |
| [スケールアウト クラウド データベース全体のレポート](sql-database-elastic-query-getting-started.md) |このチュートリアルでは、水平方向にパーティション分割 (シャード) されたすべてのデータベースからレポートを作成する方法を学習します |
| [スキーマが異なるクラウド データベース間のクエリ](sql-database-elastic-query-vertical-partitioning.md) | このチュートリアルでは、スキーマが異なる複数のデータベースにまたがる T-SQL クエリを実行する方法を学習します |
| [スケールアウトされたクラウド データベース全体をレポートする](sql-database-elastic-query-horizontal-partitioning.md) |このチュートリアルでは、シャード化されたデータベース内のすべてのデータベースにまたがるレポートの作成について学習します。 |
|  | |

## <a name="database-authentication-and-authorization"></a>データベースの認証と承認
以下のチュートリアルでは、[ログインおよびユーザーの作成と管理](sql-database-manage-logins.md)について学習します。

| チュートリアル | Description |
| --- | --- | --- |
| [SQL の認証と承認](sql-database-control-access-sql-authentication-get-started.md) | このチュートリアルでは、SQL Server 認証を使用したログインとユーザーの作成、ロールへのユーザーの追加、およびロールへのアクセス許可の付与について説明します。 |
| [Azure AD 認証と承認](sql-database-control-access-aad-authentication-get-started.md) | このチュートリアルでは、Azure Active Directory 認証を使用したログインとユーザーの作成について説明します。 |
|  | |

## <a name="secure-and-protect-data"></a>データの安全性を確保して保護する
以下のチュートリアルでは、[Azure SQL Database のデータのセキュリティ保護](sql-database-security-overview.md)について学習します。

| チュートリアル | 説明 |
| --- | --- | 
| [Always Encrypted を使用して機密データを保護する ](sql-database-always-encrypted-azure-key-vault.md) |このチュートリアルでは、Always Encrypted ウィザードを使用して Azure SQL Database の機密データをセキュリティで保護する方法を学習します。 |
|  | |

## <a name="develop"></a>開発
以下のチュートリアルでは、アプリケーションとデータベースの開発について学習します。

| チュートリアル | Description |
| --- | --- | 
| [Excel を使用してレポートを作成する](sql-database-connect-excel.md) |このチュートリアルでは、Excel をクラウドの SQL Database に接続し、データをインポートして、データベース内の値に基づいてテーブルおよびグラフを作成できるようにする方法を学習します。 |
| [SQL Server を使用してアプリを構築する](https://www.microsoft.com/sql-server/developer-get-started/) |このチュートリアルでは、SQL Server を使用してアプリケーションを構築する方法を学習します |
| [エラスティック ツールでエンティティ フレームワークを使用する](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) |このチュートリアルは、Elastic Database ツールと統合するために Entity Framework アプリケーションに加える必要がある変更について学習します。 |
| [インメモリ OLTP を導入する](sql-database-in-memory-oltp-migration.md) | このチュートリアルでは、[インメモリ OLTP](sql-database-in-memory.md) を使用してトランザクション処理のパフォーマンスを向上させる方法を学習します。 |
|  | |

## <a name="data-sync"></a>データ同期
このチュートリアルでは、[データ同期](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)について学習します。

| チュートリアル | Description |
| --- | --- | 
| [Azure SQL データ同期の概要 (プレビュー)](sql-database-get-started-sql-data-sync.md) |このチュートリアルでは、Azure クラシック ポータルを使用して、Azure SQL データ同期の基礎を学習します。 |
|  | |

## <a name="next-steps"></a>次のステップ
[Azure SQL Database ソリューション クイック スタート ガイド](sql-database-solution-quick-starts.md)



<!--HONumber=Feb17_HO2-->


