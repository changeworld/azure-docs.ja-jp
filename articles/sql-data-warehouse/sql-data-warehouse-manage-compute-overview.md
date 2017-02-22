---
title: "Azure SQL Data Warehouse のコンピューティング能力の管理 (概要) | Microsoft Docs"
description: "Azure SQL Data Warehouse のパフォーマンス スケールアウト機能。 DWU を調整してスケールアウトする方法や、コンピューティング リソースを一時停止および再開してコストを節約する方法を説明します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5d3bcc3c1434b16279778573ccf3034f9ac28a4d
ms.openlocfilehash: 6871ab3bc25ab3ec7b3c60852aa06bee047d8e9a


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Azure SQL Data Warehouse のコンピューティング能力の管理 (概要)
> [!div class="op_single_selector"]
> * [概要](sql-data-warehouse-manage-compute-overview.md)
> * [ポータル](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST ()](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

SQL Data Warehouse のアーキテクチャではストレージとコンピューティングを分離して、それぞれを個別にスケーリングできます。 その結果、パフォーマンスのスケールアウト時に、必要な分だけのパフォーマンスにかかる料金を支払うことで、コストを節約できます。

この概要では、SQL Data Warehouse の次のパフォーマンス スケールアウト機能について説明し、これらの使用方法、使用するタイミングにおける推奨事項を紹介します。

* [Data Warehouse ユニット (DWU)][data warehouse units (DWUs)] の調整によるコンピューティング能力のスケーリング
* コンピューティング リソースの一時停止または再開

<a name="scale-performance-bk"></a>

## <a name="scale-performance"></a>パフォーマンスのスケーリング
SQL Data Warehouse では、CPU、メモリ、および I/O 帯域幅のコンピューティング リソースを増減させることにより、パフォーマンスの拡張と縮小をすばやく行うことができます。 パフォーマンスのスケーリングに必要なのは、SQL Data Warehouse からデータベースに割り当てられている [Data Warehouse ユニット (DWU)][data warehouse units (DWUs)] の数を調整することだけです。 SQL Data Warehouse がすばやく変更を行い、ハードウェアやソフトウェアへの基本的な変更をすべて処理します。

データ ウェアハウスで優れたパフォーマンスを実現するために必要なストレージのタイプ、メモリ量、およびプロセッサのタイプを調査する必要があったのは遠い過去のことになりました。 データ ウェアハウスをクラウドに置くことにより、低レベルのハードウェア問題に対処する必要はなくなりました。 その代わりに、SQL Data Warehouse ではデータをどの程度高速に分析する必要があるかということが検討事項になります。

### <a name="how-do-i-scale-performance"></a>パフォーマンスをスケーリングする方法
コンピューティング能力を臨機応変に調整するには、データベースの [Data Warehouse ユニット (DWU)][data warehouse units (DWUs)] 設定を変更します。 DWU を追加すると、パフォーマンスが直線的に向上します。  より高いレベルの DWU では、パフォーマンスに大幅な改善が見られるには、100 個以上の DWU を追加する必要があります。 DWU で意味のある改善を選択できるように、最良の結果が得られる DWU レベルが用意されています。

DWU を調整するのには、これらの各方法をどれでも使用できます。

* [Azure Portal を使用したコンピューティング能力のスケーリング][Scale compute power with Azure portal]
* [PowerShell を使用したコンピューティング能力のスケーリング][Scale compute power with PowerShell]
* [REST API を使用したコンピューティング能力のスケーリング][Scale compute power with REST APIs]
* [TSQL を使用したコンピューティング能力のスケーリング][Scale compute power with TSQL]

### <a name="how-many-dwus-should-i-use"></a>必要な DWU の数
SQL Data Warehouse ではパフォーマンスは線形にスケールし、あるコンピューティング スケールから別のコンピューティング スケールへの変化 (100 個の DWU から 2000 個の DWU への変化など) は数秒で実行されます。 これにより、さまざまな DWU 設定で試してから、シナリオに最適なものを選ぶことができます。

理想的な DWU 値を把握するには、データ読み込みの後で、拡大/縮小を行い、いくつかのクエリを実行してください。 スケーリングは簡単に行えるので、1 時間以内でさまざまなレベルのパフォーマンスを多数試すことができます。 SQL Data Warehouse は、大量のデータを処理することで、スケーリングの本当の機能を確認するように設計されています。特に、ここで提供する大規模環境では、1 TB に近い、あるいはそれ以上の大規模なデータを使用してください。

ワークロードに最適な DWU を特定する際の推奨事項:

1. 開発中のデータ ウェアハウスの場合は、少ない数の DWU を選択することから始めます。  手始めとしては、DW400 または DW200 が適しています。
2. アプリケーションのパフォーマンスを監視し、選択した DWU の数に対するパフォーマンスの変化を観察します。
3. 線形スケールを想定して、要件に対して最適なパフォーマンス レベルに到達するために、パフォーマンスをどの程度上下する必要があるかを判別します。
4. どのくらい速くまたは遅くワークロードを実行するかに応じて、DWU の数を増減します。 サービスはすぐに反応し、新しい DWU 要件を満たすようにコンピューティング リソースが調整されます。
5. ビジネス要件に応じた最適なパフォーマンス レベルに到達するまで調整を行います。

### <a name="when-should-i-scale-dwus"></a>DWU をスケーリングするタイミング
より短時間で結果を得る必要がある場合は DWU を増やし、増加された DWU に対して支払いを行います。  それほど高いコンピューティング能力が必要ではない場合は、DWU を減らし、小さな DWU に対して支払いを行います。

DWU をスケーリングするタイミングを特定するための推奨事項:

1. アプリケーションのワークロードが変動する場合は、ワークロードが高い時点と低い時点に対応するように DWU レベルをスケールアップまたはスケールダウンします。 たとえば、月末にワークロードがピークに達することが多い場合は、ピークに達する日には DWU をさらに追加し、ピーク期間が終わったらスケールダウンするように計画します。
2. 大量データの読み込みまたは変換操作を実行する前に、データが短時間で使用可能になるように DWU をスケールアップします。

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>コンピューティングの一時停止
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

データベースを一時停止するには、次のいずれかの方法を使用できます。

* [Azure Portal を使用したコンピューティングの一時停止][Pause compute with Azure portal]
* [PowerShell を使用したコンピューティングの一時停止][Pause compute with PowerShell]
* [REST API を使用したコンピューティングの一時停止][Pause compute with REST APIs]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>コンピューティングの再開
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

データベースを再開するには、次のいずれかの方法を使用できます。

* [Azure Portal を使用したコンピューティングの再開][Resume compute with Azure portal]
* [PowerShell を使用したコンピューティングの再開][Resume compute with PowerShell]
* [REST API を使用したコンピューティングの再開][Resume compute with REST APIs]

## <a name="permissions"></a>アクセス許可
データベースをスケーリングするには、[ALTER DATABASE][ALTER DATABASE] に関するページで説明されているアクセス許可が必要です。  一時停止と再開には、[SQL DB の共同作成者][SQL DB Contributor]のアクセス許可、具体的には Microsoft.Sql/servers/databases/action が必要です。

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>次のステップ
その他の主要なパフォーマンスの概念を理解するために、次の記事を参照してください。

* [ワークロードと同時実行の管理][Workload and concurrency managment]
* [テーブル設計の概要][Table design overview]
* [テーブルの分散][Table distribution]
* [テーブルのインデックス作成][Table indexing]
* [テーブルのパーティション分割][Table partitioning]
* [テーブルの統計][Table statistics]
* [ベスト プラクティス][Best practices]

<!--Image reference-->

<!--Article references-->
[data warehouse units (DWUs)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

[Scale compute power with Azure portal]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[Scale compute power with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scale compute power with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scale compute power with TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Pause compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Pause compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pause compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Resume compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Resume compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Resume compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Workload and concurrency managment]: ./sql-data-warehouse-develop-concurrency.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/



<!--HONumber=Dec16_HO1-->


