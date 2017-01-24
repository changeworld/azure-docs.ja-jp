---
title: "Azure Data Lake の考えられるデータ スキュー ソリューション |Microsoft Docs"
description: "Azure Data Lake でデータ スキュー問題のトラブルシューティング向けに考えられるデータ スキュー ソリューション。"
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/16/2016
ms.author: yanacai
translationtype: Human Translation
ms.sourcegitcommit: f2b2a2230900ad5007500f9efb4c8e7ee0aca165
ms.openlocfilehash: 1ddad5c3dc7006895ee38e06b1134857f95c3e7a


---

# <a name="possible-data-skew-solutions-for-azure-data-lake"></a>Azure Data Lake の考えられるデータ スキュー ソリューション

## <a name="what-is-data-skew-problem"></a>データ スキュー問題とは何ですか。

一言で言えば、データ スキューとは過度に表現された値のことです。 これを考慮する点として、50 人の税務調査官を納税申告の監査で各州に 1 人割り当てたとします。 ワイオミング州では人口が少ないため、ワイオミングの監査官は早くに終わります。しかし、カリフォルニアでは人口が多いため、そこの監査官はなかなか仕事が終わらないでしょう。 
    ![データ スキュー問題の例](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png) 

上記のケースでは、データが作業者に均等に配分されていないために、作業者の稼働量にばらつきが発生します。 ジョブの実行時に、上記の例と同じようなケースが生じます。1 つの頂点に与えられるデータが他の頂点よりはるかに多いため、その頂点が他より長く実行され、最終的にジョブ全体が遅くなります。 また、頂点の実行限度が 5 時間で、メモリ制限が 6 GB であるため、ジョブの実行に失敗する場合があります。

## <a name="how-to-troubleshoot"></a>トラブルシューティングの方法

このツールでジョブに傾斜問題があるかどうかを検知できます。問題を解決するには、以下の解決策を試すことができます。

### <a name="solution-1-improve-table-partition"></a>解決策 1: テーブルのパーティションを改善する

#### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>オプション 1: 傾斜したキーの値を事前にフィルター処理する

ビジネス ロジックに影響を与えない場合、事前に頻度の高い値をフィルター処理できます。 たとえば、GUID 列に 000 000-000 が多数ありますが、実際にその値を使って集計しない場合があります。 このケースでは、「WHERE GUID != “000-000-000”」と入力して、集計前に頻度の高い値をフィルター処理できます。

#### <a name="option-2-pick-a-different-partitiondistribution-key"></a>オプション 2: 別のパーティション/ディストリビューション キーを選択する

上記の例では、国全体の税務状況のみを確認する場合、 ID 番号を選択してデータの分散を改善することができます。 異なるパーティションやディストリビューション キーを選択することでデータをより均等に分散できることもありますが、その場合、ビジネス ロジックに影響を与えないことを確認する必要があります。 たとえば、各州の納税額の合計を計算する場合、パーティション キーとして州を選択できます。 これに問題がある場合は、オプション 3 を確認できます。

#### <a name="option-3-add-more-partitiondistribution-key"></a>オプション 3: パーティション キーおよびディストリビューション キーを追加する

州をパーティション キーとして使用するほかに、2 つ以上のキーをパーティション用に使用することができます。たとえば、郵便番号を追加パーティション キーとして追加し、データ分散のサイズを縮小することで、データ分散をより均等にできます。

#### <a name="option-4-round-robin-distribution"></a>オプション 4: ラウンド ロビン分散

パーティションとディストリビューション向けに適切なキーが見つからない場合は、ラウンド ロビン分散を使用することができます。 ラウンド ロビン分散は、すべての行を均等に処理し、対応するバケットにランダムに配置します。 このケースでは、データは均等に分散されますが、地域情報を失ったり、その結果、一部の操作においてジョブのパフォーマンスが削減されたりします。 さらに、傾斜したキーの集計を行う場合、傾斜問題はなくなりません。 ラウンド ロビンの使用方法については、[こちら](https://msdn.microsoft.com/en-us/library/mt706196.aspx#dis_sch)から確認できます。

### <a name="case-2-improve-query-plan"></a>ケース 2: クエリ プランを向上する

#### <a name="option-1-create-statistics-for-column"></a>オプション 1: 列の CREATE STATISTICS

U-SQL では、テーブル上の CREATE STATISTICS ステートメントが提供されます。これにより、値の分散など、テーブル内に保存されるデータの特性についての詳しい情報がクエリ オプティマイザーに提供されます。ほとんどのクエリの場合、クエリ オプティマイザーは高品質のクエリ プラン向けに必要な統計情報をすでに生成しています。一部のケースでは、CREATE STATISTICS で追加の統計を作成するかクエリのデザインを修正することで、クエリのパフォーマンスが向上します。 詳細については、[こちら](https://msdn.microsoft.com/en-us/library/azure/mt771898.aspx)をご覧ください。

**コード例:**

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>統計情報は自動的に更新されないことにご注意ください。つまり、テーブル内のデータを更新しても統計を再作成することを忘れた場合は、クエリ パフォーマンスが以前より悪くなることがあります。
>
>

#### <a name="option-2-use-skewfactor"></a>オプション 2: SKEWFACTOR を使用する

上記の納税確認の例では、各州の納税額を合計する場合、州ごとにグループ化する以外の選択肢はありません。これは、データ スキュー問題の影響を受けます。 ただし、クエリにデータ ヒントを指定してキーの傾斜を識別することで、オプティマイザーは実行プランを最適化できます。

通常、パラメーターを 0.5 や 1 に設定できます。0.5 は微量の傾斜、1 は激しい傾斜を意味します。 ヒントは、現在のステートメント、およびすべてのダウンストリーム ステートメントの実行プランの最適化に影響を与えるため、傾斜したキーの集計前にヒントを追加することを確認してください。

    SKEWFACTOR (columns) = x

    Provides hint that given columns have a skew factor x between 0 (no skew) and 1 (very heavy skew).

**コード例:**

    //Adding SKEWFACTOR hint
    @Impressions = 
        SELECT * FROM 
        searchDM.SML.PageView(@start, @end) AS PageView
        OPTION(SKEWFACTOR(Query)=0.5)
        ;

    //Query 1 for key: Query, ClientId
    @Sessions = 
        SELECT 
            ClientId, 
            Query, 
            SUM(PageClicks) AS Clicks
        FROM 
            @Impressions
        GROUP BY
            Query, ClientId
        ;

    // Query 2 for Key: Query
    @Display = 
        SELECT * FROM @Sessions 
            INNER JOIN @Campaigns 
                ON @Sessions.Query == @Campaigns.Query
        ;   

SKEWFACTOR に加え、特定の傾斜キー結合のケースで、他の結合行セットが小さいことがわかっている場合は、JOIN の前に ROWCOUNT ヒントを U-SQL ステートメントに追加できます。これにより、行セットの 1 つが小さいことをオプティマイザーに知らせることで、オプティマイズは結合戦略をブロードキャストしてパフォーマンスを向上できます。 ただし、ROWCOUNT は傾斜問題の対処に多少役立つ程度で、問題は解決されないことにご注意ください。

    OPTION(ROWCOUNT = n)

    Identify small row set before join by giving an estimated integer row count.

**コード例:**

    // Unstructured (24 hours daily log impressions)
    @Huge   = EXTRACT ClientId int, ... 
                FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
                ;

    // Small subset (ie: ForgetMe opt out)
    @Small  = SELECT * FROM @Huge 
                WHERE Bing.ForgetMe(x,y,z)
                OPTION(ROWCOUNT=500)
                ;

    // Result (not enough info to determine simple Broadcast join)
    @Remove = SELECT * FROM Bing.Sessions
                INNER JOIN @Small ON Sessions.Client == @Small.Client
                ;

### <a name="case-3-improve-user-defined-reducer-and-combiner"></a>ケース 3: ユーザー定義レジューサーおよびコンバイナーを向上する

複雑な処理ロジックに対応するユーザー定義演算子を構築することがあります。適切に構築されたレジューサーおよびコンバイナーは、一部のケースにおいてデータ スキュー問題を軽減する場合があります。

#### <a name="option-1-use-recursive-reducer-if-possible"></a>オプション 1: 可能な限り再帰的なレジューサーを使用する

既定では、ユーザー定義レジューサーは非再帰モードで実行されます。これは、キーの作業負荷が軽減された場合、その負荷が 1 つの頂点に配布されることを意味します。 しかし、問題点の 1 つとして、データがスキューされている場合、膨大なデータ セットが 1 つの頂点で処理され、かなり長い時間実行されることがあります。 

パフォーマンスを向上するには、コードに属性を追加して、レジューサーを再帰モードとして定義することができます。その後、膨大なデータ セットを複数の頂点で配布し、並列処理を実行することで、ジョブを高速化できます。 

注意点の 1 つとして、非再帰的なレジューサーを再帰的に変更する場合に、アルゴリズムが結合法則であることを確認する必要があります。 たとえば、合計は結合法則ですが、中央値は違います。 また、レジューサーの入力および出力が同じスキーマを保持することを確認する必要があります。

**再帰的なレジューサーの属性:**

    [SqlUserDefinedReducer(IsRecursive = true)]

**コード例:**

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow> 
            Reduce(IRowset input, IUpdatableRow output)
        {
            // your reducer code here
        }
    }

#### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>オプション 2: 可能な限り行レベルのコンバイナー モードを使用する

特定の傾斜したキーの結合ケース用の ROWCOUNT ヒントと同様に、コンバイナー モードでは、膨大な量の傾斜キー値のセットを複数の頂点に分散しようとします。これにより、作業を同時に実行できるようにします。 データ スキュー問題は解決できませんが、膨大な量の傾斜キー値のセットに対処するときに多少役立ちます。

既定では、コンバイナー モードは完全であり、左および右の行セットを分割できないことを意味します。 モードを左/右/内部に設定することで、行レベルの結合を使用できます。また、システムは対応する行セットを分割し、複数の頂点に分散して、並列処理で実行します。 ただし、コンバイナー モードを構成する前に、対応する行セットを分割できるかどうかを確認する必要があります。

分割された左の行セットの例を以下に示します。 このケースでは、出力行がいずれも、左側の単一の入力行に依存します。また、キー値が同じ右側の行すべてにも依存する可能性があります。 この場合、コンバイナー モードを左として設定した場合、システムは巨大な左側の行セットをより小さな行セットに分割し、それらを複数の頂点に割り当てます。
![コンバイナー モードの図](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png) 
   
>[!NOTE]
>間違ったコンバイナー モードを設定した場合、結合の効率が低下し、さらには間違った結果を引き起こす場合があります。
>
>

**コンバイナーの属性: **

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): 出力行がいずれも、左側の単一の入力行に依存します (また、キー値が同じ右側の行すべてにも依存する可能性があります)。

- qlUserDefinedCombiner(Mode=CombinerMode.Right): 出力行がいずれも、右側の単一の入力行に依存します (また、キー値が同じ左側の行すべてにも依存する可能性があります)。

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): 出力行がいずれも、値が同じである左側および右側の単一の入力行に依存します。

**コード例:**

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow> 
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        // your combiner code here
        }
    }


<!--HONumber=Dec16_HO3-->


