---
title: Azure Stream Analytics でのジョブ監視について
description: この記事では、Azure Stream Analytics でジョブを監視する方法について説明します
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 4b048705c80b7776ab0ab6823e27659a01eedeb5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Stream Analytics ジョブ監視とクエリの監視方法の概要

## <a name="introduction-the-monitor-page"></a>概要: [監視] ページ
Azure ポータルは、クエリとジョブ パフォーマンスの監視とトラブルシューティングに使用できる主要なパフォーマンス メトリックを明らかにします。 これらのメトリックを表示するには、メトリックを確認したい Stream Analytics ジョブを参照し、[概要] ページの **[監視]** 画面を表示します。  

![監視リンク](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

次のようなウィンドウが表示されます。

![監視ジョブ、ダッシュボード](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Stream Analytics に利用できるメトリック
| メトリック                 | 定義                               |
| ---------------------- | ---------------------------------------- |
| SU % 使用率       | ジョブの [スケール] タブからジョブに割り当てられたストリーミング ユニットの使用率。 このインジケーターが 80% 以上に達した場合、イベントの処理が遅れたり、進行が遅延または停止する可能性があります。 |
| 入力イベント           | Stream Analytics のジョブが受信したデータのイベントの量。 イベントが入力ソースに送信されることを検証するために使用できます。 |
| 出力イベント          | Stream Analytics のジョブから出力ターゲットに送信されたデータのイベントの量。 |
| 異常イベント    | イベント順序ポリシーに基づいて、削除された、または調整されたタイムスタンプが付与された、順不同で受信したイベントの数。 誤順序の許容期間の設定の構成により、影響を受けることがあります。 |
| データ変換エラー | Stream Analytics のジョブによって発生した、データ変換エラーの数。 |
| 実行時エラー         | クエリ処理に関連するエラーの総数 (イベントの取り込み時または結果の出力時に見つかったエラーを除く) |
| 遅延入力イベント      | 到着遅延許容期間の設定のイベント順序ポリシーの構成に基づいて、削除された、または調整されたタイムスタンプが付与された、ソースから遅れて到着するイベントの数。 |
| 関数の要求      | Azure Machine Learning 関数に対する呼び出しの数 (存在する場合)。 |
| 失敗した関数の要求 | 失敗した Azure Machine Learning 関数呼び出しの数 (存在する場合)。 |
| 関数のイベント        | Azure Machine Learning 関数に送られたイベントの数 (存在する場合)。 |
| 入力イベントのバイト数      | Stream Analytics ジョブが受信したデータの量 (バイト単位)。 イベントが入力ソースに送信されることを検証するために使用できます。 |


## <a name="customizing-monitoring-in-the-azure-portal"></a>Azure ポータルでの監視のカスタマイズ
グラフのタイプ、表示されるメトリック、および時間範囲を [グラフの編集] 設定で調整できます。 詳細については、「[監視のカスタマイズ方法](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)」をご覧ください。

  ![クエリ モニター、タイム グラフ](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>最新の出力
[概要] ページに示されているように、ジョブを監視する他の対象データ ポイントは最後の出力時刻です。
この時刻は、ジョブの最新の出力のアプリケーション時間 (つまり、イベント データからのタイムスタンプを使用した時間) です。

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次の手順
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

