---
title: Azure SQL Data Warehouse のバックアップと復元 - スナップショット、geo 冗長 | Microsoft Docs
description: Azure SQL Data Warehouse でバックアップと復元がどのように機能するかを説明します。 データ ウェアハウスをプライマリ リージョンの復元ポイントに復元するには、データ ウェアハウスのバックアップを使用します。 異なる地理的リージョンに復元するには、Geo 冗長バックアップを使用します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: bdcc0510503e48caf70f4f0d91d7602d767ca9ab
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092480"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse でのバックアップと復元
Azure SQL Data Warehouse でバックアップと復元がどのように機能するかを説明します。 データ ウェアハウスをプライマリ リージョンの以前の復元ポイントに復旧またはコピーするには、データ ウェアハウスのスナップショットを使用します。 異なる地理的リージョンに復元するには、データ ウェアハウスの geo 冗長バックアップを使用します。 

## <a name="what-is-a-data-warehouse-snapshot"></a>データ ウェアハウスのスナップショットとは
"*データ ウェアハウスのスナップショット*" では、データ ウェアハウスの以前の状態を復旧またはコピーするために利用できる復元ポイントが作成されます。  SQL Data Warehouse は分散システムなので、データ ウェアハウスのスナップショットは Azure Storage に配置されている多くのファイルで構成されます。 スナップショットでは、データ ウェアハウスに格納されたデータの増分の変更がキャプチャされます。

"*データ ウェアハウスの復元*" とは、既存または削除済みのデータ ウェアハウスの復元ポイントから作成された新しいデータ ウェアハウスのことです。 偶発的な破損や削除が起きた後にデータを再作成するデータウェアハウスの復元は、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 データ ウェアハウスは、テストまたは開発用にデータ ウェアハウスのコピーを作成する強力なメカニズムでもあります。  SQL Data Warehouse では高速復元メカニズムが使用されており、どのようなデータ サイズでも、同じリージョン内であれば 20 分未満で復元できます。 

## <a name="automatic-restore-points"></a>自動復元ポイント
スナップショットは、復元ポイントを作成する、サービスの組み込み機能です。 この機能を有効にする必要はありません。 サービスは自動復元ポイントを使用して復旧の SLA を維持するので、現時点では、ユーザーはこれらの復元ポイントを削除できません。

SQL Data Warehouse は、1 日を通してデータ ウェアハウスのスナップショットを取得し、7 日間利用できる復元ポイントを作成します。 この保持期間は変更できません。 SQL Data Warehouse では、8 時間の復元ポイントの目標 (RPO) がサポートされています。 プライマリ リージョンのデータ ウェアハウスを、過去 7 日間に作成されたいずれかのスナップショットから復元することができます。

最新のスナップショットが開始された時間を表示するには、オンラインの SQL Data Warehouse で次のクエリを実行します。 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>ユーザー定義の復元ポイント
ユーザーは、この機能を使用して、大規模な変更の前後に、スナップショットを手動でトリガーしてデータ ウェアハウスの復元ポイントを作成できます。 この機能により、復元ポイントの論理的な一貫性を保証し、ワークロードの中断やユーザー エラーが発生して迅速な復旧時間が必要な場合に、追加のデータ保護を提供できます。 ユーザー定義の復元ポイントは、7 日間使用でき、自動的に削除されます。 ユーザー定義の復元ポイントの保持期間を変更することはできません。 任意の時点で **42 個のユーザー定義の復元ポイント**が保証されているため、別の復元ポイントを作成する前に[削除](https://go.microsoft.com/fwlink/?linkid=875299)する必要があります。 ユーザー定義の復元ポイントを作成するスナップショットのトリガーは、[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabaserestorepoint?view=azurermps-6.2.0#examples) または Azure portal で行うことができます。


> [!NOTE]
> 7 日より長い復元ポイントが必要な場合は、[こちら](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points)でこの機能に投票してください。 ユーザー定義の復元ポイントを作成し、新しく作成された復元ポイントを新しいデータ ウェアハウスに復元することもできます。 復元が済むと、データ ウェアハウスはオンラインになるので、無期限に一時停止してコンピューティング コストを節約することができます。 一時停止したデータベースについては、Azure Premium Storage レートでストレージに対して課金されます。 復元されたデータ ウェアハウスのアクティブなコピーが必要な場合は、わずか数分で再開できます。
>

### <a name="restore-point-retention"></a>復元ポイントのリテンション期間
以下に復元ポイントのリテンション期間の詳細について説明します。
1. SQL Data Warehouse は、7 日間のリテンション期間に達したとき**と**、復元ポイントの合計数が少なくとも 42 個ある場合 (ユーザー定義と自動の両方を含む) に、復元ポイントを削除します
2. データ ウェアハウスが一時停止しているときはスナップショットは取得されません
3. 復元ポイントの経過時間は、復元ポイントが取得された時点 (データ ウェアハウスが一時停止された時点を含む) からのカレンダーの絶対日数によって測定されます。
4. 任意の時点で、復元ポイントが 7 日間のリテンション期間に達していない限り、データ ウェアハウスは最大 42 個のユーザー定義の復元ポイントと 42 個の自動復元ポイントを格納できることが保証されています。
5. スナップショットが取得され、データ ウェアハウスが 7 日間以上停止されてから再開した場合、復元ポイントの数が合計で 42 になるまで (ユーザー定義と自動の両方を含む)、復元ポイントを維持できます。

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>データ ウェアハウスが停止しているときのスナップショットのリテンション期間
データ ウェアハウスを削除すると、SQL Data Warehouse によって最後のスナップショットが作成され、7 日間保存されます。 データ ウェアハウスは、削除するときに作成された最後の復元ポイントに復元できます。 

> [!IMPORTANT]
> 論理的な SQL Server インスタンスを削除すると、そのインスタンスに属するデータベースもすべて削除されます。これを回復することはできません。 削除されたサーバーを復元することはできません。
>

## <a name="geo-backups"></a>geo バックアップ
SQL Data Warehouse は、1 日に 1 回、[ペアのデータ センター](../best-practices-availability-paired-regions.md)に geo バックアップを実行します。 geo 復元の RPO は 24 時間です。 geo バックアップは、SQL Data Warehouse がサポートされているその他の任意のリージョン内のサーバーに復元することができます。 geo バックアップにより、プライマリ リージョンの復元ポイントにアクセスできない場合でも、データ ウェアハウスを復元できます。

geo バックアップは既定で有効です。 データ ウェアハウスが Gen1 である場合は、必要に応じて[オプトアウト](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy)することができます。 データ保護は組み込み保証であるため、Gen2 の geo バックアップからオプトアウトすることはできません。

> [!NOTE]
> geo バックアップ用にさらに短い RPO が必要な場合は、[こちら](https://feedback.azure.com/forums/307516-sql-data-warehouse)でこの機能に投票してください。 ユーザー定義の復元ポイントを作成し、新しく作成された復元ポイントを異なるリージョンの新しいデータ ウェアハウスに復元することもできます。 復元が済むと、データ ウェアハウスはオンラインになるので、無期限に一時停止してコンピューティング コストを節約することができます。 一時停止したデータベースについては、Azure Premium Storage レートでストレージに対して課金されます。 それから一時停止します。 データ ウェアハウスのアクティブなコピーが必要な場合は、わずか数分で再開できます。
>


## <a name="backup-and-restore-costs"></a>バックアップと復元のコスト
Azure の課金には、ストレージの明細項目とディザスター リカバリー ストレージの明細項目があることがわかります。 ストレージの料金は、プライマリ リージョンでのデータの格納と、スナップショットによってキャプチャされた増分変更のコストの合計です。 スナップショットの現在の取得方法について詳しくは、こちらの[ドキュメント](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios)をご覧ください。 geo 冗長の料金には、geo バックアップを格納するコストが含まれます。  

プライマリ データ ウェアハウスと 7 日間のスナップショット変更の総コストは、TB 単位で四捨五入されます。 たとえば、データ ウェアハウスが 1.5 TB で、スナップショットが 100 GB をキャプチャする場合、Azure Premium Storage の料金で 2 TB のデータが課金されます。 

地理冗長ストレージを使用している場合は、個別のストレージ料金がかかります。 geo 冗長ストレージは、標準の読み取りアクセスの地理冗長ストレージ (RA-GRS) の料金で課金されます。

SQL Data Warehouse の価格の詳細については、「[SQL Data Warehouse の価格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)」と、リージョンをまたいで復元するときの[送信料金](https://azure.microsoft.com/pricing/details/bandwidth/)をご覧ください。

## <a name="restoring-from-restore-points"></a>復元ポイントからの復元
各スナップショットでは、スナップショットの開始時刻を表す復元ポイントが作成されます。 データ ウェアハウスを復元するには、復元ポイントを選択して restore コマンドを実行します。  

復元されたデータ ウェアハウスと現在のデータ ウェアハウスの両方を保持するか、いずれか 1 つを削除することができます。 現在のデータ ウェアハウスを、復元されたデータ ウェアハウスで置換する場合は、MODIFY NAME オプションを指定して [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) を使用し、名前を変更できます。 

データ ウェアハウスを復元する場合は、[Azure Portal を使用したデータ ウェアハウスの復元](sql-data-warehouse-restore-database-portal.md)、 [PowerShell を使用したデータ ウェアハウスを復元](sql-data-warehouse-restore-database-powershell.md)、または [T-SQL を使用したデータ ウェアハウスの復元](sql-data-warehouse-restore-database-rest-api.md)を参照してください。

削除済みまたは一時停止中のデータ ウェアハウスを復元する場合は、[サポート チケットを作成](sql-data-warehouse-get-started-create-support-ticket.md)できます。 


## <a name="geo-redundant-restore"></a>geo 冗長復元
選択したパフォーマンス レベルで、SQL Data Warehouse をサポートする任意のリージョンに[データ ウェアハウスを復元](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region)できます。 

> [!NOTE]
> geo 冗長復元を実行するには、この機能の使用を中止しないようにする必要があります。
>

## <a name="next-steps"></a>次の手順
障害計画の詳細については、[ビジネス継続性の概要](../sql-database/sql-database-business-continuity.md)をご覧ください
