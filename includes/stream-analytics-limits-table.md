---
title: Stream Analytics に関する制限の表
description: Stream Analytics コンポーネントおよび接続のシステム制限および推奨サイズについて説明します。
services: stream-analytics
documentationcenter: NA
author: jeffstokes72
manager: paulettm
editor: cgronlun

ms.service: stream-analytics
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: big-data
ms.date: 07/25/2016
ms.author: jeffstok

---
| 制限の種類 | 制限 | 説明 |
| --- | --- | --- |
| リージョンあたりのサブスクリプションごとのストリーミング ユニットの最大数 |50 |サブスクリプションのストリーミング ユニットを 50 より大きい数に増やす要求を行うには、[Microsoft サポート](https://support.microsoft.com/ja-JP)に連絡します。 |
| ストリーミング ユニットの最大スループット |1 MB/秒* |SU ごとの最大スループットは、シナリオによって異なります。実際のスループットはそれより少なくなる可能性があり、クエリの複雑さやパーティション分割によって異なります。詳細な情報については、「[スループット向上のための Azure Stream Analytics ジョブのスケーリング](../articles/stream-analytics/stream-analytics-scale-jobs.md)」記事を参照してください。 |
| ジョブあたりの入力の最大数 |60 |Stream Analytics ジョブあたりの入力には、最大数 60 のハード制限があります。 |
| ジョブあたりの出力の最大数 |60 |Stream Analytics ジョブあたりの出力には、最大数 60 のハード制限があります。 |
| ジョブあたりの関数の最大数 |60 |Stream Analytics ジョブあたりの関数には、最大数 60 のハード制限があります。 |
| リージョンあたりのジョブの最大数 |1500 |サブスクリプションごとに、地理的リージョンあたり最大 1500 ジョブが可能です。 |

<!---HONumber=AcomDC_0727_2016-->