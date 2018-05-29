---
title: Azure SQL Data Warehouse のバックアップと復元 - スナップショット、geo 冗長 | Microsoft Docs
description: Azure SQL Data Warehouse でバックアップと復元がどのように機能するかを説明します。 データ ウェアハウスをプライマリ リージョンの復元ポイントに復元するには、データ ウェアハウスのバックアップを使用します。 異なる地理的リージョンに復元するには、Geo 冗長バックアップを使用します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: a4f24aad95f13315eaeac790c9006ca00f61af69
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187601"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse でのバックアップと復元
Azure SQL Data Warehouse でバックアップと復元がどのように機能するかを説明します。 データ ウェアハウスをプライマリ リージョンの復元ポイントに復元するには、データ ウェアハウスのバックアップを使用します。 異なる地理的リージョンに復元するには、Geo 冗長バックアップを使用します。 

## <a name="what-is-backup-and-restore"></a>バックアップと復元とは何か
*データ ウェアハウスのバックアップ*は、データ ウェアハウスを復元するために使用できるデータベースのコピーです。  SQL Data Warehouse は分散システムなので、データ ウェアハウスのバックアップは Azure Storage に配置されている多くのファイルで構成されます。 データ ウェアハウスのバックアップには、データ ウェアハウスに関連付けられているすべてのデータベースおよびファイルのローカル データベース スナップショットと geo バックアップの両方が含まれています。 

*データ ウェアハウスの復元*とは、既存または削除済みのデータ ウェアハウスのバックアップから作成された新しいデータ ウェアハウスのことです。 復元されたデータ ウェアハウスは、特定の時点でバックアップされたデータ ウェアハウスを再作成します。 偶発的な破損や削除が起きた後にデータを再作成するデータウェアハウスの復元は、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。

SQL Data Warehouse のディザスター リカバリー機能の一部として、ローカル バックアップと地理的バックアップの両方からの復元を利用できます。 

## <a name="local-snapshot-backups"></a>ローカル スナップショット バックアップ
ローカル スナップショット バックアップは、サービスの組み込み機能です。  これらを有効にする必要はありません。 

SQL Data Warehouse は、1 日を通してデータ ウェアハウスのスナップショットを作成します。 スナップショットは 7 日間使用できます。 SQL Data Warehouse では、8 時間の回復ポイントの目標 (RPO) がサポートされています。 プライマリ リージョンのデータ ウェアハウスを、過去 7 日間に作成されたいずれかのスナップショットに復元することができます。

最新のスナップショットが開始された時間を表示するには、オンラインの SQL Data Warehouse で次のクエリを実行します。 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

### <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>データ ウェアハウスが一時停止しているときのスナップショットのリテンション期間
データ ウェアハウスの一時停止中、SQL Data Warehouse ではスナップショットが作成されないため、スナップショットの有効期限は切れません。 データ ウェアハウスが一時停止されている間、スナップショットの有効期間は変更されません。 スナップショットの保持は、データ ウェアハウスがオンラインになっている日数に基づきます。カレンダーの日数ではありません。

たとえば、スナップショットが 10 月 1 日午後 4 時に開始され、データ ウェアハウスが 10 月 3 日午後 4 時に一時停止された場合、スナップショットは最大 2 日間保持された状態です。 データ ウェアハウスがオンラインに戻ったとき、スナップショットは 2 日前のものです。 データ ウェアハウスが 10 月 5 日午後 4 時にオンラインになった場合、スナップショットは 2 日の状態であるため、あと 5 日間保持されます。

データ ウェアハウスがオンラインに戻ると、SQL Data Warehouse は新しいスナップショットを再開し、データが 7 日以上経過すると、スナップショットの有効期限が切れます。

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>データ ウェアハウスが停止しているときのスナップショットのリテンション期間
データ ウェアハウスを削除すると、SQL Data Warehouse によって最後のスナップショットが作成され、7 日間保存されます。 データ ウェアハウスは、削除するときに作成された最後の復元ポイントに復元できます。 

> [!IMPORTANT]
> 論理的な SQL Server インスタンスを削除すると、そのインスタンスに属するデータベースもすべて削除されます。これを回復することはできません。 削除されたサーバーを復元することはできません。
> 

## <a name="geo-backups"></a>geo バックアップ
SQL Data Warehouse は、1 日に 1 回、[ペアのデータ センター](../best-practices-availability-paired-regions.md)に geo バックアップを実行します。 geo 復元の RPO は 24 時間です。 geo バックアップは、SQL Data Warehouse がサポートされているその他の任意のリージョン内のサーバーに復元することができます。 geo バックアップにより、プライマリ リージョンのスナップショットにアクセスできない場合でも、データ ウェアハウスを復元できます。

geo バックアップは既定で有効です。 データ ウェアハウスが Gen1 である場合は、必要に応じて[オプトアウト](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy)することができます。 データ保護は組み込み保証であるため、Gen2 の geo バックアップからオプトアウトすることはできません。

## <a name="backup-costs"></a>バックアップのコスト
Azure の課金には、Azure Premium Storage の明細項目と geo 冗長ストレージの明細項目があることがわかります。 Premium Storage の料金は、スナップショットを含む、プライマリ リージョンでのデータの格納コストの合計です。  geo 冗長の料金には、geo バックアップを格納するコストが含まれます。  

プライマリ データ ウェアハウスと 7 日間の Azure BLOB のスナップショットの総コストは、TB 単位で四捨五入されます。 たとえば、データ ウェアハウスが 1.5 TB で、スナップショットが 100 GB を使用する場合、Azure Premium Storage の料金で 2 TB のデータが課金されます。 

> [!NOTE]
> 各スナップショットは最初は空で、プライマリ データ ウェアハウスに変更を加えるたびに増加します。 データ ウェアハウスが変更されると、すべてのスナップショットのサイズが増加します。 そのため、スナップショットのストレージ コストは、変化率に合わせて増加します。
> 
> 

地理冗長ストレージを使用している場合は、個別のストレージ料金がかかります。 geo 冗長ストレージは、標準の読み取りアクセスの地理冗長ストレージ (RA-GRS) の料金で課金されます。

SQL Data Warehouse の価格の詳細については、「[SQL Data Warehouse の価格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)」をご覧ください。

## <a name="restoring-from-restore-points"></a>復元ポイントからの復元
各スナップショットには、スナップショットの開始時刻を表す復元ポイントがあります。 データ ウェアハウスを復元するには、復元ポイントを選択して restore コマンドを実行します。  

SQL Data Warehouse は、常に新しいデータ ウェアハウスにバックアップを復元します。 復元されたデータ ウェアハウスと現在のデータ ウェアハウスの両方を保持するか、いずれか 1 つを削除することができます。 現在のデータ ウェアハウスを、復元されたデータ ウェアハウスで置換する場合は、MODIFY NAME オプションを指定して [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) を使用し、名前を変更できます。 

データ ウェアハウスを復元する場合は、[Azure Portal を使用したデータ ウェアハウスの復元](sql-data-warehouse-restore-database-portal.md)、 [PowerShell を使用したデータ ウェアハウスを復元](sql-data-warehouse-restore-database-powershell.md)、または [T-SQL を使用したデータ ウェアハウスの復元](sql-data-warehouse-restore-database-rest-api.md)を参照してください。

削除済みまたは一時停止中のデータ ウェアハウスを復元する場合は、[サポート チケットを作成](sql-data-warehouse-get-started-create-support-ticket.md)できます。 


## <a name="geo-redundant-restore"></a>geo 冗長復元
選択したパフォーマンス レベルで、データ ウェアハウスを Azure SQL Data Warehouse をサポートするリージョンに復元できます。 

> [!NOTE]
> geo 冗長復元を実行するには、この機能の使用を中止しないようにする必要があります。
> 
> 

## <a name="restore-timeline"></a>タイムライン復元
過去 7 日間の任意の復元ポイントにデータベースを復元できます。 スナップショットは 4 ～ 8 時間ごとに開始され、7 日間使用できます。 スナップショットが 7 日間以上経過した場合、期限切れとなり、復元ポイントは使用できなくなります。 

バックアップは、一時停止中のデータ ウェアハウスに対しては実行されません。 データ ウェアハウスが 7 日以上一時停止している場合は、まったく復元ポイントがありません。 

## <a name="restore-costs"></a>復元コスト
復元されたデータ ウェアハウスのストレージ料金は、Azure Premium Storage レートで請求されます。 

復元されたデータ ウェアハウスを一時停止した場合、Azure Premium Storage レートでストレージに対して課金されます。 一時停止の利点は、コンピューティング リソースに対しては課金されないことです。

SQL Data Warehouse の価格の詳細については、「[SQL Data Warehouse の価格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)」をご覧ください。

## <a name="restore-use-cases"></a>復元のユース ケース
データ ウェアハウスの復元の主な用途は、偶発的なデータの消失や破損が生じた際にデータを回復することです。 バックアップを 8 日以上保持する場合にも、データ ウェアハウスの復元を使用できます。 バックアップが復元されたら、データ ウェアハウスをオンラインにして、データベースを無期限に一時停止してコンピューティング コストを節約することができます。 一時停止したデータベースについては、Azure Premium Storage レートでストレージに対して課金されます。 

## <a name="next-steps"></a>次の手順
障害計画の詳細については、[ビジネス継続性の概要](../sql-database/sql-database-business-continuity.md)をご覧ください
