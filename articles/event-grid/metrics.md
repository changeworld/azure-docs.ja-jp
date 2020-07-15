---
title: Azure Event Grid でサポートされているメトリック
description: この記事では、Azure Event Grid サービスによってサポートされている Azure Monitor メトリックについて説明します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3b22beafc9f88d2d95b25fd7ad2f2308a4df9097
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116422"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Azure Event Grid でサポートされているメトリック
この記事では、名前空間ごとに分類された Event Grid メトリックの一覧を示します。 

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|トピック|
|PublishFailCount|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|Topic、ErrorType、Error|
|PublishSuccessLatencyInMs|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|なし|
|MatchedEventCount|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName|
|DeliveryAttemptFailCount|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName、Error、ErrorType|
|DeliverySuccessCount|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|Topic、EventSubscriptionName、DomainEventSubscriptionName|
|DroppedEventCount|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName、DropReason|
|DeadLetteredCount|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName、DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|なし|
|PublishFailCount|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|ErrorType、Error|
|UnmatchedEventCount|不一致のイベント|Count|合計|このトピックのどのイベント サブスクリプションにも一致しないイベントの合計|なし|
|PublishSuccessLatencyInMs|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|なし|
|MatchedEventCount|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|EventSubscriptionName|
|DeliveryAttemptFailCount|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|EventSubscriptionName|
|DestinationProcessingDurationInMs|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|EventSubscriptionName|
|DroppedEventCount|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|DropReason、EventSubscriptionName|
|DeadLetteredCount|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|DeadLetterReason、EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|なし|
|PublishFailCount|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|ErrorType、Error|
|UnmatchedEventCount|不一致のイベント|Count|合計|このトピックのどのイベント サブスクリプションにも一致しないイベントの合計|なし|
|PublishSuccessLatencyInMs|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|なし|
|MatchedEventCount|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|EventSubscriptionName|
|DeliveryAttemptFailCount|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|EventSubscriptionName|
|DestinationProcessingDurationInMs|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|EventSubscriptionName|
|DroppedEventCount|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|DropReason、EventSubscriptionName|
|DeadLetteredCount|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|DeadLetterReason、EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|MatchedEventCount|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|なし|
|DeliveryAttemptFailCount|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Error、ErrorType|
|DeliverySuccessCount|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|なし|
|DestinationProcessingDurationInMs|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|なし|
|DroppedEventCount|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|DropReason|
|DeadLetteredCount|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|なし|
|PublishFailCount|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|ErrorType、Error|
|UnmatchedEventCount|不一致のイベント|Count|合計|このトピックのどのイベント サブスクリプションにも一致しないイベントの合計|なし|
|PublishSuccessLatencyInMs|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|なし|

## <a name="next-steps"></a>次のステップ
次の記事を参照してください: [診断ログ](diagnostic-logs.md)
