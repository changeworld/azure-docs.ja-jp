---
title: Azure Time Series Insights データの監視のリファレンス | Microsoft Docs
description: Azure Time Series Insights の監視に関するリファレンス ドキュメント。
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: bfd0d04313f0b519b4013a43e29d88400c73ea31
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591389"
---
# <a name="monitoring-azure-time-series-insights-data-reference"></a>Azure Time Series Insights データの監視のリファレンス

ご利用の Azure Time Series Insights 環境から Azure Monitor によって収集されるデータとリソースについて説明します。 監視データの収集と分析の詳細については、「[Time Series Insights の監視]( ./how-to-monitor-tsi.md)」を参照してください。

## <a name="metrics"></a>メトリック

このセクションには、Azure Time Series Insights 用に自動的に収集されたすべてのプラットフォーム メトリックが一覧表示されています。 すべての Azure Monitor サポート メトリック (Azure Time Series Insights を含む) の一覧については、[Azure Monitor でサポートされているメトリック](../azure-monitor/essentials/metrics-supported.md)に関する記事を参照してください。 これらのメトリックのリソース プロバイダーは、[Microsoft.TimeSeriesInsights/environments/eventsources](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironmentseventsources) および [Microsoft.TimeSeriesInsights/environments](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironments) です。


### <a name="ingress"></a>イングレス
 
|メトリック|メトリックの表示名|ユニット|集計の種類|説明|
|---|---|---|---|---|
|IngressReceivedBytes|Ingress Received Bytes (受信バイトの受信)|バイト|合計|イベント ソースから読み取られたバイト数|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (無効な受信メッセージの受信)|Count|合計|イベント ソースから読み取られた無効なメッセージの数|
|IngressReceivedMessages|Ingress Received Messages (受信メッセージの受信)|Count|合計|イベント ソースから読み取られたメッセージの数|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (受信メッセージの受信のカウント ラグ)|Count|Average|イベント ソース パーティションで最後にエンキューされたメッセージのシーケンス番号とイングレスで処理されているメッセージのシーケンス番号の間の差|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (受信メッセージの受信のタイム ラグ)|Seconds|最大値|メッセージがイベント ソースでエンキューされた時刻とそれがイングレスで処理された時刻の間の差|
|IngressStoredBytes|Ingress Stored Bytes (保存済みバイトの受信)|バイト|合計|正常に処理され、クエリで利用できるイベントの合計サイズ|
|IngressStoredEvents|Ingress Stored Events (保存済みイベントの受信)|Count|合計|正常に処理され、クエリで利用できるフラット化されたイベントの数|

### <a name="storage"></a>ストレージ

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|
|---|---|---|---|---|
|WarmStorageMaxProperties|ウォーム ストレージの最大プロパティ数|Count|最大値|S1/S2 SKU の環境で許可されているプロパティの最大数と PAYG SKU のウォーム ストアで許可されたプロパティの最大数|
|WarmStorageUsedProperties|ウォーム ストレージ使用済みプロパティ数 |Count|最大値|S1/S2 SKU の環境で使用されているプロパティ数と PAYG SKU のウォーム ストアで使用されているプロパティ数|

## <a name="resource-logs"></a>リソース ログ

このセクションには、Azure Time Series Insights 環境用に収集できるリソース ログの種類が一覧表示されています。

| カテゴリ | 表示名 | 説明 |
|----- |----- |----- |
| イングレス | TSIIngress | イングレス カテゴリでは、イングレス パイプラインで発生したエラーが追跡されます。 このカテゴリには、イベントの受信時に発生するエラー (イベント ソースへの接続エラーなど) とイベントの処理時に発生するエラー (イベント ペイロード解析時のエラーなど) が含まれます。 |

## <a name="schemas"></a>スキーマ
次のスキーマは、Azure Time Series Insights によって使用されています

### <a name="tsiingress-table"></a>TSIIngress テーブル

| プロパティ | 説明 |
|----- |----- |
| TimeGenerated | このイベントが生成された時刻 (UTC)。 |
| Location | リソースの場所。 |
| カテゴリ | ログ イベントのカテゴリ。 |
| OperationName | イベントの操作名。 |
| CorrelationId | 要求に対する関連付け ID。 |
| Level | イベントの重大度レベル。 |
| ResultDescription | 操作の結果についての説明 ("禁止エラーを受信しました" など)。 |
| Message | エラーに関連付けられているメッセージ。 問題の内容およびエラーを軽減する方法に関する詳細が含まれます。 |
| ErrorCode | エラーに関連付けられたコード。 |
| EventSourceType | イベント ソースの種類。 イベント ハブまたは IoT ハブのいずれかです。 |
| EventSourceProperties | イベント ソースに固有のプロパティのコレクション。 コンシューマー グループやアクセス キー名などの詳細が含まれます。 |
