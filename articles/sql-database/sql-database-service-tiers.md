---
title: "SQL Database のパフォーマンス: サービス レベル | Microsoft Docs"
description: "SQL Database のサービス レベルを比較します。"
keywords: "データベース オプション, データベース パフォーマンス"
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/09/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 920143756a0e0b369cf839681f9c4308f77a4af0


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>SQL Database のオプションとパフォーマンス: 各サービス レベルで使用できる内容について理解する
[Azure SQL Database](sql-database-technical-overview.md) は、さまざまなワークロードに対応するため、3 つのサービス レベル (**Basic**、**Standard**、**Premium**) といくつかのパフォーマンス レベルが用意されています。 パフォーマンス レベルが高くなるほど提供されるリソースが増加し、スループットが段階的に高くなるように設計されています。 ダウンタイムを発生させることなく、[サービス レベルとパフォーマンス レベルは動的に](sql-database-service-tiers.md)変更できます。 Basic、Standard、Premium のサービス レベルは、いずれも稼働時間の SLA が 99.99% で、柔軟なビジネス継続性のオプション、セキュリティ機能、時間単位の課金体系が用意されています。 

選択した[パフォーマンス レベル](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)で、専用リソースを含む単一データベースを作成できます。 また、リソースがデータベース間で共有される[エラスティック プール](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)内で複数のデータベースを管理することもできます。 単一データベースで利用できるリソースは、データベース トランザクション ユニット (DTU) を単位として表現されます。これに対して、エラスティック プールで利用できるリソースは、エラスティック DTU (eDTU) を単位として表現されます。 DTU と eDTU の詳細については、[DTU の概要](sql-database-what-is-a-dtu.md)に関する記事を参照してください。 

どちらの場合も、サービス レベルには **Basic**、**Standard**、**Premium** があります。 

## <a name="choosing-a-service-tier"></a>サービス階層の選択
次の表では、各種のアプリケーション ワークロードに最適なサービス階層の例を示します。

| サービス階層 | 対象のワークロード |
| :--- | --- |
| **Basic** | 小規模なデータベースに最適です。通常は、一度に&1; つのアクティブな操作をサポートします。 たとえば、開発やテスト、使用頻度の低い小規模なアプリケーションなどに使用するデータベースがこれに該当します。 |
| **Standard** |IO パフォーマンス要件が低～中程度のクラウド アプリケーションに適しています。複数の同時クエリをサポートします。 たとえば、ワークグループや Web アプリケーションなどです。 |
| **Premium** | IO パフォーマンス要件が高く、トランザクション量が膨大な場合に合わせて設計されています。多数の同時ユーザーをサポートしています。 たとえば、ミッション クリティカルなアプリケーションをサポートするデータベースなどです。 |

最初に、単一データベースを実行するかどうか、またはリソースを共有するデータベースをグループ化するかどうかを決定します。 [エラスティック プールの考慮事項](sql-database-elastic-pool-guidance.md)を確認してください。 サービス レベルを決定するには、まず、最低限必要なデータベース機能を確認します。

* 個々のデータベースの最大データベース サイズ (ハイエンドのパフォーマンス レベルの場合、Basic では最大 2 GB、Standard では最大 250 GB、Premium では最大 500 GB ～ 1 TB)
* エラスティック プールの最大合計ストレージ (Basic では 117 GB、Standard では 1,200 GB、Premium では 750 GB)
* プールあたりのデータベースの最大数 (Basic では 400 個、Standard では 400 個、Premium では 50 個)
* データベース バックアップの保有期間 (Basic では 7 日間、Standard と Premium では 35 日間)

最小限のサービス レベルを決定したら、データベースのパフォーマンス レベル (DTU 数) を決定できるようになります。 多くの場合、出発点として Standard S2 および S3 というパフォーマンス レベルが適しています。 CPU または IO の要件が高いデータベースの場合は、Premium のパフォーマンス レベルが出発点として適しています。 Premium では、より多くの CPU が提供されるため、最も高い Standard パフォーマンス レベルと比較して 10 倍超える IO から始まります。

## <a name="single-database-service-tiers-and-performance-levels"></a>Single Database サービス階層とパフォーマンス レベル
単一データベースでは、各サービス レベル内に複数のパフォーマンス レベルがあります。 ワークロードのニーズに最適なレベルを選択できる柔軟性があります。 スケールアップやスケールダウンが必要な場合は、データベースの階層を簡単に変更することができます。 詳細については、「 [データベースのサービス階層とパフォーマンス レベルの変更](sql-database-service-tiers.md) 」を参照してください。

ホストされるデータベースの数にかかわらず、データベースは所定のリソースを取得します。データベースの期待されるパフォーマンス特性には影響しません。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> このサービス層テーブルのその他すべての行の詳細については、「 [サービス層の機能と制限](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)」を参照してください。
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>1 つのデータベースのスケールアップとスケールダウン

最初にサービス レベルとパフォーマンス レベルを選んだ後、実際の使用感に基づいて、1 つのデータベースを動的にスケールアップまたはスケールダウンすることができます。 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

データベースのサービス レベルやパフォーマンス レベルを変更すると、新しいパフォーマンス レベルで元のデータベースのレプリカが作成され、接続先がそのレプリカに切り替えられます。 このプロセスでデータが失われることはありませんが、レプリカに切り替えるほんの少しの間、データベースに接続できなくなるため、実行中の一部トランザクションがロールバックされる場合があります。 この時間はさまざまですが、平均 4 秒以内であり、99% 以上が 30 秒未満です。 接続が無効になった時点で多数のトランザクションが実行中の場合、この時間が長引くことがあります。  

スケールアップ プロセス全体の継続時間は、変更前後のデータベースのサイズとサービス レベルによって異なります。 たとえば、250 GB のデータベースを Standard サービス レベルとの間または Standard サービス レベル内で変更する場合は、6 時間以内に完了します。 Premium サービス レベル内で同じサイズのデータベースのパフォーマンス レベルを変更する場合は、3 時間以内で完了します。

* データベースをダウングレードするには、データベースがダウングレード後のサービス階層で許可されている最大サイズより小さい必要があります。 
* [geo レプリケーション](sql-database-geo-replication-portal.md) を有効にしてデータベースをアップグレードする場合、まず、そのセカンダリ データベースを目的のパフォーマンス レベルにアップグレードしてから、プライマリ データベースをアップグレードする必要があります。
* Premium サービス階層からダウングレードするときは、最初に geo レプリケーション リレーションシップをすべて終了する必要があります。 「 [Azue SQL Database を障害から回復する](sql-database-disaster-recovery.md) 」に記載されている手順に従って、プライマリ データベースとアクティブなセカンダリ データベース間のレプリケーション プロセスを停止できます。
* サービス階層によって、提供されている復元サービスは異なります。 ダウングレードすると、特定の時点に復元する機能を使えなくなったり、バックアップの保存期間が短くなったりする可能性があります。 詳細については、「 [Azure SQL Database のバックアップと復元](sql-database-business-continuity.md)」を参照してください。
* データベースに対する新しいプロパティは、変更が完了するまで適用されません。

> [!IMPORTANT]
> 詳細な手順については、[Azure Portal を使用した&1; つのデータベースの管理](sql-database-manage-single-databases-portal.md)、[Powershell を使用した&1; つのデータベースの管理](sql-database-manage-single-databases-powershell.md)、[Transact-SQL を使用した&1; つのデータベースの管理](sql-database-manage-single-databases-tsql.md)に関する記事をそれぞれ参照してください。
>

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>エラスティック プール サービス階層と eDTU のパフォーマンス

プールでは、データベースが eDTU リソースを共有して使用することができ、プール内の各データベースに特定のパフォーマンス レベルを割り当てる必要はありません。 たとえば、Standard プール内の単一データベースで使用できるのは、0 eDTU から、プールの構成時に設定した最大データベース eDTU までとなります。 プールでは、多様なワークロードを持つ複数のデータベースが、プール全体で利用可能な eDTU リソースを効率的に使用できます。 詳細については、 [エラスティック プールの価格およびパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md) に関する記事を参照してください。

次の表では、プールのサービス階層の特性について説明します。

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

プール内の各データベースは、該当するレベルにおける単一データベースの特性にも従います。 たとえば、Basic プールでは、プールあたりの最大セッション数が 4,800 ～ 28,800 の範囲に制限されます。ただし、Basic プール内の個々のデータベースでは、データベースあたりのセッション数が 300 に制限されます。

## <a name="scaling-up-or-scaling-down-an-elastic-pool"></a>エラスティック プールのスケールアップとスケールダウン

最初にサービス レベルとパフォーマンス レベルを選んだ後、実際の使用感に基づいて、エラスティック プールを動的にスケールアップまたはスケールダウンすることができます。 

* 通常、データベースあたりの最小 eDTU またはデータベースあたりの最大 eDTU の変更は、5 分以内で完了します。
* プール サイズ (eDTU) の変更時間は、プール内のすべてのデータベースを結合したサイズに依存します。 変更の平均時間は、100 GB あたり 90 分以下です。 たとえば、プール内のすべてのデータベースの合計領域が 200 GB の場合、プールあたりの eDTU の変更にかかる想定待機時間は、3 時間以下になります。

> [!IMPORTANT]
> 詳細な手順については、[Azure Portal を使用したエラスティック プールの管理](sql-database-elastic-pool-manage-portal.md)、[Powershell を使用したエラスティック プールの管理](sql-database-elastic-pool-manage-powershell.md)、[Transact-SQL を使用したエラスティック プールの管理](sql-database-elastic-pool-manage-tsql.md)、[C# を使用したエラスティック プールの管理](sql-database-elastic-pool-manage-csharp.md)に関する記事をそれぞれ参照してください。
>

## <a name="next-steps"></a>次のステップ

* [エラスティック プール](sql-database-elastic-pool-guidance.md)と、[エラスティック プールの価格とパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)の詳細を確認してください。
* [エラスティック プールの監視、管理、およびサイズ変更](sql-database-elastic-pool-manage-portal.md)と[単一データベースのパフォーマンスの監視](sql-database-single-database-monitor.md)を行う方法を確認してください。
* SQL Database の階層について理解できたら、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)で実際に試してみてください。また、[初めて SQL データベースを作成するにあたっての方法](sql-database-get-started.md)を確認してください。
* 移行のシナリオの場合は、[DTU Calculator](http://dtucalculator.azurewebsites.net/) を使用して必要な DTU 数を見積もります。 




<!--HONumber=Feb17_HO3-->


