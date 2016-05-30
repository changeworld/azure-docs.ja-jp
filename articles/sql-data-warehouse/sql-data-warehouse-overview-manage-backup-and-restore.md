<properties
   pageTitle="Azure SQL Data Warehouse でのデータベースのバックアップと復元 (概要) | Microsoft Azure"
   description="Azure SQL Data Warehouse でデータベースを復旧するためのバックアップおよび復元オプションの概要。"
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
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>


# Azure SQL Data Warehouse でのデータベースのバックアップと復元 (概要)

> [AZURE.SELECTOR]
- [概要](sql-data-warehouse-overview-manage-backup-and-restore.md)
- [ポータル](sql-data-warehouse-manage-backup-and-restore-tasks-portal.md)
- [PowerShell](sql-data-warehouse-manage-backup-and-restore-tasks-powershell.md)
- [REST ()](sql-data-warehouse-manage-backup-and-restore-tasks-rest-api.md)

この記事では、SQL Data Warehouse でビジネス継続性と障害復旧を計画するための基本事項について説明します。データ ウェアハウスは、ビジネス情報の中央リポジトリであり、あらゆる規模の組織での分析やビジネス インテリジェンスに関する毎日の業務において重要な役割を果たします。このため、データ ウェアハウスは信頼でき、回復性を持ち、継続的に操作できることが不可欠です。特にこの記事では、SQL Data Warehouse でデータベースの復元や geo リストアを使用してユーザーのエラーや障害から回復する方法を説明します。

## 復旧計画 

ビジネス持続性と障害復旧計画は、次を最適化するために必要です。

**目標復旧時間 (RTO):** 障害中の可用性の最大損失など、破壊的なイベントの後にデータベースが完全に復旧するまでの最大許容時間。

**目標復旧時点 (RPO):** 障害中の可用性の最大損失など、破壊的なイベントからデータベースが復旧するときの更新データの損失の最大許容時間。

**推定復旧時間 (ERT):** 復元の要求の後にデータベースが完全に機能するようになる推定時間。

詳細については、[SQL Database のビジネス継続性の概要][]に関するページをご覧ください。

## 復旧のシナリオ

**インフラストラクチャの障害からの回復:** このシナリオは、ディスク障害などのインフラストラクチャ問題からの回復を示します。お客様は、フォールト トレラントで、可用性の高いインフラストラクチャによってビジネス継続性を実現したいと考えます。

**ユーザー エラーからの回復:** このシナリオは、意図しない、または偶発的なデータの破損または削除からの回復を示します。ユーザーが意図せずに、または偶発的にデータを変更または削除した場合、顧客は以前の任意の時点にデータベースを復元することで、ビジネスの継続性を確保したいと考えます。

**災害からの回復 (DR):** このシナリオは、大災害からの回復を示します。自然災害や地域的な障害などの破壊的な事象によってデータベースを使用できなくなるシナリオでは、お客様は別のリージョンでデータベースを回復し、業務を続行したいと考えます。


## バックアップと復元の機能

前述のシナリオで、SQL Data Warehouse によってデータベースの信頼性を高め、回復性と継続的な操作を行うことができるようにする方法について説明します。


### データの冗長性

SQL Data Warehouse ではコンピューティングとストレージが分離され、すべてのデータが geo 冗長 Azure Storage (RA-GRS) に直接書き込まれます。地理冗長ストレージでは、プライマリ リージョンから数百マイル離れたセカンダリ リージョンにデータがレプリケートされます。プライマリとセカンダリの両方のリージョンで、データは別個のフォールト ドメインとアップグレード ドメインの間でそれぞれ 3 回レプリケートされます。これにより、いずれかのリージョンが使用できなくなるような完全な地域的障害や災害が発生した場合でも、データの永続性が確保されます。読み取りアクセス地理冗長ストレージの詳細については、「[Azure storage redundancy options (Azure ストレージ冗長オプション)][]」を参照してください。

### データベースの復元

データベースの復元は、以前の任意の時点にデータベースが復元されるように設計されます。Azure SQL Data Warehouse サービスは、少なくとも 8 時間ごとにストレージのスナップショットを自動的に取得し、それらを 7 日間保持して復元ポイントの個別セットを用意することで、すべてのデータベースを保護します。これらのバックアップは RA-GRS Azure Storage に保存されるため、規定で地理冗長になります。自動バックアップおよび復元機能では、管理作業ゼロで、偶発的な破損や削除からデータベースを保護します。データベースの復元の詳細については、[バックアップおよび復元作業][]に関するページをご覧ください。

### 地理リストア

geo リストアは、破壊的なイベントが原因でデータベースを使用できなくなった場合にデータベースを回復するように設計されています。バックアップは geo 冗長であるため、災害によってデータベースにアクセスできない場合でも、それを使用してデータベースを回復できます。復元されたデータベースは任意の Azure リージョンの任意のサーバーに作成できます。geo リストアは、障害からの復旧以外に、データベースを別のサーバーやリージョンに移行またはコピーするなど、他のシナリオにも使用できます。

**復旧操作を開始するタイミング** 復旧操作を行う場合、復旧時に SQL 接続文字列を変更する必要があり、結果としてデータが完全に失われる可能性があります。そのため、障害がアプリケーションの RTO よりも長くかかる可能性が高い場合にのみ行ってください。次のデータ ポイントを使用して、復旧が保証されていることを確認してください。

- データベースへの永続的な接続エラー。
- Azure ポータルは、幅広い影響のあるリージョンでのインシデントに関するアラートを示します。


## 次のステップ
他の重要な管理タスクについては、[管理の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Azure storage redundancy options (Azure ストレージ冗長オプション)]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[バックアップおよび復元作業]: sql-data-warehouse-backup-and-restore-tasks-portal.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md
[管理の概要]: sql-data-warehouse-overview-management.md
[SQL Database のビジネス継続性の概要]: ../sql-database/sql-database-business-continuity.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->