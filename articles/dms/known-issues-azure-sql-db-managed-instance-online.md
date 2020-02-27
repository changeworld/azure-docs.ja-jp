---
title: Azure SQL Database マネージド インスタンスへのオンライン移行に関する既知の問題と制限事項
description: Azure SQL Database マネージド インスタンスへのオンライン移行に関する既知の問題と移行の制限事項について説明します。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 88e2b5894686ee93caecf33e04940803eb75f394
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648667"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>Azure SQL Database マネージド インスタンスへのオンライン移行に関する既知の問題と制限事項

ここでは、SQL Server から Azure SQL Database マネージド インスタンスへのオンライン移行に関する既知の問題と制限事項について説明します。

> [!IMPORTANT]
> SQL Server から Azure SQL Database へのオンライン移行では、SQL_variant データ型の移行はサポートされていません。

## <a name="backup-requirements"></a>バックアップの要件

- **チェックサムを使用するバックアップ**

    Azure Database Migration Service では、バックアップと復元の方法を使用して、オンプレミスのデータベースを SQL Database マネージド インスタンスに移行します。 Azure Database Migration Service は、チェックサムを使用して作成されたバックアップのみをサポートします。

    [バックアップ中または復元中にバックアップ チェックサムを有効または無効にする (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > 圧縮を使用してデータベースのバックアップを実行する場合、明示的に無効にしない限り、チェックサムは既定の動作です。

    オフライン移行で **[I will let Azure Database Migration Service…]\(Azure Database Migration Service でバックアップ ファイル...\)** を選択した場合、Azure Database Migration Service ではチェックサム オプションが有効な状態でデータベースのバックアップが実行されます。

- **バックアップ メディア**

    必ず各バックアップを個別のバックアップ メディア (バックアップ ファイル) に作成してください。 Azure Database Migration Service は、1 つのバックアップ ファイルに追加するバックアップをサポートしていません。 完全バックアップとログ バックアップを別々のバックアップ ファイルに作成します。

## <a name="data-and-log-file-layout"></a>データとログ ファイルのレイアウト

- **ログ ファイル数**

    Azure Database Migration Service は、複数のログ ファイルを含むデータベースをサポートしていません。 複数のログ ファイルがある場合は、それらを圧縮し、1 つのトランザクション ログ ファイルに再構成します。 空ではないログ ファイルにリモートでアクセスできないため、まずログ ファイルをバックアップする必要があります。

## <a name="sql-server-features"></a>SQL Server 機能

- **FileStream と FileTables**

    現在、SQL Database マネージド インスタンスは FileStream および FileTables をサポートしていません。 これらの機能に依存するワークロードの場合は、Azure VM で実行される SQL Server を Azure ターゲットとして選択することをお勧めします。

- **インメモリ テーブル**

    インメモリ OLTP は、SQL Database マネージド インスタンスの Premium レベルと Business Critical レベルで使用できます。General Purpose レベルではインメモリ OLTP がサポートされていません。

## <a name="migration-resets"></a>移行のリセット

- **デプロイ**

    SQL Database マネージド インスタンスは、自動修正とバージョン更新プログラムを含む PaaS サービスです。 SQL Database マネージド インスタンスの移行中、重要でない更新プログラムは最大 36 時間利用できます。 その後 (および重要な更新プログラムの場合)、移行が中断された場合、プロセスは完全復元状態にリセットされます。

    完全バックアップが復元され、すべてのログ バックアップに追いついた後にのみ、移行のカットオーバーを呼び出すことができます。 運用環境への移行のカットオーバーが影響を受ける場合は、[Azure DMS フィードバックのエイリアス](mailto:dmsfeedback@microsoft.com)にお問い合わせください。
