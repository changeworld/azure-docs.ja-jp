<properties
   pageTitle="Azure SQL Data Warehouse のコンピューティング能力の管理 (概要) | Microsoft Azure"
   description="Azure SQL Data Warehouse のパフォーマンス スケールアウト機能。DWU を調整してスケールアウトする方法や、コンピューティング リソースを一時停止および再開してコストを節約する方法を説明します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/03/2016"
   ms.author="barbkess;sonyama"/>

# Azure SQL Data Warehouse のコンピューティング能力の管理 (概要)

> [AZURE.SELECTOR]
- [概要](sql-data-warehouse-manage-compute-overview.md)
- [ポータル](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST ()](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)

SQL Data Warehouse のアーキテクチャではストレージとコンピューティングを分離して、それぞれを個別にスケーリングできます。その結果、パフォーマンスのスケールアウト時に、必要な分だけのパフォーマンスにかかる料金を支払うことで、コストを節約できます。

この概要では、SQL Data Warehouse の次のパフォーマンス スケールアウト機能について説明し、これらの使用方法、使用するタイミングにおける推奨事項を紹介します。

- [Data Warehouse ユニット (DWU)][] の調整によるコンピューティング能力のスケーリング
- コンピューティング リソースの一時停止または再開

<a name="scale-performance-bk"></a>

## パフォーマンスのスケーリング

SQL Data Warehouse では、CPU、メモリ、および I/O 帯域幅のコンピューティング リソースを増減させることにより、パフォーマンスの拡張と縮小をすばやく行うことができます。パフォーマンスのスケーリングに必要なことは、SQL Data Warehouse からデータベースに割り当てられている [Data Warehouse ユニット (DWU)][] の数を調整するだけです。SQL Data Warehouse がすばやく変更を行い、ハードウェアやソフトウェアに対する基本的な変更をすべて処理します。

データ ウェアハウスで優れたパフォーマンスを実現するために必要なストレージのタイプ、メモリ量、およびプロセッサのタイプを調査する必要があったのは遠い過去のことになりました。データ ウェアハウスをクラウドに置くことにより、低レベルのハードウェア問題に対処する必要はなくなりました。その代わりに、SQL Data Warehouse ではデータをどの程度高速に分析する必要があるかということが検討事項になります。

### パフォーマンスをスケーリングする方法

コンピューティング能力を臨機応変に調整するには、データベースの [Data Warehouse ユニット (DWU)][] の設定を単に変更します。DWU を追加すると、パフォーマンスが直線的に向上します。より高いレベルの DWU では、パフォーマンスに大幅な改善が見られるには、100 個以上の DWU を追加する必要があります。DWU で意味のある改善を選択できるように、最良の結果が得られる DWU レベルが用意されています。
 
DWU を調整するのには、これらの各方法をどれでも使用できます。

- [Azure ポータルによるコンピューティング能力のスケーリング][]
- [PowerShell によるコンピューティング能力のスケーリング][]
- [REST API によるコンピューティング能力のスケーリング][]
- [TSQL によるコンピューティング能力のスケーリング][]

### 必要な DWU の数
 
SQL Data Warehouse ではパフォーマンスは線形にスケールし、あるコンピューティング スケールから別のコンピューティング スケールへの変化 (100 個の DWU から 2000 個 の DWU への変化など) は数秒で実行されます。これにより、さまざまな DWU 設定で試してから、シナリオに最適なものを選ぶことができます。

理想的な DWU 値を把握するには、データ読み込みの後で、拡大/縮小を行い、いくつかのクエリを実行してください。スケーリングは簡単に行えるので、1 時間以内でさまざまなレベルのパフォーマンスを多数試すことができます。SQL Data Warehouse は、大量のデータを処理することで、スケーリングの本当の機能を確認するように設計されています。特に、ここで提供する大規模環境では、1 TB に近い、あるいはそれ以上の大規模なデータを使用してください。

ワークロードに最適な DWU を特定する際の推奨事項:

1. 開発中のデータ ウェアハウスの場合は、少ない数の DWU を選択することから始めます。手始めとしては、DW400 または DW200 が適しています。
2. アプリケーションのパフォーマンスを監視し、選択した DWU の数に対するパフォーマンスの変化を観察します。
3. 線形スケールを想定して、要件に対して最適なパフォーマンス レベルに到達するために、パフォーマンスをどの程度上下する必要があるかを判別します。
4. どのくらい速くまたは遅くワークロードを実行するかに応じて、DWU の数を増減します。サービスはすぐに反応し、新しい DWU 要件を満たすようにコンピューティング リソースが調整されます。
5. ビジネス要件に応じた最適なパフォーマンス レベルに到達するまで調整を行います。

### DWU をスケーリングするタイミング

より短時間で結果を得る必要がある場合は DWU を増やし、増加された DWU に対して支払いを行います。それほど高いコンピューティング能力が必要ではない場合は、DWU を減らし、小さな DWU に対して支払いを行います。

DWU をスケーリングするタイミングを特定するための推奨事項:

1. アプリケーションのワークロードが変動する場合は、ワークロードが高い時点と低い時点に対応するように DWU レベルをスケールアップまたはスケールダウンします。たとえば、月末にワークロードがピークに達することが多い場合は、ピークに達する日には DWU をさらに追加し、ピーク期間が終わったらスケールダウンするように計画します。
2. 大量データの読み込みまたは変換操作を実行する前に、データが短時間で使用可能になるように DWU をスケールアップします。

<a name="pause-compute-bk"></a>

## コンピューティングの一時停止

[AZURE.INCLUDE [SQL Data Warehouse の一時停止の説明](../../includes/sql-data-warehouse-pause-description.md)]

データベースを一時停止するには、次のいずれかの方法を使用できます。

- [Azure ポータルによるコンピューティングの一時停止][]
- [PowerShell によるコンピューティングの一時停止][]
- [REST API によるコンピューティングの一時停止][]

<a name="resume-compute-bk"></a>

## コンピューティングの再開

[AZURE.INCLUDE [SQL Data Warehouse の再開の説明](../../includes/sql-data-warehouse-resume-description.md)]

データベースを再開するには、次のいずれかの方法を使用できます。

- [Azure ポータルによるコンピューティングの再開][]
- [PowerShell によるコンピューティングの再開][]
- [REST API によるコンピューティングの再開][]

## アクセス許可

データベースをスケーリングするには、[ALTER DATABASE][] に関するページで説明されているアクセス許可が必要です。一時停止と再開には、[SQL DB の共同作成者][]のアクセス許可、具体的には Microsoft.Sql/servers/databases/action が必要です。

<a name="next-steps-bk"></a>

## 次のステップ
その他の主要なパフォーマンスの概念を理解するために、次の記事を参照してください。

- [ワークロードと同時実行の管理][]
- [テーブル設計の概要][]
- [テーブルのディストリビューション][]
- [テーブルのインデックス作成][]
- [テーブル パーティション][]
- [テーブルの統計][]
- [ベスト プラクティス][]

<!--Image reference-->

<!--Article references-->
[Data Warehouse ユニット (DWU)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

[Azure ポータルによるコンピューティング能力のスケーリング]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-bk
[PowerShell によるコンピューティング能力のスケーリング]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[REST API によるコンピューティング能力のスケーリング]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[TSQL によるコンピューティング能力のスケーリング]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Azure ポータルによるコンピューティングの一時停止]: ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[PowerShell によるコンピューティングの一時停止]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[REST API によるコンピューティングの一時停止]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Azure ポータルによるコンピューティングの再開]: ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[PowerShell によるコンピューティングの再開]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[REST API によるコンピューティングの再開]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[ワークロードと同時実行の管理]: ./sql-data-warehouse-develop-concurrency.md
[テーブル設計の概要]: ./sql-data-warehouse-tables-overview.md
[テーブルのディストリビューション]: ./sql-data-warehouse-tables-distribute.md
[テーブルのインデックス作成]: ./sql-data-warehouse-tables-index.md
[テーブル パーティション]: ./sql-data-warehouse-tables-partition.md
[テーブルの統計]: ./sql-data-warehouse-tables-statistics.md
[ベスト プラクティス]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB の共同作成者]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0907_2016-->