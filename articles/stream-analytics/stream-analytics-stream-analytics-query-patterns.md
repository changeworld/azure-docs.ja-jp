<properties
	pageTitle="Azure Stream Analytics クエリ パターン | Microsoft Azure"
	description="一般的な Azure Stream Analytics クエリのパターン"
	keywords="stream analytics, サンプル, クエリ, 言語, ガイド, パターン"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="11/06/2015"
	ms.author="jeffstok"/>


# 一般的な Azure Stream Analytics クエリのパターン  #

## はじめに ##
Azure Stream Analytics でのクエリは、SQL に似たクエリ言語で表されます。この言語については、[こちら](https://msdn.microsoft.com/library/azure/dn834998.aspx)をご覧ください。このドキュメントでは、実際のシナリオに基づいて、いくつかの一般的なクエリ パターンの対処方法について説明します。このドキュメントは作成中であり、継続的に新しいパターンで更新されます。

## 基本 ##

## データ型の変換 ##
**説明**: 入力ストリームのプロパティの型を定義します。例: 車の重量は入力ストリームでは文字列ですが、合計を計算するために INT に変換する必要があります。

**入力**:

| 保存する | 時刻 | 重量 |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | "1000" |
| Honda | 2015-01-01T00:00:02.0000000Z | "2000" |

**出力**:

| 保存する | 重量 |
| --- | --- |
| Honda | 3000 |

**解決策**:

	SELECT
    	Make,
    	SUM(CAST(Weight AS BIGINT)) AS Weight
	FROM
    	Input TIMESTAMP BY Time
	GROUP BY
		Make,
    	TumblingWindow(second, 10)

**説明**: Weight フィールドに対して CAST ステートメントを使用し、型を指定します (サポートされるデータ型の一覧は[こちら](https://msdn.microsoft.com/library/azure/dn835065.aspx)を参照)。

## Like/Not like を使用したパターン マッチング ##
**説明**: イベントのフィールド値が特定のパターンと一致することをチェックします。例: A で始まって 9 で終わるライセンス プレートを返します。

**入力**:

| 保存する | LicensePlate | 時刻 |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**出力**:

| 保存する | LicensePlate | 時刻 |
| --- | --- | --- |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**解決策**:

	SELECT
    	*
	FROM
    	Input TIMESTAMP BY Time
	WHERE
    	LicensePlate LIKE 'A%9'

**説明**: LIKE ステートメントを使用して、LicensePlate フィールドの値が、A で始まり、0 文字以上の任意の文字列があって、9 で終わるかどうかをチェックします。

## 異なるケース/値に異なるロジックを指定する (CASE ステートメント) ##
**説明**: 何らかの条件に基づいてフィールドに異なる計算を適用します。例: 通過した車のメーカー別台数の説明文で、1 の場合とそれ以外の場合を分けて処理します。

**入力**:

| 保存する | 時刻 |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**出力**:

| CarsPassed | 時刻 |
| --- | --- | --- |
| 1 Honda | 2015-01-01T00:00:10.0000000Z |
| 2 Toyotas | 2015-01-01T00:00:10.0000000Z |

**解決策**:

    SELECT
    	CASE
			WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
			ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
		END AS CarsPassed,
		System.TimeStamp AS Time
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)

**説明**: CASE 句を使用すると、条件に基づいて異なる計算を適用できます (この例では、集計ウィンドウでの車の台数)。

## 複数の出力にデータを送信する ##
**説明**: 単一のジョブから複数の出力ターゲットにデータを送信します。例: しきい値ベースのアラートに対してデータを分析し、すべてのイベントを BLOB ストレージにアーカイブします。

**入力**:

| 保存する | 時刻 |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**出力 1**:

| 保存する | 時刻 |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**出力 2**:

| 保存する | 時刻 | カウント |
| --- | --- | --- |
| Toyota | 2015-01-01T00:00:10.0000000Z | 3 |

**解決策**:

	SELECT
		*
	INTO
		ArchiveOutput
	FROM
		Input TIMESTAMP BY Time

	SELECT
		Make,
		System.TimeStamp AS Time,
		COUNT(*) AS [Count]
	INTO
		AlertOutput
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)
	HAVING
		[Count] >= 3

**説明**: INTO 句は、Stream Analytics に対して、このステートメントからのデータを書き込む出力を指定します。1 番目のクエリは、受け取ったデータを ArchiveOutput という名前の出力にパススルーします。2 番目のクエリは、簡単な集計とフィルター処理を行い、結果を下流のアラート システムに送信します。*注*: CTE の結果を複数の出力ステートメントで再利用することもできます (WITH ステートメント)。これには、入力ソースに対して開くリーダーが少なくて済む利点があります。例:

	WITH AllRedCars AS (
		SELECT
			*
		FROM
			Input TIMESTAMP BY Time
		WHERE
			Color = 'red'
	)
	SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
	SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## パターン ##

## ユニークな値のカウント
**説明**: 期間内にストリームに出現するユニークなフィールド値の数をカウントします。例: 2 秒間に料金所を通過した自動車のユニークなメーカーの数。

**入力**:

| 保存する | 時刻 |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**出力:**

| カウント | 時刻 |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**解決策:**

	WITH Makes AS (
	    SELECT
	        Make,
	        COUNT(*) AS CountMake
	    FROM
	        Input TIMESTAMP BY Time
	    GROUP BY
	          Make,
	          TumblingWindow(second, 2)
	)
	SELECT
	    COUNT(*) AS Count,
	    System.TimeStamp AS Time
	FROM
	    Makes
	GROUP BY
	    TumblingWindow(second, 1)


**説明:** 初期集計を行って、ユニークなメーカーと期間内のその数を取得します。その後、得られたメーカーの数を集計します。期間内のすべてのユニークな値を渡して同じタイムスタンプを取得した後、最初のステップの2 つの期間を集計しないように 2 番目の集計期間を最小化する必要があります。

## 値が変化したかどうかを判定する ##
**説明**: 前の値を見て、現在の値と異なるかどうかを判定します。例: 有料道路上の前の自動車は、現在の自動車と同じメーカーか。

**入力**:

| 保存する | 時刻 |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**出力**:

| 保存する | 時刻 |
| --- | --- |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**解決策**:

	SELECT
		Make,
		Time
	FROM
		Input TIMESTAMP BY Time
	WHERE
		LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**説明**: LAG を使用して入力ストリームで 1 つ前のイベントを調べて、Make の値を取得します。次に、現在のイベントの Make と比較し、異なる場合はイベントを出力します。

## 期間内の最初のイベントを検索する ##
**説明**: 10 分間隔で最初の自動車を検索します。

**入力**:

| LicensePlate | 保存する | 時刻 |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**出力**:

| LicensePlate | 保存する | 時刻 |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |

**解決策**:

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) = 1

問題を変更して、10 分ごとに特定のメーカーの最初の自動車を検索します。

| LicensePlate | 保存する | 時刻 |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**解決策**:

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## 期間内の最後のイベントを検索する ##
**説明**: 10 分間隔で最後の自動車を検索します。

**入力**:

| LicensePlate | 保存する | 時刻 |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**出力**:

| LicensePlate | 保存する | 時刻 |
| --- | --- | --- |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**解決策**:

	WITH LastInWindow AS
	(
		SELECT 
			MAX(Time) AS LastEventTime
		FROM 
			Input TIMESTAMP BY Time
		GROUP BY 
			TumblingWindow(minute, 10)
	)
	SELECT 
		Input.LicensePlate,
		Input.Make,
		Input.Time
	FROM
		Input TIMESTAMP BY Time 
		INNER JOIN LastInWindow
		ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
		AND Input.Time = LastInWindow.LastEventTime

**説明**: クエリには 2 つのステップがあります。第 1 のステップで、10 分の期間内の最後のタイムスタンプを検索します。第 2 のステップで、第 1 のクエリの結果と元のストリームを結合し、各期間で最後のタイムスタンプに一致するイベントを検索します。

## イベントがないことを検出する ##
**説明**: ストリームに特定の条件に一致する値が含まれていないことを確認します。例: 90 秒以内に同じメーカーの自動車が 2 台連続して有料道路に進入したか。

**入力**:

| 保存する | LicensePlate | 時刻 |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Honda | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Toyota | DEF-987 | 2015-01-01T00:00:03.0000000Z |
| Honda | GHI-345 | 2015-01-01T00:00:04.0000000Z |

**出力**:

| 保存する | 時刻 | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda | 2015-01-01T00:00:02.0000000Z | AAA-999 | ABC-123 | 2015-01-01T00:00:01.0000000Z |

**解決策**:

	SELECT
	    Make,
	    Time,
	    LicensePlate AS CurrentCarLicensePlate,
	    LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
	    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
	FROM
	    Input TIMESTAMP BY Time
	WHERE
	    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**説明**: LAG を使用して入力ストリームで 1 つ前のイベントを調べて、Make の値を取得します。次にそれを現在のイベントの Make と比較して、それらが同じ場合はイベントを出力し、LAG を使用して前の自動車についてのデータを取得します。

## 条件の期間を検出する ##
**説明**: 条件が発生していた時間の長さを調べます。例: すべての自動車が正しくない (20,000 ポンド超) 結果になるバグがありました。バグの期間を計算します。

**入力**:

| 保存する | 時刻 | 重量 |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | 2000 |
| Toyota | 2015-01-01T00:00:02.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:03.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:04.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:05.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:06.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:07.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:08.0000000Z | 2000 |

**出力**:

| StartFault | EndFault | FaultDurationSeconds |
| --- | --- | --- |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |

**解決策**:

	SELECT
	    PrevGood.Time AS StartFault,
	    ThisGood.Time AS Endfault,
	    DATEDIFF(second, PrevGood.Time, ThisGood.Time) AS FaultDuraitonSeconds
	FROM
	    Input AS ThisGood TIMESTAMP BY Time
	    INNER JOIN Input AS PrevGood TIMESTAMP BY Time
	    ON DATEDIFF(second, PrevGood, ThisGood) BETWEEN 1 AND 3600
	    AND PrevGood.Weight < 20000
	    INNER JOIN Input AS Bad TIMESTAMP BY Time
	    ON DATEDIFF(second, PrevGood, Bad) BETWEEN 1 AND 3600
	    AND DATEDIFF(second, Bad, ThisGood) BETWEEN 1 AND 3600
	    AND Bad.Weight >= 20000
	    LEFT JOIN Input AS MidGood TIMESTAMP BY Time
	    ON DATEDIFF(second, PrevGood, MidGood) BETWEEN 1 AND 3600
	    AND DATEDIFF(second, MidGood, ThisGood) BETWEEN 1 AND 3600
	    AND MidGood.Weight < 20000
	WHERE
	    ThisGood.Weight < 20000
	    AND MidGood.Weight IS NULL

**説明**: 間に含まれるのが異常なイベントだけで正常なイベントは含まれない 2 つの正常なイベントを探します。これらは、少なくとも 1 つの異常イベントの前のイベントと後のイベントです。間に 1 つの異常イベントがある 2 つの正常イベントを取得するには、2 つの JOIN を使用し、重みを調べてタイムスタンプを比較することによって正常 -> 異常 -> 正常を確認します。

「NULL が含まれる、またはイベントを含まない左外部結合」で学習したことを使用して、選択した 2 つの正常イベントの間で正常イベントが発生していないことを確認する方法を知ります。

これらを組み合わせて、間に他の正常イベントがない正常 -> 異常 -> 正常を取得します。開始正常イベントと終了正常イベントの間の期間を計算して、バグの期間が得られます。

## 問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/ja-JP/home?forum=AzureStreamAnalytics)を参照してください。

## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](../stream.analytics.get.started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=Nov15_HO3-->