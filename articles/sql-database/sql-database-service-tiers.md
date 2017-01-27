---
title: "SQL Database のパフォーマンス: サービス レベル | Microsoft Docs"
description: "SQL Database のサービス レベルを比較します。"
keywords: "データベース オプション, データベース パフォーマンス"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/11/2017
ms.author: carlrab; janeng
translationtype: Human Translation
ms.sourcegitcommit: 0a00aff343bfd31c956f6cbc831e89cc1cc84b23
ms.openlocfilehash: 95ae4bd67b7d08755035e7b5559ca9648d45bdaa


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>SQL Database のオプションとパフォーマンス: 各サービス レベルで使用できる内容について理解する
[Azure SQL Database](sql-database-technical-overview.md) は、さまざまなワークロードに対応するため、3 つのサービス レベル (**Basic**、**Standard**、**Premium**) といくつかのパフォーマンス レベルが用意されています。 パフォーマンス レベルが高くなるほど提供されるリソースが増加し、スループットが段階的に高くなるように設計されています。 ダウンタイムを発生させることなく、[サービス レベルとパフォーマンス レベルは動的に](sql-database-scale-up.md)変更できます。 Basic、Standard、Premium のサービス レベルは、いずれも稼働時間の SLA が 99.99% で、柔軟なビジネス継続性のオプション、セキュリティ機能、時間単位の課金体系が用意されています。 

選択した[パフォーマンス レベル](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)で、専用リソースを含む単一データベースを作成できます。 また、リソースがデータベース間で共有される[エラスティック プール](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)内で複数のデータベースを管理することもできます。 単一データベースで利用できるリソースは、データベース トランザクション ユニット (DTU) を単位として表現されます。これに対して、エラスティック プールで利用できるリソースは、エラスティック DTU (eDTU) を単位として表現されます。 DTU と eDTU については、[DTU の概要](sql-database-what-is-a-dtu.md)に関する記事を参照してください。 

どちらの場合も、サービス レベルには **Basic**、**Standard**、**Premium** があります。 

## <a name="choosing-a-service-tier"></a>サービス階層の選択
次の表では、各種のアプリケーション ワークロードに最適なサービス階層の例を示します。

| サービス階層 | 対象のワークロード |
| :--- | --- |
| **Basic** | 小規模なデータベースに最適です。通常は、一度に 1 つのアクティブな操作をサポートします。 たとえば、開発やテスト、使用頻度の低い小規模なアプリケーションなどに使用するデータベースがこれに該当します。 |
| **Standard** |IO パフォーマンス要件が低～中程度のクラウド アプリケーションに適しています。複数の同時クエリをサポートします。 たとえば、ワークグループや Web アプリケーションなどです。 |
| **Premium** | IO パフォーマンス要件が高く、トランザクション量が膨大な場合に合わせて設計されています。多数の同時ユーザーをサポートしています。 たとえば、ミッション クリティカルなアプリケーションをサポートするデータベースなどです。 |

最初に、単一データベースを実行するかどうか、またはリソースを共有するデータベースをグループ化するかどうかを決定します。 [エラスティック プールの考慮事項](sql-database-elastic-pool-guidance.md)を確認してください。 サービス レベルを決定するには、まず、最低限必要なデータベース機能を確認します。

* 個々のデータベースの最大データベース サイズ (ハイエンドのパフォーマンス レベルの場合、Basic では最大 2 GB、Standard では最大 250 GB、Premium では最大 500 GB ～ 1 TB)
* エラスティック プールに備えた最大合計ストレージ (Basic では 117 GB、Standard では 1200 GB、Premium では 750 GB)
* プールあたりのデータベースの最大数 (Basic では 400 個、Standard では 400 個、Premium では 50 個)
* データベース バックアップの保有期間 (Basic では 7 日間、Standard と Premium では 35 日間)

最小限のサービス レベルを決定したら、データベースのパフォーマンス レベル (DTU 数) を決定できるようになります。 多くの場合、出発点として Standard S2 および S3 というパフォーマンス レベルが適しています。 CPU または IO の要件が高いデータベースの場合は、Premium のパフォーマンス レベルが出発点として適しています。 Premium では、より多くの CPU が提供されるため、最も高い Standard パフォーマンス レベルと比較して 10 倍超える IO から始まります。

最初にパフォーマンス レベルを選んだ後、実際の使用感に基づいて、[個々のデータベース](sql-database-scale-up.md)または[エラスティック プール](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool)を動的にスケールアップしたりスケールダウンしたりすることができます。 移行のシナリオの場合、[DTU Calculator](http://dtucalculator.azurewebsites.net/) を使用して、必要な DTU 数を見積もることもできます。 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

## <a name="single-database-service-tiers-and-performance-levels"></a>Single Database サービス階層とパフォーマンス レベル
単一データベースでは、各サービス レベル内に複数のパフォーマンス レベルがあります。 ワークロードのニーズに最適なレベルを選択できる柔軟性があります。 スケールアップやスケールダウンが必要な場合は、データベースの階層を簡単に変更することができます。 詳細については、「 [データベースのサービス階層とパフォーマンス レベルの変更](sql-database-scale-up.md) 」を参照してください。

ホストされるデータベースの数にかかわらず、データベースは所定のリソースを取得します。データベースの期待されるパフォーマンス特性には影響しません。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> このサービス層テーブルのその他すべての行の詳細については、「 [サービス層の機能と制限](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)」を参照してください。
> 

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>エラスティック プール サービス階層と eDTU のパフォーマンス

プールでは、データベースが eDTU リソースを共有して使用することができ、プール内の各データベースに特定のパフォーマンス レベルを割り当てる必要はありません。 たとえば、Standard プール内の単一データベースで使用できるのは、0 eDTU から、プールの構成時に設定した最大データベース eDTU までとなります。 プールでは、多様なワークロードを持つ複数のデータベースが、プール全体で利用可能な eDTU リソースを効率的に使用できます。 詳細については、 [エラスティック プールの価格およびパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md) に関する記事を参照してください。

次の表では、プールのサービス階層の特性について説明します。

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

プール内の各データベースは、該当するレベルにおける単一データベースの特性にも従います。 たとえば、Basic プールでは、プールあたりの最大セッション数が 4,800 ～ 28,800 の範囲に制限されます。ただし、Basic プール内の個々のデータベースでは、データベースあたりのセッション数が 300 に制限されます。


## <a name="next-steps"></a>次のステップ

* [エラスティック プール](sql-database-elastic-pool-guidance.md)と、[エラスティック プールの価格とパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)の詳細を確認してください。
* [エラスティック プールの監視、管理、およびサイズ変更](sql-database-elastic-pool-manage-portal.md)と[単一データベースのパフォーマンスの監視](sql-database-single-database-monitor.md)を行う方法を確認してください。
* SQL Database の階層について理解できたら、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)で実際に試してみてください。また、[初めて SQL データベースを作成するにあたっての方法](sql-database-get-started.md)を確認してください。




<!--HONumber=Jan17_HO2-->


