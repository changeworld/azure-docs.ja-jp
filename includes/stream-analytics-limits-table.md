<properties 
   pageTitle="Stream Analytics に関する制限の表"
   description="Stream Analytics コンポーネントおよび接続のシステム制限および推奨サイズについて説明します。"
   services="stream-analytics"
   documentationCenter="NA"
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun" />
<tags 
   ms.service="stream-analytics"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-data"
   ms.date="10/22/2015"
   ms.author="jeffstok" />

| 制限の種類 | 制限 | 説明 |
|----------------- | ------------|--------- |
| リージョンあたりのサブスクリプションごとのストリーミング ユニットの最大数 | 50 | サブスクリプションのストリーミング ユニットを 50 より大きい数に増やす要求を行うには、[Microsoft サポート](https://support.microsoft.com/ja-JP)に連絡します。 |
| ストリーミング ユニットの最大スループット | 1 MB/秒* | SU ごとの最大スループットは、シナリオによって異なります。実際のスループットはそれより少なくなる可能性があり、クエリの複雑さやパーティション分割によって異なります。詳細な情報については、「[スループット向上のための Azure Stream Analytics ジョブのスケーリング](../articles/stream-analytics/stream-analytics-scale-jobs.md)」記事を参照してください。 |
| SELECT ステートメントのクエリに関する制限 | クエリごとに 5 つの出力 | この制限は将来に増加する可能性があります。 |

<!---HONumber=Nov15_HO1-->