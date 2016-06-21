<properties
   pageTitle="Azure SQL Data Warehouse でのデータベースの復元 (概要) | Microsoft Azure"
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
   ms.date="06/04/2016"
   ms.author="elfish;barbkess;sonyama"/>


# Azure SQL Data Warehouse でのデータベースの復元 (概要)

> [AZURE.SELECTOR]
- [概要](sql-data-warehouse-restore-database-overview.md)
- [ポータル](sql-data-warehouse-restore-database-portal.md)
- [PowerShell](sql-data-warehouse-restore-database-powershell.md)
- [REST ()](sql-data-warehouse-manage-restore-database-rest-api.md)

Azure SQL Data Warehouse でデータベースを復元するオプションについて説明します。ライブ データ ウェアハウスおよび削除されたデータ ウェアハウスの復元を対象とします。ライブ データ ウェアハウスおよび削除されたデータ ウェアハウスは、すべてのデータ ウェアハウスから作成された自動スナップショットから復元されます。

## 復旧のシナリオ

**インフラストラクチャの障害からの回復:** このシナリオは、ディスク障害などのインフラストラクチャ問題からの回復を示します。お客様は、フォールト トレラントで、可用性の高いインフラストラクチャによってビジネス継続性を実現したいと考えます。

**ユーザー エラーからの回復:** このシナリオは、意図しない、または偶発的なデータの破損または削除からの回復を示します。ユーザーが意図せずに、または偶発的にデータを変更または削除した場合、顧客は以前の任意の時点にデータベースを復元することで、ビジネスの継続性を確保したいと考えます。

## スナップショット ポリシー

[AZURE.INCLUDE [SQL Data Warehouse のバックアップ保持ポリシー](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## データベース復元機能

前述のシナリオで、SQL Data Warehouse によってデータベースの信頼性を高め、回復性と継続的な操作を行うことができるようにする方法について説明します。


### データの冗長性

SQL Data Warehouse には、[ローカル冗長 (LRS)](../storage/storage-redundancy.md) な Azure Premium Storage にすべてのデータが格納され、データの 3 つのコピーが保持されます。

### データベースの復元

データベースの復元は、以前の任意の時点にデータベースが復元されるように設計されます。Azure SQL Data Warehouse サービスは、少なくとも 8 時間ごとにストレージのスナップショットを自動的に取得し、それらを 7 日間保持して復元ポイントの個別セットを用意することで、すべてのデータベースを保護します。自動スナップショットおよび復元機能では、管理作業ゼロで、偶発的な破損や削除からデータベースを保護します。データベースの復元の詳細については、[データベースの復元作業][]に関するページをご覧ください。

## 次のステップ
他の重要な管理タスクについては、[管理の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Azure storage redundancy options]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Backup and restore tasks]: sql-data-warehouse-database-restore-portal.md
[管理の概要]: sql-data-warehouse-overview-management.md
[データベースの復元作業]: sql-data-warehouse-manage-database-restore-portal.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0608_2016-->