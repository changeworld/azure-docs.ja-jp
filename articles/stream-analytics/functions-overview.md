---
title: Azure Stream Analytics でのユーザー定義関数
description: この記事は、Azure Stream Analytics でのユーザー定義関数の概要です。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b29d66e8bb213fbbb162c3249f022e0783f9f62f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115669"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Azure Stream Analytics でのユーザー定義関数

Azure Stream Analytics の SQL に似たクエリ言語を使用すると、ストリーミング データに対してリアルタイム分析ロジックを簡単に実装できます。 Stream Analytics では、クエリ内で呼び出されるカスタム関数を通して柔軟性を高めることができます。 次のコード例は `sampleFunction` という名前の UDF であり、1 つのパラメーター (ジョブが受信する各入力レコード) を受け取り、その結果が `sampleResult` として出力に書き込まれます。

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>関数の種類

Azure Stream Analytics では、次の 4 種類の関数がサポートされています。 

* JavaScript ユーザー定義関数 
* JavaScript ユーザー定義集計 
* C# ユーザー定義関数 (Visual Studio 使用) 
* Azure Machine Learning 

機械学習モデルを使用したリアルタイム スコアリング、文字列操作、複雑な数学的計算、データのエンコードとデコードなどのシナリオで、これらの関数を使用できます。 

## <a name="limitations"></a>制限事項

ユーザー定義関数はステートレスであり、戻り値にはスカラー値のみが可能です。 これらのユーザー定義関数から外部の REST エンドポイントを呼び出すことはできません。それはジョブのパフォーマンスに影響する可能性があるためです。 

Azure Stream Analytics では、すべての関数の呼び出しと返された結果のレコードは保持されません。 再現性を保証する (たとえば古いタイムスタンプからジョブを再実行すると、同じ結果が再び返される) 場合は、`Date.GetData()` や `Math.random()` などの関数を使用しないでください。これらの関数では毎回の呼び出しで同じ結果が返されることはないためです。  

## <a name="diagnostic-logs"></a>診断ログ

ランタイム エラーは致命的とみなされ、アクティビティ ログと診断ログに表示されます。 関数ですべての例外とエラーを処理し、クエリに有効な結果を返すことをお勧めします。 これにより、ジョブが[エラー状態](job-states.md)になるのを防ぐことができます。  


## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics での JavaScript ユーザー定義関数](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics の JavaScript ユーザー定義集計](stream-analytics-javascript-user-defined-aggregates.md)
* [Azure Stream Analytics ジョブ用の .NET Standard ユーザー定義関数を開発する](stream-analytics-edge-csharp-udf-methods.md)
* [Azure Stream Analytics と Azure Machine Learning を統合する](machine-learning-udf.md)

