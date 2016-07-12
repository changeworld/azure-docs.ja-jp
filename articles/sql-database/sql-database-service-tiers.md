<properties
	pageTitle="SQL Database のパフォーマンスとオプション: サービス階層 | Microsoft Azure"
	description="サービス階層の SQL Database のパフォーマンスとビジネス継続性機能を比較して、実際の規模に合わせてコストと互換性のバランスを取ります。"
	keywords="データベース オプション, データベース パフォーマンス"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="07/05/2016"
	ms.author="carlrab"/>

# SQL Database のオプションとパフォーマンス: 各サービス階層で使用できる内容について理解します

[Azure SQL Database](sql-database-technical-overview.md) には、さまざまなワークロードを処理する複数のサービス階層があります。[サービス階層](sql-database-scale-up.md)はいつでも変更することが可能で、アプリケーションのダウンタイムも最小限に留められます (通常、平均で 4 秒未満)。また、定義済みの特性と価格を指定して[単一データベースを作成](sql-database-get-started.md)することもできます。または、[エラスティック データベース プールを作成して](sql-database-elastic-pool-create-portal.md)複数のデータベースを管理できます。いずれの場合も、階層には **Basic**、**Standard**、および **Premium** があります。これらの階層のデータベース オプションは、単一データベースとエラスティック プールの場合に似ていますが、エラスティック プールにはさらに考慮事項があります。この記事では、単一データベースとエラスティック データベースのサービス階層の詳細について説明します。

## サービス階層とデータベース オプション
Basic、Standard、および Premium のサービス階層は、いずれも稼働時間の SLA が 99.99% で、予測可能なパフォーマンス、柔軟なビジネス継続性のオプション、セキュリティ機能、1 時間単位の課金体系などが提供されます。次の表では、各種のアプリケーション ワークロードに最適なサービス階層の例を示します。

| サービス階層 | 対象のワークロード |
|---|---|
| **Basic** | 小規模なデータベースに最適です。通常は、一度に 1 つのアクティブな操作をサポートします。たとえば、開発やテスト、使用頻度の低い小規模なアプリケーションなどに使用するデータベースがこれに該当します。 |
| **Standard** | ほとんどのクラウド アプリケーションに適しています。同時に複数のクエリをサポートしています。たとえば、ワークグループや Web アプリケーションなどです。 |
| **Premium** | トランザクション量が多く、多数の同時ユーザーをサポートし、高度なビジネス継続性機能を必要とする場合に合わせて設計されています。たとえば、ミッション クリティカルなアプリケーションをサポートするデータベースなどです。 |

>[AZURE.NOTE] Web および Business エディションは使用中止となっています。Web エディションと Business エディションの使用を続行する場合は、[終了に関する FAQ](https://azure.microsoft.com/pricing/details/sql-database/web-business/) のページをお読みください。

## Single Database サービス階層とパフォーマンス レベル
単一データベースでは、各サービス階層内に複数のパフォーマンス レベルがあります。ワークロードのニーズに最適なレベルを選択できる柔軟性があります。スケールアップやスケールダウンが必要な場合は、データベースの階層を簡単に変更することができます。詳細については、「[データベースのサービス階層とパフォーマンス レベルの変更](sql-database-scale-up.md)」を参照してください。

ここに示されたパフォーマンス特性は、[SQL Database V12](sql-database-v12-whats-new.md) を使用して作成したデータベースに適用されます。Azure 内の基になるハードウェアに複数のデータベースをホストする場合も、データベースは、所定のリソースを取得します。データベースの期待されるパフォーマンス特性には影響しません。

[AZURE.INCLUDE [SQL DB のサービス階層表](../../includes/sql-database-service-tiers-table.md)]

DTU について理解を深めるためには、このトピックの[「DTU」セクション](#understanding-dtus)を参照してください。

>[AZURE.NOTE] このサービス層テーブルのその他すべての行の詳細については、「[サービス層の機能と制限](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)」を参照してください。

## エラスティック プール サービス階層と eDTU のパフォーマンス
単一データベースを作成したり、スケーリングしたりするだけでなく、[エラスティック プール](sql-database-elastic-pool.md)で複数のデータベースを管理することもできます。エラスティック プール内のデータベースはすべて、共通のリソース セットを共有します。パフォーマンス特性は、*エラスティック データベース トランザクション ユニット* (eDTU) で測定されます。単一データベースの場合と同様に、プールにも **Basic**、**Standard**、**Premium** という 3 つのサービス階層があります。プールの場合もやはり、この 3 つのサービス階層によってパフォーマンス全体に関する制限事項といくつかの機能が定義されます。

プールでは、エラスティック データベースは DTU リソースを共有して使用することができ、プール内のデータベースに特定のパフォーマンス レベルを割り当てる必要はありません。たとえば、Standard プール内の単一データベースで使用できるのは、0 eDTU から、プールの構成時に設定した最大データベース eDTU までとなります。これにより、プール全体に提供された eDTU リソースを、多様なワークロードを持つ複数のデータベースが効率的に使用することができます。詳細については、[エラスティック プールの価格およびパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)に関する記事を参照してください。

次の表では、プールのサービス階層の特性について説明します。

[AZURE.INCLUDE [エラスティック データベースの SQL DB サービス階層を示す表](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

プール内の各データベースはまた、該当する層における単一のデータベースの特性に従います。たとえば、Basic プールでは、プールあたりの最大セッションが 4800 ～ 28800 の範囲に制限されますが、このプール内の個々のデータベースの場合、データベースあたりセッションが 300 に制限されます (前のセクションで指定した単一の Basic データベースに対する制限)。

## DTU について

[AZURE.INCLUDE [SQL DB DTU の説明](../../includes/sql-database-understanding-dtus.md)]

## 次のステップ
- これらの階層の価格の詳細については、「[SQL Database 価格](https://azure.microsoft.com/pricing/details/sql-database/)」を参照してください。
- [エラスティック データベース プール](sql-database-elastic-pool-guidance.md)と、[エラスティック データベース プールの価格とパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)の詳細を確認してください。
- [エラスティック プールの監視、管理、およびサイズ変更](sql-database-elastic-pool-manage-portal.md)と[単一データベースのパフォーマンスの監視](sql-database-single-database-monitor.md)を行う方法を確認してください。
- SQL Database の階層について理解できたら、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)で実際に試してみてください。また、[初めて SQL データベースを作成するにあたっての方法](sql-database-get-started.md)を確認してください。

## その他のリソース

マルチテナント SaaS (サービスとしてのソフトウェア) データベース アプリケーションの一般的なデータ アーキテクチャ パターンについては、「[Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)」を参照してください。

<!---HONumber=AcomDC_0706_2016-->