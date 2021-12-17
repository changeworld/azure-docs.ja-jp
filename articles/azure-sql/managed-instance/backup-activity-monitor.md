---
title: バックアップ アクティビティの監視
titleSuffix: Azure SQL Managed Instance
description: 拡張イベントを使用して Azure SQL Managed Instance バックアップ アクティビティを監視する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: Misliplavo
ms.author: mlazic
ms.reviewer: mathoma
ms.date: 12/14/2018
ms.openlocfilehash: 8c38b9d77baee05fe14c07dc5e6bc185bcb46538
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111897265"
---
# <a name="monitor-backup-activity-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance のバックアップ アクティビティの監視 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、[Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) のバックアップ アクティビティを監視するために拡張イベント (XEvent) セッションを構成する方法について説明します。 

## <a name="overview"></a>概要

Azure SQL Managed Instance のバックアップ アクティビティ中、レポート作成を目的としたイベント ([拡張イベントまたは XEvent](../database/xevent-db-diff-from-svr.md) とも呼ばれる) が出力されます。 XEvent セッションを構成して、バックアップの状態、バックアップの種類、サイズ、時刻、msdb データベース内の場所などの情報を追跡します。 この情報は、バックアップ監視ソフトウェアと統合し、Enterprise 監査の目的に使用することもできます。 

Enterprise 監査には、バックアップが正常に終了したことの証明、バックアップの時刻、およびバックアップ操作の期間が必要とされる場合があります。

## <a name="configure-xevent-session"></a>XEvent セッションを構成する

SQL Managed Instance バックアップの進行状況を記録するには、拡張イベント `backup_restore_progress_trace` を使用します。 必要に応じて XEvent セッションを変更し、ビジネスのために関心のある情報を追跡します。 これらの T-SQL スニペットを使用すると、XEvent セッションがリング バッファーに格納されますが、[Azure Blob Storage](../database/xevent-code-event-file.md) に書き込むこともできます。 リング バッファーにデータを格納する XEvent セッションには約 1,000 メッセージという制限があるため、最近のアクティビティを追跡するためにのみ使用する必要があります。 さらに、リング バッファー データはフェールオーバー時に失われます。 そのため、バックアップの履歴レコードの場合は、代わりにイベント ファイルに書き込みます。 

### <a name="simple-tracking"></a>単純な追跡

完了した完全バックアップに関する単純なイベントを収集するには、単純な XEvent セッションを構成します。 このスクリプトを使用すると、データベースの名前、処理された合計バイト数、バックアップが完了した時刻が収集されます。

Transact-SQL (T SQL) を使用して単純な XEvent セッションを構成します。 


```sql
CREATE EVENT SESSION [Simple backup trace] ON SERVER
ADD EVENT sqlserver.backup_restore_progress_trace(
WHERE operation_type = 0
AND trace_message LIKE '%100 percent%')
ADD TARGET package0.ring_buffer
WITH(STARTUP_STATE=ON)
GO
ALTER EVENT SESSION [Simple backup trace] ON SERVER
STATE = start;
```



### <a name="verbose-tracking"></a>詳細な追跡

バックアップ アクティビティの詳細を追跡するには、詳細な XEvent セッションを構成します。 このスクリプトを使用すると、完全と差分の両方およびログのバックアップの開始と終了が収集されます。 このスクリプトはより詳細なので、リング バッファーがより短時間でいっぱいになります。そのため、単純なスクリプトよりも短時間でエントリがリサイクルされる可能性があります。 

Transact-SQL (T SQL) を使用して詳細な XEvent セッションを構成します。 

```sql
CREATE EVENT SESSION [Verbose backup trace] ON SERVER 
ADD EVENT sqlserver.backup_restore_progress_trace(
    WHERE (
              [operation_type]=(0) AND (
              [trace_message] like '%100 percent%' OR 
              [trace_message] like '%BACKUP DATABASE%' OR [trace_message] like '%BACKUP LOG%'))
       )
ADD TARGET package0.ring_buffer
WITH (MAX_MEMORY=4096 KB,EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,
       MAX_DISPATCH_LATENCY=30 SECONDS,MAX_EVENT_SIZE=0 KB,MEMORY_PARTITION_MODE=NONE,
       TRACK_CAUSALITY=OFF,STARTUP_STATE=ON)

ALTER EVENT SESSION [Verbose backup trace] ON SERVER
STATE = start;

```

## <a name="monitor-backup-progress"></a>バックアップの進行状況の監視 

XEvent セッション作成後、Transact-SQL (T-SQL) を使用してリング バッファーの結果に対してクエリを実行し、バックアップの進行状況を監視することができます。 XEvent が開始されると、すべてのバックアップ イベントが収集され、およそ 5 から 10 分ごとにエントリがセッションに追加されます。  

### <a name="simple-tracking"></a>単純な追跡

次の Transact-SQL (T-SQL) コードは、単純な XEvent セッションに対してクエリを実行し、データベースの名前、処理された合計バイト数、バックアップが完了した時刻を返すものです。 

```sql 
WITH
a AS (SELECT xed = CAST(xet.target_data AS xml)
FROM sys.dm_xe_session_targets AS xet
JOIN sys.dm_xe_sessions AS xe
ON (xe.address = xet.event_session_address)
WHERE xe.name = 'Backup trace'),
b AS(SELECT
d.n.value('(@timestamp)[1]', 'datetime2') AS [timestamp],
ISNULL(db.name, d.n.value('(data[@name="database_name"]/value)[1]', 'varchar(200)')) AS database_name,
d.n.value('(data[@name="trace_message"]/value)[1]', 'varchar(4000)') AS trace_message
FROM a
CROSS APPLY  xed.nodes('/RingBufferTarget/event') d(n)
LEFT JOIN master.sys.databases db
ON db.physical_database_name = d.n.value('(data[@name="database_name"]/value)[1]', 'varchar(200)'))
SELECT * FROM b
```

次のスクリーンショットは、上記のクエリの出力例を示しています。 

![xEvent の出力のスクリーンショット](./media/backup-activity-monitor/present-xevents-output.png)

この例では、2 時間 30 分の間に 5 つのデータベースが自動的にバックアップされ、XEvent セッションに 130 個のエントリがあります。 

### <a name="verbose-tracking"></a>詳細な追跡 

次の Transact-SQL (T-SQL) コードは、詳細な XEvent セッションに対してクエリを実行し、データベースの名前のほか、完全と差分の両方およびログのバックアップの開始と終了を返すものです。 


```sql
WITH
a AS (SELECT xed = CAST(xet.target_data AS xml)
FROM sys.dm_xe_session_targets AS xet
JOIN sys.dm_xe_sessions AS xe
ON (xe.address = xet.event_session_address)
WHERE xe.name = 'Verbose backup trace'),
b AS(SELECT
d.n.value('(@timestamp)[1]', 'datetime2') AS [timestamp],
ISNULL(db.name, d.n.value('(data[@name="database_name"]/value)[1]', 'varchar(200)')) AS database_name,
d.n.value('(data[@name="trace_message"]/value)[1]', 'varchar(4000)') AS trace_message
FROM a
CROSS APPLY  xed.nodes('/RingBufferTarget/event') d(n)
LEFT JOIN master.sys.databases db
ON db.physical_database_name = d.n.value('(data[@name="database_name"]/value)[1]', 'varchar(200)'))
SELECT * FROM b
```

次のスクリーンショットは、XEvent セッションの完全バックアップの例を示しています。

:::image type="content" source="media/backup-activity-monitor/output-with-full.png" alt-text="完全バックアップを示す XEvent 出力":::

次のスクリーンショットは、XEvent セッションでの差分バックアップの出力例を示しています。

:::image type="content" source="media/backup-activity-monitor/output-with-differential.png" alt-text="差分バックアップを示す XEvent 出力":::


## <a name="next-steps"></a>次のステップ

バックアップが完了したら、[特定の時点に復元](point-in-time-restore.md)することや、[長期保有ポリシーを構成](long-term-backup-retention-configure.md)することができます。 

詳細については、[自動バックアップ](../database/automated-backups-overview.md)に関するページを参照してください。 
