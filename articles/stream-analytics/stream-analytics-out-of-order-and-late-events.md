---
title: "Azure Stream Analytics によるイベントの順序と遅延の処理 | Microsoft Docs"
description: "データ ストリーム中の順序を逸脱したイベントまたは遅延イベントが Stream Analytics によって処理されるしくみを説明します。"
keywords: "順序の逸脱, 遅延, イベント"
documentationcenter: 
services: stream-analytics
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 208dfa14d5d18e106d654539cd80bafdeb90cdf8
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2017
---
# <a name="azure-stream-analytics-event-order-consideration"></a>Azure Stream Analytics のイベントの順序に関する考慮事項

## <a name="understand-arrival-time-and-application-time"></a>到着時刻とアプリケーション時間について

イベントのテンポラル データ ストリームでは、各イベントにタイムスタンプが割り当てられます。 Azure Stream Analytics は、到着時刻またはアプリケーション時間を使用して、各イベントにタイムスタンプを割り当てます。 "System.Timestamp" 列には、イベントに割り当てられたタイムスタンプが含まれます。 イベントがソースに到達すると、入力ソースに到着時刻が割り当てられます。 到着時刻は、イベント ハブ入力では EventEnqueuedTime になり、BLOB 入力では [BLOB の最終更新時刻](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3)になります。 アプリケーション時間は、イベントの生成時に割り当てられ、ペイロードの一部になります。 アプリケーション時間でイベントを処理するには、SELECT クエリで "Timestamp by" 句を使用します。 "Timestamp by" 句がない場合は、イベントは到着時刻で処理されます。 到着時刻にアクセスするには、イベント ハブの場合は EventEnqueuedTime プロパティを、BLOB 入力の場合は BlobLastModified プロパティを使用します。 Azure Stream Analytics では、出力はタイムスタンプ順に生成され、順序を逸脱したデータを扱うためのいくつかの設定が備わっています。


## <a name="azure-stream-analytics-handling-of-multiple-streams"></a>Azure Stream Analytics による複数のストリームの処理

Azure Stream Analytics ジョブは、次のような場合に複数のタイムラインからイベントを組み合わせます。

* 複数のパーティションから出力を生成する場合。 明示的な "Partition by PartitionId" が含まれていないクエリは、すべてのパーティションのイベントを組み合わせる必要があります。
* 複数の異なる入力ソースを結合する場合。
* 入力ソースを結合する場合。

複数のタイムラインを組み合わせるシナリオでは、Azure Stream Analytics は、組み合わされたすべてのソースが少なくとも時刻 *t1* になった後にのみ、タイムスタンプ *t1* の出力を生成します。
たとえば、"*イベント ハブ*" パーティションに 2 つのパーティションがあり、一方のパーティション *P1* には時刻 *t1* までのイベントが含まれ、もう一方のパーティション *P2* には時刻 *t1 + x* までのイベントが含まれているとします。クエリがこのイベント ハブ パーティションから読み取ると、出力は時刻 *t1* まで生成されます。
ただし、明示的な *"Partition by PartitionId"* 句がある場合は、両方のパーティションが個別に進行します。
到着遅延許容設定は、一部のパーティションにデータがない状況に対処するために使用されます。

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>到着遅延許容と誤順序の許容の構成
順序どおりでない入力ストリームは、次のいずれかに当てはまります。
* 並べ替えられた (そのため**遅延**した)。
* ユーザーが指定したポリシーに従って、システムによって調整された。

Stream Analytics では、**アプリケーション時間**で処理する場合に、遅延イベントと順不同のイベントが許容されます。 Azure Portal の **[イベント順序]** オプションで次の設定を使用できます。 

![Stream Analytics のイベントの処理](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

**到着遅延許容**
* この設定は、アプリケーション時間で処理する場合にのみ適用され、それ以外の場合は無視されます。
* これは、到着時刻とアプリケーション時間の差の最大値です。 アプリケーション時間が (到着時刻 - 到着遅延期間) よりも前の時刻である場合は、(到着時刻 - 到着遅延期間) に設定されます
* 同じ入力ストリームの複数のパーティションまたは複数の入力ストリームが組み合わされる場合、すべてのパーティションが新しいデータを待機する最大時間が到着遅延許容期間になります。 

簡単に言うと、到着遅延期間は、イベントが生成されてから、入力ソース側でイベントが受信されるまでの最大遅延です。
到着遅延許容に基づく調整が最初に実行され、誤順序に基づく調整が次に実行されます。 **System.Timestamp** 列には、イベントに割り当てられた最後のタイムスタンプが含まれます。

**誤順序の許容期間**
* 順序を逸脱しているものの、設定された「誤順序の許容期間」内であるイベントは、**タイムスタンプに基づいて並べ替え**られます。 
* 許容範囲を超えたイベントは、**削除または調整**されます。
    * **調整**: 許容される最新の時刻に届いたかのように調整されます。 
    * **削除**: 破棄されます。

「誤順序の許容期間」内に届いたイベントを並べ替えるために、クエリの出力は**誤順序の許容期間だけ遅延**されます。

**例**

* 到着遅延許容期間 = 10 分<br/>
* 誤順序の許容期間 = 3 分<br/>
* アプリケーション時間による処理<br/>
* イベント:
   * イベント 1 "_アプリケーション時間_" = 00:00:00、"_到着時刻_" = 00:10:01、_System.Timestamp_ = 00:00:01、("_到着時刻_" - "_アプリケーション時間_") は到着遅延許容期間を超えているため、調整されます。
   * イベント 2 "_アプリケーション時間_" = 00:00:01、"_到着時刻_" = 00:10:01、_System.Timestamp_ = 00:00:01、アプリケーション時間は到着遅延期間の範囲内であるため、調整されません。
   * イベント 3 "_アプリケーション時間_" = 00:10:00、"_到着時刻_" = 00:10:02、_System.Timestamp_ = 00:10:00、アプリケーション時間は到着遅延期間の範囲内であるため、調整されません。
   * イベント 4 "_アプリケーション時間_" = 00:09:00、"_到着時刻_" = 00:10:03、_System.Timestamp_ = 00:09:00、アプリケーション時間は誤順序の許容期間の範囲内であるため、元のタイムスタンプで承認されます。
   * イベント 5 "_アプリケーション時間_" = 00:06:00、"_到着時刻_" = 00:10:04、_System.Timestamp_ = 00:07:00、アプリケーション時間は誤順序の許容期間よりも古いため、調整されます。

## <a name="practical-considerations"></a>実用的な考慮事項
前述のように、"*到着遅延許容*" は、アプリケーション時間と到着時刻の差の最大値です。
アプリケーション時間で処理する場合、"*誤順序の許容*" 設定を適用する前に、構成済みの "*到着遅延許容*" の範囲を超えたイベントが調整されます。 そのため、有効な誤順序は、到着遅延許容と誤順序の許容の最小値になります。

ストリーム内で順不同のイベントが発生する原因は次のとおりです。
* 送信元の間での時刻のずれ。
* 送信元と入力イベント ソースの間の変わりやすい待機時間。

到着遅延が発生する原因は次のとおりです。
* 送信元がイベントをバッチ処理し、間隔を置いてからイベントを送信する。
* 送信元がイベントを送信してから入力ソースでイベントを受信するまでの待機時間。

特定のジョブの "*到着遅延許容*" と "*誤順序の許容*" を構成するときは、正確さ、待機時間の要件、上記の要因を考慮する必要があります。

以下に例を示します。

### <a name="example-1"></a>例 1: 
クエリに "Partition by PartitionId" 句が含まれており、1 つのパーティション内で同期送信メソッドを使用してイベントが送信されます。 同期送信メソッドは、イベントが送信されるまでブロックします。
この場合、次のイベントが送信される前に明示的な確認が行われ、イベントが順番に送信されるため、誤順序はありません。 到着遅延は、イベントが生成されてからイベントが送信されるまでの最大遅延時間 + 送信元と入力ソースの間の最大待機時間になります。

### <a name="example-2"></a>例 2:
クエリに "Partition by PartitionId" 句が含まれており、1 つのパーティション内で非同期送信メソッドを使用してイベントが送信されます。 非同期送信メソッドは複数の送信を同時に開始できるので、順不同のイベントが発生する可能性があります。
この場合、誤順序と到着遅延の両方が、少なくとも、イベントが生成されてからイベントが送信されるまでの最大遅延時間 + 送信元と入力ソースの間の最大待機時間になります。

### <a name="example-3"></a>例 3:
クエリに "Partition by PartitionId" 句が含まれておらず、少なくとも 2 つのパーティションがあります。
構成は例 2 と同じです。 ただし、パーティションのいずれかにデータがない場合、到着遅延許容期間の追加によって出力が遅延する可能性があります。

## <a name="to-summarize"></a>まとめ
* 到着遅延許容期間と誤順序の許容期間は、正確さと待機時間の要件に基づいて構成する必要があり、イベントの送信方法も考慮する必要があります。
* 誤順序の許容期間を到着遅延許容期間よりも短くすることをお勧めします。
* 複数のタイムラインを組み合わせる場合、ソースまたはパーティションのいずれかにデータがないと、到着遅延許容期間の追加によって出力が遅延する可能性があります。

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Stream Analytics の概要](stream-analytics-introduction.md)
* [Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics ジョブのスケール設定](stream-analytics-scale-jobs.md)
* [Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics 管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)
