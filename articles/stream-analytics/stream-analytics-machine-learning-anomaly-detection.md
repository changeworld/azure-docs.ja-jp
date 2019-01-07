---
title: Azure Stream Analytics での異常検出 (プレビュー)
description: この記事では、Azure Stream Analytics と Azure Machine Learning を併用して異常を検出する方法を説明します。
services: stream-analytics
author: dubansal
ms.author: dubansal
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: df1010be8c9f41684af806885db7587bfcf1c540
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091222"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure Stream Analytics での異常検出

> [!IMPORTANT]
> この機能は非推奨になる過程にありますが、新しい機能に置き換えられます。 詳細については、「[Azure Stream Analytics での 8 つの新機能](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)」のブログの投稿を参照してください。

**AnomalyDetection** 演算子を使うと、イベント ストリーム内のさまざまな種類の異常を検出できます。 たとえば、空きメモリの量が長い時間をかけてゆっくり減るのはメモリ リークを示していることがあり、ある範囲で安定していた Web サービス要求の数が急激に増えたり減ったりすることがあります。  

AnomalyDetection 演算子は、3 種類の異常を検出します。 

* **双方向のレベル変化**:上向きと下向き両方向の、値のレベルの持続的な増加または減少。 この値は、スパイクやディップ (瞬間的な、または短時間の変化) とは異なります。  

* **ゆっくりした増加傾向**:時間の経過に伴うゆっくりした増加の傾向。  

* **ゆっくりした減少傾向**:時間の経過に伴うゆっくりした減少の傾向。  

AnomalyDetection 演算子を使う場合は、**Limit Duration** 句を指定する必要があります。 この句では、異常を検出する際に考慮する時間間隔 (現在のイベントからどこまで履歴をさかのぼるか) を指定します。 **When** 句を使うと、特定のプロパティまたは条件に一致するイベントのみにこの演算子を制限することもできます。 また、この演算子は必要に応じて、**Partition by** 句で指定するキーに基づいて、イベントのグループを個別に処理できます。 トレーニングと予測は、パーティションごとに独立して行われます。 

## <a name="syntax-for-anomalydetection-operator"></a>AnomalyDetection 演算子の構文

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 

**使用例**  

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`

### <a name="arguments"></a>引数

* **scalar_expression** - 異常検出を実行する対象のスカラー式です。 このパラメーターに使える値は、単一の (スカラー) 値を返す Float または Bigint データ型です。 ワイルドカード式 **\*** は使用できません。 スカラー式に他の分析関数または外部関数を含めることはできません。 

* **partition_by_clause** - `PARTITION BY <partition key>` 句は、学習とトレーニングを個別のパーティションに分割します。 つまり、`<partition key>` の値ごとに異なるモデルが使われ、その値を持つイベントのみがそのモデルの学習とトレーニングに使われます。 たとえば以下のクエリは、同じセンサーの他の読み取りに対してのみ、トレーニングとスコア付けを行います。

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

* **limit_duration clause** `DURATION(<unit>, <length>)` - 異常を検出する際に考慮する時間間隔 (現在のイベントからどこまで履歴をさかのぼるか) を指定します。 サポートされるユニットとその省略形について詳しくは、[DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) をご覧ください。 

* **when_clause** - 異常検出の計算で考慮に入れるイベントのブール条件を指定します。

### <a name="return-types"></a>戻り値の型

AnomalyDetection 演算子は、3 つのスコアをすべて含むレコードを出力として返します。 次のプロパティは、異なる種類の異常検出機能に関連付けられています。

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

レコードから個々の値を抽出するには、**GetRecordPropertyValue** 関数を使います。 例: 

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 

ある種類の異常は、異常スコアのいずれかがしきい値を超えると検出されます。 しきい値は、0 以上の任意の浮動小数点数です。 しきい値は、感度と信頼性のトレードオフになります。 たとえば、しきい値を低くすると、変化の検出感度が高くなり、生成されるアラートの数が増えます。しきい値を高くすると、検出の感度は低くなり、信頼性は高くなりますが、一部の異常が見落とされる可能性があります。 使うしきい値の正確な値は、シナリオによって異なります。 上限はありませんが、推奨される範囲は 3.25 から 5 です。 

値の例に示す 3.25 は、推奨される開始点にすぎません。 独自のデータ セットについて操作を実行して値を微調整し、許容可能なしきい値に到達するまで出力値を観察します。

## <a name="anomaly-detection-algorithm"></a>異常検出アルゴリズム

* AnomalyDetection 演算子では、イベントにいかなる種類の分布も仮定しない**教師なし学習**の手法が使われます。 一般に、どの時点でも 2 つのモデルが並行して保持されます。その 1 つはスコア付けに使われ、もう 1 つはバックグラウンドでトレーニングされます。 異常検出モデルは、帯域外のメカニズムを使うのではなく、現在のストリームのデータを使ってトレーニングされます。 トレーニングに使われるデータの量は、ユーザーが Limit Duration パラメーターで指定するウィンドウのサイズ d によって異なります。 各モデルは、最終的に d から 2d 分のイベントに基づいてトレーニングされます。 最善の結果を得るためには、各ウィンドウに少なくとも 50 のイベントを持たせることをお勧めします。 

* AnomalyDetection 演算子は、**スライディング ウィンドウ セマンティクス**を使ってモデルをトレーニングし、イベントをスコア付けします。 つまり、各イベントの異常性が評価され、スコアが返されます。 スコアは、その異常の信頼レベルを示す値です。 

* AnomalyDetection 演算子では**再現性が保証されます**。同一の入力は、ジョブ出力の開始時刻に関係なく、常に同じスコアを生成します。 ジョブ出力の開始時刻は、ジョブによって最初の出力イベントが生成される時刻を表します。 これはユーザーによって、現在の時刻、カスタム値、または (ジョブによって以前に出力が生成されている場合は) 前回の出力時刻に設定されます。 

### <a name="training-the-models"></a>モデルのトレーニング 

しばらくすると、モデルは異なるデータでトレーニングされます。 スコアの意味を理解するためには、モデルのトレーニングの基本的なメカニズムを理解することが役立ちます。 ここでは、**t<sub>0</sub>** は**ジョブ出力の開始時刻**を、**d** は Limit Duration パラメーターで指定される**ウィンドウのサイズ**を表します。 時間は、`01/01/0001 00:00:00` から始まる、**サイズ d のホップ** に分割されると仮定します。 次の手順を使って、モデルをトレーニングし、イベントをスコア付けします。

* ジョブが起動すると、時刻 t<sub>0</sub> – 2d から始まるデータが読み取られます。  
* 時間が次のホップに達すると、新しいモデル M1 が作成され、トレーニングが開始されます。  
* 時間がもう 1 つのホップに達すると、新しいモデル M2 が作成され、トレーニングが開始されます。  
* 時間が t<sub>0</sub> に達すると、M1 がアクティブになり、スコアの出力が開始されます。  
* 次のホップでは、同時に 3 つのことが行われます。  

  * 不要になった M1 が破棄されます。  
  * 十分にトレーニングされた M2 がスコア付けに使われます。  
  * 新しいモデル M3 が作成され、バック グラウンドでのトレーニングが開始されます。  

* このサイクルはすべてのホップに対して繰り返されます。アクティブなモデルが破棄され、並行するモデルに切り替えられ、バックグラウンドで第 3 のモデルのトレーニングが開始されます。 

手順を図にすると、次のようになります。 

![機械学習トレーニング モデル](media/stream-analytics-machine-learning-anomaly-detection/machine-learning-training-model.png)

|**Model** | **トレーニングの開始時刻** | **スコアの使用を開始する時刻** |
|---------|---------|---------|
|M1     | 11:20   | 11:33   |
|M2     | 11:30   | 11:40   |
|M3     | 11:40   | 11:50   |

* 11:20 am にモデル M1 がトレーニングを開始します。これは 11:13 am にジョブが読み取りを開始した次のホップです。 13 分間のデータでトレーニングした後、11:33 am に M1 から最初の出力が生成されます。 

* 11:30 am に新しいモデル M2 のトレーニングも開始されますが、そのスコアは、10 分間のデータでトレーニングした 11:40 am になるまで使われません。 

* M3 は M2 と同様のパターンに従います。 

### <a name="scoring-with-the-models"></a>モデルでのスコア付け 

Machine Learning レベルでは、異常検出アルゴリズムは各受信イベントを履歴ウィンドウ内のイベントと比較することで、その異常度の値を計算します。 異常度の計算は、異常の種類ごとに異なります。  

異常度の計算について詳しく確認してみましょう (一連の履歴ウィンドウとイベントが存在すると仮定します)。 

1. **双方向のレベル変化:** 履歴ウィンドウに基づき、通常の運用範囲は [10 パーセンタイル、90 パーセンタイル] として計算されます。つまり、10 パーセンタイルの値が下限、90 パーセンタイルの値が上限です。 現在のイベントの異常度の値は次のように計算されます。  

   - 0 (event_value が通常の運用範囲内にある場合)  
   - event_value/90th_percentile (event_value > 90th_percentile の場合)  
   - 10th_percentile/event_value (event_value < 10th_percentile の場合)  

2. **ゆっくりした増加傾向:** 履歴ウィンドウのイベント値の傾向線を計算し、操作によって線の中の増加傾向を探します。 異常度の値は次のように計算されます。  

   - 傾き (傾きが正の場合)  
   - 0 (それ以外の場合) 

3. **ゆっくりした減少傾向:** 履歴ウィンドウのイベント値の傾向線を計算し、操作によって線の中の減少傾向を探します。 異常度の値は次のように計算されます。 

   - 傾き (傾きが負の場合)  
   - 0 (それ以外の場合)  

受信イベントの異常度の値が計算されると、その値に基づいてマルチンゲール値が計算されます (マルチンゲール値の計算方法について詳しくは、[Machine Learning のブログ](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)を参照)。 異常スコアとしてこのマルチンゲール値が返されます。 マルチンゲール値は異常度の値に応じてゆっくり増加します。これにより、検出機能は散発的な変化の影響を受けづらくなり、誤ったアラートが減少します。 また、次の有用な性質があります。 

M<sub>t</sub> > λ を満たすような t が存在する確率 < 1/λ (M<sub>t</sub> は瞬間 t におけるマルチンゲール値、λ は実数)。 たとえば、M<sub>t</sub> > 100 でアラートを発行する場合、誤検出の確率は 1/100 よりも小さくなります。  

## <a name="guidance-for-using-the-bi-directional-level-change-detector"></a>双方向のレベル変化検出機能を使うためのガイダンス 

双方向のレベル変化検出機能は、停電と復旧、またはラッシュ時間のトラフィックなどのシナリオで使えます。ただし、一度モデルが特定のデータでトレーニングされると、別のレベル変化は、新しい値が以前の上限よりも大きい場合 (上向きのレベル変化の場合)、または以前の下限よりも小さい場合 (下向きのレベル変化の場合) にのみ異常となります。 そのため、モデルはトレーニング ウィンドウを異常と見なすことで (上または下) は、新しいレベルの範囲内のデータ値を表示する必要がありません。 

この検出機能を使う場合は、次の点を考慮する必要があります。 

1. タイム シリーズの値が突然増加または減少する場合、AnomalyDetection 演算子によって検出されます。 しかし、通常への復帰を検出するには、詳細な計画が必要です。 異常の前にタイム シリーズが安定した状態にあった場合、これは AnomalyDetection 演算子の検出ウィンドウを最大でも異常の長さの半分に設定することで可能になります。 このシナリオでは、前もって異常の最小継続時間を推定できること、また、その期間内にモデルを十分にトレーニングするためのイベントがあること (少なくとも 50 イベント) を仮定します。 

   これを、上限の変化を使う以下の図 1 と図 2 で示します (下限の変化にも同じロジックが適用されます)。 どちらの図でも、波形は異常なレベル変化を表します。 オレンジ色の垂直線はホップの境界を示し、ホップのサイズは、AnomalyDetection 演算子で指定される検出ウィンドウと同じです。 緑の線は、トレーニングのウィンドウのサイズを示します。 図 1 では、ホップのサイズは異常が継続する時間と同じです。 図 2 では、ホップのサイズは異常が継続する時間の半分です。 いずれの場合も、スコア付けに使われるモデルが通常のデータでトレーニングされたため、上向きの変化が検出されます。 ただし、双方向のレベル変化検出機能の動作方法に基づき、通常への復帰をスコア付けするモデルに使われる通常の値をトレーニングのウィンドウから除外する必要があります。 図 1 では、スコア付けのモデルのトレーニングにいくつかの通常イベントが含まれるため、通常への復帰は検出できません。 ただし図 2 では、トレーニングに含まれるのは異常な部分のみのため、通常への復帰が検出されます。 半分よりも小さい場合はいずれも同じ理由で動作します。一方、半分よりも大きい場合は最終的に通常のイベントが少し含まれます。 

   ![ウィンドウのサイズが異常の長さと等しい AD](media/stream-analytics-machine-learning-anomaly-detection/windowsize-equal-anomaly-length.png)

   ![ウィンドウのサイズが異常の長さの半分と等しい AD](media/stream-analytics-machine-learning-anomaly-detection/windowsize-equal-half-anomaly-length.png)

2. 異常の長さを予測できない場合、この検出機能はベスト エフォートで動作します。 ただし、より狭い時間枠を選択するとトレーニングのデータが制限され、通常への復帰を検出する可能性が高まります。 

3. 次のシナリオでは、トレーニングのウィンドウに既に同様の高い値の異常が含まれているため、長い異常は検出されません。 

   ![検出された同じサイズの異常](media/stream-analytics-machine-learning-anomaly-detection/anomalies-with-same-length.png)

## <a name="example-query-to-detect-anomalies"></a>異常を検出するためのクエリの例 

次のクエリを使うと、異常が検出された場合にアラートを出力できます。
入力ストリームが一定でないときは、集計手順が時系列を一定にするのに役立ちます。 この例では AVG を使いますが、どのような種類の集計を使うかはユーザーのシナリオによって異なります。 さらに、タイム シリーズに集計ウィンドウより大きいギャップがある場合は、(スライディング ウィンドウ セマンティクスに従って) 異常検出をトリガーするイベントがタイム シリーズに存在しません。 その結果、次のイベントが到着した時点で、一様性の想定が崩れます。 このような状況では、タイム シリーズのギャップを埋める必要があります。 可能な方法の 1 つは、次に示すように、すべてのホップ ウィンドウで最後のイベントを取得することです。

```sql
    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) >= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) >=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) >=
       3.25
```

## <a name="performance-guidance"></a>パフォーマンスに関するガイダンス

* ジョブに対して 6 つのストリーミング ユニットを使います。 
* 少なくとも 1 秒間隔でイベントを送信します。
* AnomalyDetection 演算子を使うパーティション分割されていないクエリでは、平均約 25 ミリ秒の計算待機時間で結果を生成することができます。
* パーティション分割されたクエリで発生する待機時間は、計算の数が増えるので、パーティションの数によって若干異なります。 ただし、全パーティションのイベントの総数が等しい場合は、待機時間はパーティション分割されていない場合とほぼ同じです。
* リアルタイムではないデータを読み取るときは、大量のデータが短時間で取り込まれます。 現在は、このデータを処理すると速度が低下します。 このようなシナリオでの待機時間は、ウィンドウ サイズやイベントの間隔ではなく、ウィンドウ内のデータ ポイントの数に比例して増加することが判明しています。 リアルタイムではない場合の待機時間を減らすには、ウィンドウ サイズを小さくすることを検討してください。 または、現在の時刻からジョブを開始することを検討してください。 パーティション分割されていないクエリの待機時間に関するいくつかの例: 
    - 検出ウィンドウ内のデータ ポイントの数が 60 の場合、3 MBps のスループットで 10 秒の待機時間になります。 
    - データ ポイントの数が 600 では、0.4 MBps のスループットで待機時間は約 80 秒になります。

## <a name="limitations-of-the-anomalydetection-operator"></a>AnomalyDetection 演算子の制限事項 

* 現在、この演算子はスパイクとディップの検出をサポートしていません。 スパイクとディップは、タイム シリーズ内の自然発生的な、または短時間の変化です。 

* 現在、この演算子は季節性のパターンを処理しません。 季節性のパターンとはデータ内に繰り返されるパターンで、たとえば通常とは異なる週末の Web トラフィックの動作や、ブラック フライデーのショッピングの傾向などです。これらは異常ではなく、想定される動作のパターンです。 

* この演算子では、入力タイム シリーズが一定であるものと想定されます。 イベント ストリームは、タンブリング ウィンドウまたはホッピング ウィンドウについて集計することにより一定にできます。 イベント間のギャップが集計ウィンドウより常に小さいシナリオでは、時系列を一定にするにはタンブリング ウィンドウで十分です。 ギャップが大きくなる可能性がある場合は、ホッピング ウィンドウを使って最後の値を繰り返すことにより、ギャップを埋めることができます。 

## <a name="references"></a>参照

* [異常検出 – Machine Learning を使って時系列データの異常を検出する](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [Machine Learning 異常検出 API](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [時系列の異常検出](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="next-steps"></a>次の手順

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

