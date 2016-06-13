<properties
   pageTitle="Azure SQL Data Warehouse のパフォーマンスのスケーラビリティ | Microsoft Azure"
   description="Azure SQL Data Warehouse のパフォーマンス スケールアウト機能について説明します。DWU を調整してスケールアウトする方法や、コンピューティング リソースを一時停止および再開してコストを節約する方法を説明します。"
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
   ms.date="05/26/2016"
   ms.author="barbkess;sonyama"/>

# Azure SQL Data Warehouse のパフォーマンスのスケーラビリティ

> [AZURE.SELECTOR]
- [概要](sql-data-warehouse-overview-scalability.md)
- [ポータル](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST ()](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)

SQL Data Warehouse のアーキテクチャではストレージとコンピューティングを分離して、それぞれを個別にスケーリングできます。その結果、パフォーマンスのスケールアウト時に、必要な分だけのパフォーマンスにかかる料金を支払うことで、コストを節約できます。

この概要では、SQL Data Warehouse の次のパフォーマンス スケールアウト機能について説明し、これらの使用方法、使用するタイミングにおける推奨事項を紹介します。

- Data Warehouse ユニット (DWU) の調整によるパフォーマンスのスケーリング
- コンピューティング リソースの一時停止または再開

<a name="scale-performance-bk"></a>

## パフォーマンスのスケーリング

SQL Data Warehouse では、CPU、メモリ、および I/O 帯域幅のコンピューティング リソースを増減させることにより、パフォーマンスの拡張と縮小をすばやく行えます。パフォーマンスのスケーリングに必要なことは、SQL Data Warehouse からデータベースに割り当てられている Data Warehouse ユニット (DWU) の数を調整するだけです。SQL Data Warehouse がすばやく変更を行い、ハードウェアやソフトウェアへの基本的な変更をすべて処理します。

>[AZURE.NOTE] データ ウェアハウスで優れたパフォーマンスを実現するために必要なストレージのタイプ、メモリ量、およびプロセッサのタイプを調査する必要があったのは遠い過去のことになりました。データ ウェアハウスをクラウドに置くことにより、低レベルのハードウェア問題に対処する必要はなくなりました。その代わりに、SQL Data Warehouse ではデータをどの程度高速に分析する必要があるかということが検討事項になります。

### DWU とは何ですか?

*Data Warehouse ユニット* (DWU) とは、特定の時点でのデータベースの基盤となるコンピューティング機能の測定値です。DWU の数が増えるほど、SQL Data Warehouse はより広範囲にわたって分散された CPU やメモリ リソース間で操作 (データのロードやクエリなど) を並列で実行するようになります。これにより、遅延が削減され、パフォーマンスが向上します。

DWU は、読み込みレートやスキャン レートに基づきます。DWU が増えるほど、読み込みレートとスキャン レートも増えます。

- **読み込みレート**。SQL Data Warehouse が 1 秒あたりに取り込めるレコードの数。具体的に言うと、SQL Data Warehouse が PolyBase を使用して Azure BLOB ストレージからクラスター化列ストア インデックスにインポートできるレコードの数です。 

- **スキャン レート**。クエリが 1 秒あたりに SQL Data Warehouse から取得できるレコードの数。具体的には、SQL Data Warehouse がクラスター化列ストア インデックス上でデータ ウェアハウス クエリを実行して返すことのできるレコードの数です。スキャン レートをテストするために、大量の行をスキャンし、複雑な集計を実行する標準的なデータ ウェアハウス クエリを使用します。これは IO と CPU を集中的に使用する操作です。

>[AZURE.NOTE] 現在、重要なパフォーマンスの拡張機能を測定、構築しており、期待されるレートをまもなくお伝えします。プレビュー中にも、これらのレートを高めて予想どおりに拡張できるように、継続的な拡張 (圧縮およびキャッシュ機能の向上) を行います。

DWU の一覧については、[容量制限][]に関する記事のサービス レベル目標に関するセクションを参照してください。

### パフォーマンスをスケーリングする方法

コンピューティング能力を臨機応変に調整するには、データベースの Data Warehouse ユニット (DWU) の設定を単に変更します。バックグラウンドでは、SQL Data Warehouse はインスタンスを停止し、CPU とメモリの割り当てを変更してから、インスタンスを再開します。

DWU は 100 ブロック単位で割り当てられますが、すべてのブロックを使用できるわけではありません。パフォーマンスは DWU の増加に比例して増加します。より高いレベルの DWU では、パフォーマンスに大幅な改善が見られるには、100 個以上の DWU を追加する必要があります。DWU で意味のある改善を選択できるように、最良の結果が得られる DWU レベルが用意されています。
 
DWU を調整するのには、これらの各方法をどれでも使用できます。

- [Azure ポータルでのパフォーマンスのスケーリング][]
- [PowerShell でのパフォーマンスのスケーリング][]
- [REST API でのパフォーマンスのスケーリング][]
- [TSQL でのパフォーマンスのスケーリング][]

### 必要な DWU の数
 
SQL Data Warehouse ではパフォーマンスは線形にスケールし、あるコンピューティング スケールから別のコンピューティング スケールへの変化 (100 個の DWU から 2000 個 の DWU への変化など) は数秒で実行されます。これにより、さまざまな DWU 設定で試してから、シナリオに最適なものを選ぶことができます。

> [AZURE.NOTE] データ ボリュームが小さいと、予想どおりのパフォーマンス スケーリングを達成できない場合があります。正確なパフォーマンス テスト結果を取得するためには、1 TB 以上のデータ ボリュームで始めることをお勧めします。

ワークロードに最適な DWU を特定する際の推奨事項:

1. 開発中のデータ ウェアハウスの場合は、少ない数の DWU を選択することから始めます。
2. アプリケーションのパフォーマンスを監視し、選択した DWU の数に対するパフォーマンスの変化を観察します。
3. 線形スケールを想定して、要件に対して最適なパフォーマンス レベルに到達するために、パフォーマンスをどの程度上下する必要があるかを判別します。
4. どのくらい速くまたは遅くワークロードを実行するかに応じて、DWU の数を増減します。サービスはすぐに反応し、新しい DWU 要件を満たすようにコンピューティング リソースが調整されます。
5. ビジネス要件に応じた最適なパフォーマンス レベルに到達するまで調整を行います。

### DWU をスケーリングするタイミング

全体的に見て、DWU は単純である必要があります。より短時間で結果を得る必要がある場合は DWU を増やし、増加された DWU に対して支払いを行います。それほど高いコンピューティング能力が必要ではない場合は、DWU を減らし、小さな DWU に対して支払いを行います。

DWU をスケーリングするタイミングを特定するための推奨事項:

1. アプリケーションのワークロードが変動する場合は、ワークロードが高い時点と低い時点に対応するように DWU レベルをスケールアップまたはスケールダウンします。たとえば、月末にワークロードがピークに達することが多い場合は、ピークに達する日には DWU をさらに追加し、ピーク期間が終わったらスケールダウンするように計画します。
1. 大量データの読み込みまたは変換操作を実行する前に、データが短時間で使用可能になるように DWU をスケールアップします。

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

<a name="next-steps-bk"></a>

## 次のステップ
その他の主要なパフォーマンスとスケールの概念を理解するために、次の記事を参照してください。

- [同時実行モデル][]
- [テーブルの設計][]
- [テーブルのハッシュ分散キーの選択][]
- [パフォーマンスを向上させる統計][]

<!--Image reference-->

<!--Article references-->

[Azure ポータルでのパフォーマンスのスケーリング]: ./sql-data-warehouse-manage-scale-out-tasks.md#task-1-scale-performance
[PowerShell でのパフォーマンスのスケーリング]: ./sql-data-warehouse-manage-scale-out-tasks-powershell.md#task-1-scale-performance
[REST API でのパフォーマンスのスケーリング]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md#task-1-scale-performance
[TSQL でのパフォーマンスのスケーリング]: ./sql-data-warehouse-manage-scale-out-tasks-tsql.md

[容量制限]: ./sql-data-warehouse-service-capacity-limits.md

[Azure ポータルによるコンピューティングの一時停止]: ./sql-data-warehouse-manage-scale-out-tasks.md#task-2-pause-compute
[PowerShell によるコンピューティングの一時停止]: ./sql-data-warehouse-manage-scale-out-tasks-powershell.md#task-2-pause-compute
[REST API によるコンピューティングの一時停止]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md#task-2-pause-compute
[Azure ポータルによるコンピューティングの再開]: ./sql-data-warehouse-manage-scale-out-tasks.md#task-3-resume-compute
[PowerShell によるコンピューティングの再開]: ./sql-data-warehouse-manage-scale-out-tasks-powershell.md#task-3-resume-compute
[REST API によるコンピューティングの再開]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md#task-3-resume-compute

[同時実行モデル]: ./sql-data-warehouse-develop-concurrency.md
[テーブルの設計]: ./sql-data-warehouse-develop-table-design.md
[テーブルのハッシュ分散キーの選択]: ./sql-data-warehouse-develop-hash-distribution-key.md
[パフォーマンスを向上させる統計]: ./sql-data-warehouse-develop-statistics.md
[development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0601_2016-->