---
title: 'SQL Database: DTU とは  | Microsoft Docs'
description: Azure SQL Database トランザクション ユニットについて説明します。
keywords: データベース オプション, データベース パフォーマンス
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 3fb0d0f73f475e246580f7ea6e4aea60b069c230
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894009"
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>データベース トランザクション ユニット (DTU) とエラスティック データベース トランザクション ユニット (eDTU)
この記事では、データベース トランザクション ユニット (DTU) とエラスティック データベース トランザクション ユニット (eDTU) について説明します。また、DTU または eDTU が最大数に達した場合に何が起こるかについても解説します。 詳細な料金情報については、[Azure SQL Database の料金](https://azure.microsoft.com/pricing/details/sql-database/single/)に関するページを参照してください。

## <a name="what-are-database-transaction-units-dtus"></a>データベース トランザクション ユニット (DTU) とは
[サービス層](sql-database-single-database-resources.md)内の特定のパフォーマンス レベルの単一の Azure SQL Database の場合、Microsoft では、そのデータベース (Azure クラウド内の他のデータベースから独立した) に対し、特定のレベルのリソースと予測可能なレベルのパフォーマンスの提供を保証します。 リソースの量は、データベース トランザクション ユニット (DTU) の数として計算され、コンピューティング、ストレージ、および IO リソースのバンドルされたメジャーです。 これらのリソース間の比率は、最初に一般的な現実の OLTP ワークロードとして設計された [OLTP ベンチマーク ワークロード](sql-database-benchmark-overview.md)によって特定されます。 ワークロードがこれらのいずれかのリソースの量を超えると、スループットが調整され、パフォーマンスが低下し、タイムアウトが発生します。 ワークロードによって使用されるリソースは、Azure クラウド内の他の SQL Database で使用できるリソースには影響せず、他のワークロードによって使用されるリソースは、SQL Database で使用できるリソースに影響しません。

![境界ボックス](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTU は、さまざまなパフォーマンス レベルとサービス層の Azure SQL Database 間のリソースの相対量を理解するために最も役立ちます。 たとえば、データベースのパフォーマンス レベルを上げて DTU を 2 倍にすると、そのデータベースが利用できるリソースを 2 倍にしたのと同じ効果が得られます。 たとえば、DTU が 1750 である Premium P11 データベースは、DTU が 5 である Basic データベースと比べ、DTU 換算で 350 倍の計算能力を持ちます。  

ワークロードのリソース (DTU) の使用量の詳細を把握するには、[Azure SQL Database Query Performance Insight](sql-database-query-performance.md) を使用して、次の操作を実行します。

- パフォーマンス向上のためのチューニングの対象となる可能性がある CPU/期間/実行回数の上位クエリを特定します。 たとえば、IO 負荷の高いクエリは、特定のサービス層とパフォーマンス レベルで使用可能なメモリを効率的に使用するために、[メモリ内の最適化手法](sql-database-in-memory.md)を使用すると利点が得られる可能性があります。
- クエリの詳細にドリルダウンして、そのテキストやリソース使用率の履歴を表示します。
- [SQL Database Advisor](sql-database-advisor.md) によって実行されるアクションを示すアクセス パフォーマンス チューニングの推奨事項。

[DTU サービス層](sql-database-service-tiers-dtu.md)はいつでも変更することが可能で、アプリケーションのダウンタイムも最小限に留められます (通常、平均で 4 秒未満)。 特に使用パターンが比較的予測可能である場合、多くのビジネスとアプリについては、データベースを作成し、要求に応じてパフォーマンスを調整する能力は十分です。 しかし、使用パターンが予測できない場合、コストおよびビジネス モデルを管理するのが難しくなる可能性があります。 このシナリオでは、プール内の複数のデータベース間で共有される特定の数の DTU でエラスティック プールを使用します。

![Intro to SQL Database: Single database DTUs by tier and level](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>エラスティック データベース トランザクション ユニット (eDTU) とは
常に使用できる SQL Database にとって常に必要とは限らない専用のリソース (DTU) セットを提供する代わりに、SQL Database サーバー上の[エラスティック プール](sql-database-elastic-pool.md)に、これらのデータベース間でリソースのプールを共有するデータベースを配置できます。 エラスティック プール内の共有リソースは、エラスティック データベース トランザクション ユニット (eDTU) によって測定されます。 エラスティック プールは、多種多様な予測できない使用パターンを持つ複数のデータベースに対するパフォーマンス目標を管理するための、簡単でコスト効率に優れたソリューションを提供します。 エラスティック プールは、プール内の 1 つのデータベースによってリソースを消費できないことを保証する一方で、プール内の各データベースが常に、最低限必要な量の利用可能なリソースを持っていることを保証します。 

![Intro to SQL Database: eDTUs by tier and level](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

プールには、設定価格に合わせて、設定された eDTU 数が与えられます。 エラスティック プール内の個々のデータベースには、構成された境界内で自動スケールを行う柔軟性が与えられます。 データベースの負荷が増加すると、eDTU の消費量を増やして需要に対応します。 データベースの負荷が減少すると、eDTU の消費が減ります。 負荷のないデータベースは eDTU を消費しません。 データベースごとではなく、プール全体のリソースをプロビジョニングすることにより、管理タスクが簡素化され、プールの予算が予測可能になります。

データベースを停止せず、プール内のデータベースに影響を及ぼさずに、既存のプールに eDTU を追加できます。 同様に、不要になった eDTU は、いつでも既存のプールから削除できます。 プールのデータベースを増減できるほか、負荷が大きいときにデータベースが使用できる eDTU の量を制限して、他のデータベース用の eDTU を確保することもできます。 データベースのリソース使用率が低いと予測できる場合は、プールから削除して、予測可能な量の必要リソースを備えた単一データベースとして構成できます。

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>ワークロードで必要とされる DTU の数を判断する方法
既に存在するオンプレミスのワークロードや、SQL Server 仮想マシンのワークロードを Azure SQL Database に移行することを検討している場合には、[DTU Calculator](http://dtucalculator.azurewebsites.net/) を使用すると、必要な DTU のおおよその数がわかります。 既存の Azure SQL Database ワークロードについては、[SQL Database Query Performance Insight](sql-database-query-performance.md) を使えばデータベース リソースの消費量 (DTU) を把握でき、ワークロードを最適化するための深い洞察が得られます。 このほか、[sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV を使用して、過去 1 時間のリソース消費量を確認することもできます。 また、カタログ ビュー [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) は過去 14 日間のリソース消費量を表示しますが、こちらはデータの精度がやや低く、5 分間の平均となります。

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>リソースのエラスティック プールを使うメリット
プールは、多数のデータベースが一定のパターンで使用されている場合に適しています。 あるデータベースは、使用が急増することはあまりなく、使用量平均が低いパターンの特徴を持っています。 SQL Database は、既存の SQL Database サーバー内にあるデータベースのリソース使用量の履歴を自動的に評価し、Azure ポータルでのプールの適切な構成を推奨します。 詳細については、「[エラスティック プールの使用に適した状況](sql-database-elastic-pool.md)」を参照してください。

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>DTU が最大数に達した場合に起こること
選択したサービス層/パフォーマンス レベルで許可されている最大までデータベース ワークロードを実行するために必要なリソースを提供できるよう、パフォーマンス レベルが調整、制御されます。 ワークロードが CPU/データ IO/ログ IO いずれかの制限に達した場合でも、許可されている最大レベルのリソースを引き続き利用できますが、クエリの遅延が増加する可能性が高くなります。 上限に達してもエラーにはなりませんが、ワークロードが遅くなり、遅延が深刻になった場合はクエリのタイムアウトが発生するようになります。許可される同時ユーザー セッション/要求 (ワーカー スレッド) の上限に達した場合、明示的なエラーが発生します。 CPU、メモリ、データ IO、またはトランザクション ログ IO に関係しないリソース制限については、「[Azure SQL Database resource limits]( sql-database-dtu-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached)」をご覧ください。

## <a name="next-steps"></a>次の手順
* 単一データベースとエラスティック プールで使用できる DTU と eDTU、および CPU、メモリ、データ IO、トランザクション ログ IO 以外のリソースの制限については、「[DTU-based purchasing model](sql-database-service-tiers-dtu.md)」をご覧ください。
* 仮想コアに基づいたリソース割り当てとサービス層については、「[仮想コアベースの購入モデル (プレビュー)](sql-database-service-tiers-vcore.md)」をご覧ください。 
* (DTU の) 消費量を確認する場合には、「 [SQL Database Query Performance Insight](sql-database-query-performance.md) 」を参照してください。
* DTU の各構成要素の割合を決める際に使用する OLTP ワークロードの裏で具体的にどのような方法論が採用されているかについては、「 [Azure SQL Database ベンチマークの概要](sql-database-benchmark-overview.md) 」を参照してください。
