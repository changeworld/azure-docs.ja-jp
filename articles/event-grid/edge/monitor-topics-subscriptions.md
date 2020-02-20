---
title: トピックとイベント サブスクリプションの監視 - Azure Event Grid IoT Edge | Microsoft Docs
description: トピックとイベント サブスクリプションを監視する
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086677"
---
# <a name="monitor-topics-and-event-subscriptions"></a>トピックとイベント サブスクリプションを監視する

Edge の Event Grid では、トピックおよびイベント サブスクリプションのさまざまなメトリックが [Prometheus の公開形式](https://prometheus.io/docs/instrumenting/exposition_formats/)で公開されます。 この記事では、使用可能なメトリックとそれらを有効にする方法について説明します。

## <a name="enable-metrics"></a>メトリックの有効化

メトリックを出力するようにモジュールを構成するには、コンテナー作成オプションで `metrics__reporterType` 環境変数を `prometheus` に設定します。

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

メトリックは、http の場合はモジュールの `5888/metrics` で、https の場合は `4438/metrics` で使用できます。 たとえば、http の場合は `http://<modulename>:5888/metrics?api-version=2019-01-01-preview`。 この時点で、メトリック モジュールではエンドポイントをポーリングして、こちらの[アーキテクチャ例](https://github.com/veyalla/ehm)のようにメトリックを収集できます。

## <a name="available-metrics"></a>使用可能なメトリック

トピックとイベント サブスクリプションの両方からメトリックが出力され、イベントの配信とモジュールのパフォーマンスに関する分析情報を得ることができます。

### <a name="topic-metrics"></a>トピックのメトリック

| メトリック | 説明 |
| ------ | ----------- |
| EventsReceived | トピックに発行されたイベントの数
| UnmatchedEvents | トピックに発行されたイベントのうち、イベント サブスクリプションと一致せずにドロップされた数
| SuccessRequests | トピックによって受信されたインバウンド発行要求の数
| SystemErrorRequests | 内部システム エラーが原因で失敗したインバウンド発行要求の数
| UserErrorRequests | JSON の形式が正しくないなどのユーザー エラーが原因で失敗したインバウンド発行要求の数
| SuccessRequestLatencyMs | 発行要求応答の待機時間 (ミリ秒)


### <a name="event-subscription-metrics"></a>イベント サブスクリプションのメトリック

| メトリック | 説明 |
| ------ | ----------- |
| DeliverySuccessCounts | 構成されたエンドポイントに正常に配信されたイベントの数
| DeliveryFailureCounts | 構成されたエンドポイントへの配信に失敗したイベントの数
| DeliverySuccessLatencyMs | 正常に配信されたイベントの待機時間 (ミリ秒)
| DeliveryFailureLatencyMs | 失敗したイベント配信の待機時間 (ミリ秒)
| SystemDelayForFirstAttemptMs | 最初の配信試行までのイベントのシステム遅延 (ミリ秒)
| DeliveryAttemptsCount | イベント配信試行回数 - 成功および失敗
| ExpiredCounts | 有効期限が切れ、構成されたエンドポイントに配信されなかったイベントの数