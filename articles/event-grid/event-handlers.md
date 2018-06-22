---
title: Azure Event Grid イベント ハンドラー
description: Azure Event Grid でサポートされるイベント ハンドラーについて説明します
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: tomfitz
ms.openlocfilehash: 7c012bdf025a352788aec2d2d70bab33d7914577
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849544"
---
# <a name="event-handlers-in-azure-event-grid"></a>Azure Event Grid のイベント ハンドラー

イベント ハンドラーは、イベントの送信先となる場所です。 ハンドラーは、さらにいくつかのアクションを行ってイベントを処理します。 一部の Azure サービスは、イベントを処理するように自動的に構成されます。 イベント処理のための Webhook を使うこともできます。 Webhook は、イベント処理用に Azure でホストする必要はありません。 Event Grid では、HTTPS webhook エンドポイントのみがサポートされます。

この記事では、各イベント ハンドラーに関するコンテンツへのリンクを示します。

## <a name="azure-automation"></a>Azure Automation

Azure Automation を使うと、自動化された Runbook でイベントを処理できます。

|タイトル  |説明  |
|---------|---------|
|[Azure Automation を Event Grid および Microsoft Teams と統合する](ensure-tags-exists-on-new-virtual-machines.md) |イベントを送信する仮想マシンを作成します。 このイベントは、仮想マシンをタグ付けする Automation Runbook をトリガーし、Microsoft Teams チャネルに送信されるメッセージをトリガーします。 |

## <a name="azure-functions"></a>Azure Functions

Azure Functions を使うと、サーバーを介さずにイベントに応答できます。

ハンドラーとして Azure Functions を使用する場合は、汎用 HTTP トリガーではなく Event Grid トリガーを使用します。 Event Grid では、Event Grid 関数トリガーが自動的に検証されます。 汎用 HTTP トリガーの場合は、[検証応答](security-authentication.md#webhook-event-delivery)を実装する必要があります。

|タイトル  |説明  |
|---------|---------|
| [Azure Functions の Event Grid トリガー](../azure-functions/functions-bindings-event-grid.md) | 関数での Event Grid トリガーの使用に関する概要です。 |
| [Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する](resize-images-on-storage-blob-upload-event.md) | ユーザーは、Web アプリを使ってストレージ アカウントにイメージをアップロードします。 ストレージ BLOB が作成されると、Event Grid によって関数アプリにイベントが送信され、アップロードされたイメージのサイズが変更されます。 |
| [ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md) | Event Hubs によってキャプチャ ファイルが作成されると、Event Grid が関数アプリにイベントを送信します。 アプリはキャプチャ ファイルを取得し、データ ウェアハウスにデータを移行します。 |
| [Azure Service Bus と Azure Event Grid の統合の例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid は、Service Bus トピックから関数アプリとロジック アプリにメッセージを送信します。 |

## <a name="event-hubs"></a>Event Hubs

ソリューションで処理できるよりも速くイベントが取得される場合に、Event Hubs を使用します。 アプリケーションでは、独自のスケジュールで Event Hubs からのイベントを処理します。 イベント処理をスケーリングして、受信イベントを処理することができます。

|タイトル  |説明  |
|---------|---------|
| [Azure CLI と Event Grid を使用してカスタム イベントを Azure Event Hubs にルーティングする](custom-event-to-eventhub.md) | アプリケーションで処理するためにカスタム イベントをイベント ハブに送信します。 |

## <a name="hybrid-connections"></a>ハイブリッド接続と

Azure Relay ハイブリッド接続を使用して、エンタープライズ ネットワーク内にあり、パブリックにアクセスできるエンドポイントがないアプリケーションにイベントを送信します。

|タイトル  |説明  |
|---------|---------|
| [ハイブリッド接続にイベントを送信する](custom-event-to-hybrid-connection.md) | リスナー アプリケーションで処理するために既存のハイブリッド接続にカスタム イベントを送信します。 |

## <a name="logic-apps"></a>Logic Apps

Logic Apps を使うと、イベントに応答するためのビジネス プロセスを自動化できます。

|タイトル  |説明  |
|---------|---------|
| [Azure Event Grid と Logic Apps で仮想マシンの変更を監視する](monitor-virtual-machine-changes-event-grid-logic-app.md) | ロジック アプリは仮想マシンへの変更を監視し、それらの変更に関する電子メールを送信します。 |
| [Logic Apps を使って Azure IoT Hub イベントに関する電子メール通知を送信する](publish-iot-hub-events-to-logic-apps.md) | ロジック アプリは、IoT ハブにデバイスが追加されるたびに、通知の電子メールを送信します。 |
| [Azure Service Bus と Azure Event Grid の統合の例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid は、Service Bus トピックから関数アプリとロジック アプリにメッセージを送信します。 |

## <a name="queue-storage"></a>Queue Storage

キュー ストレージを使うと、プルする必要があるイベントを受信できます。 応答に時間がかかりすぎる長期実行プロセスの場合に、Queue Storage を使用できます。 イベントを Queue Storage に送信することで、アプリでは独自のスケジュールでイベントをプルして処理することができます。

|タイトル  |説明  |
|---------|---------|
| [Route custom events to Azure Queue storage with Azure CLI and Event Grid (Azure CLI と Event Grid を使ってカスタム イベントを Azure Queue Storage にルーティングする)](custom-event-to-queue-storage.md) | Queue Storage にカスタム イベントを送信する方法について説明します。 |

## <a name="webhooks"></a>WebHook

Webhook は、イベントに応答するカスタマイズ可能なエンドポイントに対して使います。

|タイトル  |説明  |
|---------|---------|
| [HTTP エンドポイントへのイベントの受信](receive-events.md) | イベント サブスクリプションからイベントを受信する HTTP エンドポイントを検証した後、イベントを逆シリアル化する方法について説明します。 |

## <a name="next-steps"></a>次の手順

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
