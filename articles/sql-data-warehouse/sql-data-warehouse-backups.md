---
title: Azure SQL Data Warehouse のバックアップ - スナップショット、地理冗長 | Microsoft Docs
description: Azure SQL Data Warehouse を復元ポイントまたは別の地理的な領域に復元できる、SQL Data Warehouse の組み込みデータベースのバックアップについて説明します。
services: sql-data-warehouse
documentationcenter: ''
author: barbkess
manager: jhubbard
editor: ''
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 159a1d34caba829750da33dbc4ad403fb21cd147
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="backup-and-restore-in-sql-data-warehouse"></a>SQL Data Warehouse でのバックアップおよび復元
この記事では、SQL Data Warehouse のバックアップの詳細について説明します。 データ ウェアハウスのバックアップを使用して、プライマリ リージョンにデータベース スナップショットを復元したり、geo ペア リージョンに geo バックアップを復元したりします。 

## <a name="what-is-a-data-warehouse-backup"></a>データ ウェアハウスのバックアップとは
データ ウェアハウスのバックアップは、データ ウェアハウスを復元するために使用できるデータベースのコピーです。  SQL Data Warehouse は分散システムなので、データ ウェアハウスのバックアップは Azure Storage に配置されている多くのファイルで構成されます。 データ ウェアハウスのバックアップには、データ ウェアハウスに関連付けられているすべてのデータベースおよびファイルのローカル データベース スナップショットと geo バックアップの両方が含まれています。 

## <a name="local-snapshot-backups"></a>ローカル スナップショット バックアップ
SQL Data Warehouse は、1 日を通してデータ ウェアハウスのスナップショットを作成します。 スナップショットは 7 日間使用できます。 SQL Data Warehouse では、8 時間の回復ポイントの目標 (RPO) がサポートされています。 プライマリ リージョンのデータ ウェアハウスを、過去 7 日間に作成されたいずれかのスナップショットに復元することができます。

最新のスナップショットが開始された時間を表示するには、オンラインの SQL Data Warehouse で次のクエリを実行します。 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="geo-backups"></a>geo バックアップ
SQL Data Warehouse は、1 日に 1 回、[ペアのデータ センター](../best-practices-availability-paired-regions.md)に geo バックアップを実行します。 geo 復元の RPO は 24 時間です。 geo ペア リージョン内のサーバーに geo バックアップを復元できます。 geo バックアップにより、プライマリ リージョンのスナップショットにアクセスできない場合でも、データ ウェアハウスを復元できます。

geo バックアップは既定で有効です。 データ ウェアハウスが弾力性に合わせて最適化されている場合、必要に応じて[オプトアウト](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy)できます。 コンピューティング パフォーマンス レベルに合わせて最適化されている場合、geo バックアップをオプトアウトすることはできません。

## <a name="backup-costs"></a>バックアップのコスト
Azure の課金には、Azure Premium Storage の明細項目と geo 冗長ストレージの明細項目があることがわかります。 Premium Storage の料金は、スナップショットを含む、プライマリ リージョンでのデータの格納コストの合計です。  geo 冗長の料金には、geo バックアップを格納するコストが含まれます。  

プライマリ データ ウェアハウスと 7 日間の Azure BLOB のスナップショットの総コストは、TB 単位で四捨五入されます。 たとえば、データ ウェアハウスが 1.5 TB で、スナップショットが 100 GB を使用する場合、Azure Premium Storage の料金で 2 TB のデータが課金されます。 

> [!NOTE]
> 各スナップショットは最初は空で、プライマリ データ ウェアハウスに変更を加えるたびに増加します。 データ ウェアハウスが変更されると、すべてのスナップショットのサイズが増加します。 そのため、スナップショットのストレージ コストは、変化率に合わせて増加します。
> 
> 

地理冗長ストレージを使用している場合は、個別のストレージ料金がかかります。 geo 冗長ストレージは、標準の読み取りアクセスの地理冗長ストレージ (RA-GRS) の料金で課金されます。

SQL Data Warehouse の価格の詳細については、「[SQL Data Warehouse の価格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)」をご覧ください。

## <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>データ ウェアハウスが一時停止しているときのスナップショットのリテンション期間
データ ウェアハウスの一時停止中、SQL Data Warehouse ではスナップショットが作成されないため、スナップショットの有効期限は切れません。 データ ウェアハウスが一時停止されている間、スナップショットの有効期間は変更されません。 スナップショットの保持は、データ ウェアハウスがオンラインになっている日数に基づきます。カレンダーの日数ではありません。

たとえば、スナップショットが 10 月 1 日午後 4 時に開始され、データ ウェアハウスが 10 月 3 日午後 4 時に一時停止された場合、スナップショットは最大 2 日間保持された状態です。 データ ウェアハウスがオンラインに戻ったとき、スナップショットは 2 日前のものです。 データ ウェアハウスが 10 月 5 日午後 4 時にオンラインになった場合、スナップショットは 2 日の状態であるため、あと 5 日間保持されます。

データ ウェアハウスがオンラインに戻ると、SQL Data Warehouse は新しいスナップショットを再開し、データが 7 日以上経過すると、スナップショットの有効期限が切れます。

## <a name="can-i-restore-a-dropped-data-warehouse"></a>削除されたデータ ウェアハウスを復元できますか。
データ ウェアハウスを削除すると、SQL Data Warehouse によって最後のスナップショットが作成され、7 日間保存されます。 データ ウェアハウスは、削除するときに作成された最後の復元ポイントに復元できます。 

> [!IMPORTANT]
> 論理的な SQL Server インスタンスを削除すると、そのインスタンスに属するデータベースもすべて削除されます。これを回復することはできません。 削除されたサーバーを復元することはできません。
> 

## <a name="next-steps"></a>次の手順
SQL Data Warehouse を復元するには、「[Azure SQL Data Warehouse の復元](sql-data-warehouse-restore-database-overview.md)」をご覧ください。

ビジネス継続性の概要については、「[Azure SQL Database によるビジネス継続性の概要](../sql-database/sql-database-business-continuity.md)」をご覧ください。
