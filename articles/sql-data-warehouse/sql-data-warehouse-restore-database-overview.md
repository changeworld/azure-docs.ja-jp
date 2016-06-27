<properties
   pageTitle="Azure SQL Data Warehouse の復元 (概要) | Microsoft Azure"
   description="Azure SQL Data Warehouse でデータベースを復旧するためのデータベース復元オプションの概要。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/14/2016"
   ms.author="elfish;barbkess;sonyama"/>


# Azure SQL Data Warehouse の復元 (概要)

> [AZURE.SELECTOR]
- [概要][]
- [ポータル][]
- [PowerShell][]
- [REST ()][]

Azure SQL Data Warehouse は、ローカル冗長ストレージと自動バックアップの両方を使用してデータを保護します。自動バックアップでは、管理を必要としない方法で、データベースを偶発的な破損や削除から保護できます。ユーザーが意図せずに、または偶発的にデータを変更または削除するような場合には、それ以前の任意の時点にデータベースを復元することで、ビジネスの継続性を確保できます。SQL Data Warehouse では、ダウンタイムなしでデータベースをシームレスにバックアップするために、Azure Storage のスナップショットを使用しています。

## 自動バックアップ

**アクティブな**データベースは、最低でも 8 時間ごとに 1 回の自動バックアップが行われ、7 日間保持されます。これによって、アクティブなデータベースを過去 7 日間にわたる複数の復元ポイントのいずれかに復元できるようになります。

データベースが一時停止すると、新しいスナップショットの作成は停止し、それ以前のスナップショットは保持期間が 7 日に達したものから順に削除されます。データベースの一時停止期間が 7 日を超えた場合は最後のスナップショットが保存されるため、常に少なくとも 1 つのスナップショットが確保されることになります。

データベースが削除されると、最後のスナップショットは 7 日間保存されます。

次のクエリを実行すると、インスタンスで最後のバックアップがいつ作成されたのかを確認することができます。

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

バックアップを 7 日間より長く保持する必要がある場合は、いずれかの復元ポイントを新しいデータベースに復元します。その後は、保持しているバックアップのストレージ領域のみが課金されるように、必要に応じてそのデータベースを一時停止することができます。

## データの冗長性

SQL Data Warehouse では、バックアップに加えて、[ローカル冗長 (LRS)][] Azure Premium Storage でデータを保護することもできます。複数の同期されたデータ コピーがローカル データ センターに保持され、ローカルで障害が発生した場合には透過的なデータ保護が保証されます。データの冗長性により、ディスク障害などのインフラストラクチャの問題でデータを失う心配がなくなります。データの冗長性を確保することで、フォールト トレラントで可用性の高いインフラストラクチャによるビジネス継続性が実現できます。

## データベースを復元する

SQL Data Warehouse の復元操作は簡単で、Azure ポータルで行うか、PowerShell または REST API を使用して自動的に行うことができます。


## 次のステップ
Azure SQL Database の各エディションのビジネス継続性機能については、[Azure SQL Database のビジネス継続性の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Azure SQL Database のビジネス継続性の概要]: ./sql-database-business-continuity.md
[ローカル冗長 (LRS)]: ../storage/storage-redundancy.md
[概要]: ./sql-data-warehouse-restore-database-overview.md
[ポータル]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST ()]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0615_2016-->