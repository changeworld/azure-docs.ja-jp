---
title: Azure Event Grid のイベント ソース
description: Azure Event Grid でサポートされるイベント ソースについて説明します
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: tomfitz
ms.openlocfilehash: f9c3bcb6b92b43fe5b5bad72c99e6ce199c17448
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302128"
---
# <a name="event-sources-in-azure-event-grid"></a>Azure Event Grid のイベント ソース

イベント ソースは、イベントの発生場所です。 一部の Azure サービスは、イベントを送信するように自動的に構成されます。 イベントを送信するカスタム アプリケーションを作成することもできます。 Event Grid を使ってイベントを配布するために、カスタム アプリケーションを Azure でホストする必要はありません。

この記事では、各イベント ソースに関するコンテンツへのリンクを示します。

## <a name="azure-subscriptions"></a>Azure サブスクリプション

Azure サブスクリプションでのリソースの変更に対応するために、Azure サブスクリプションのイベントをサブスクライブします。

|タイトル |[説明]  |
|---------|---------|
| [Azure Automation を Event Grid および Microsoft Teams と統合する](ensure-tags-exists-on-new-virtual-machines.md) |イベントを送信する仮想マシンを作成します。 このイベントは、仮想マシンをタグ付けする Automation Runbook をトリガーし、Microsoft Teams チャネルに送信されるメッセージをトリガーします。 |
| [イベント スキーマ](event-schema-subscriptions.md) | Azure サブスクリプション イベントのフィールドを示します。 |

## <a name="custom-topics"></a>カスタム トピック

アプリケーションのイベントに対応するために、カスタム トピックをサブスクライブします。

|タイトル  |[説明]  |
|---------|---------|
| [Azure CLI を使ったカスタム イベントの作成とルーティング](custom-event-quickstart.md) | Azure CLI を使ってカスタム イベントを送信する方法を示します。 |
| [Azure PowerShell を使ったカスタム イベントの作成とルーティング](custom-event-quickstart-powershell.md) | Azure PowerShell を使ってカスタム イベントを送信する方法を示します。 |
| [Azure portal を使ったカスタム イベントの作成とルーティング](custom-event-quickstart-portal.md) | ポータルを使ってカスタム イベントを送信する方法を示します。 |
| [カスタム トピックへの投稿](post-to-custom-topic.md) | カスタム トピックにイベントを投稿する方法を示します。 |
| [Azure Queue Storage にカスタム イベントをルーティングする](custom-event-to-queue-storage.md) | Queue Storage にカスタム イベントを送信する方法について説明します。 |
| [イベント スキーマ](event-schema.md) | カスタム イベントのフィールドを示します。 |

## <a name="event-hubs"></a>Event Hubs

Capture ファイル イベントに対応するために、Event Hubs のイベントをサブスクライブします。

|タイトル  |[説明]  |
|---------|---------|
| [ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md) | Event Hubs によってキャプチャ ファイルが作成されると、Event Grid が関数アプリにイベントを送信します。 アプリは Capture ファイルを取得し、データ ウェアハウスにデータを移行します。 |
| [イベント スキーマ](event-schema-event-hubs.md) | Event Hubs イベントのフィールドを示します。 |

## <a name="iot-hub"></a>IoT Hub

デバイスの作成および削除のイベントに対応するために、IoT Hub のイベントをサブスクライブします。

|タイトル  |[説明]  |
|---------|---------|
| [Logic Apps を使用して Azure IoT Hub イベントに関する電子メール通知を送信する](publish-iot-hub-events-to-logic-apps.md) | ロジック アプリは、IoT ハブにデバイスが追加されるたびに、通知の電子メールを送信します。 |
| [Event Grid を使用し IoT Hub のイベントに対応してアクションをトリガーする](../iot-hub/iot-hub-event-grid.md) | Iot Hubs と Event Grid の統合の概要です。 |
| [イベント スキーマ](event-schema-iot-hub.md) | IoT Hub イベントのフィールドを示します。 |

## <a name="media-services"></a>Media Services

ジョブ状態のイベントに対応するため、Media Services のイベントをサブスクライブします。

|タイトル  |[説明]  |
|---------|---------|
| [Media Services イベントへの対応](../media-services/latest/reacting-to-media-services-events.md) | Media Services と Event Grid の統合の概要です。 |
| [CLI を使用して Azure Media Services のイベントをカスタム Web エンドポイントにルーティングする](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Media Services からイベントを送信する方法を示します。 |
| [イベント スキーマ](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Media Services イベントのフィールドを示します。 |

## <a name="resource-groups"></a>リソース グループ

リソース グループのリソースの変更に対応するため、リソース グループのイベントをサブスクライブします。

|タイトル  |[説明]  |
|---------|---------|
| [Azure Event Grid と Logic Apps で仮想マシンの変更を監視する](monitor-virtual-machine-changes-event-grid-logic-app.md) | ロジック アプリは仮想マシンへの変更を監視し、それらの変更に関する電子メールを送信します。 |
| [イベント スキーマ](event-schema-resource-groups.md) | リソース グループ イベントのフィールドを示します。 |

## <a name="service-bus"></a>Service Bus

アクティブなリスナーのいないメッセージに対応するため、Service Bus のイベントをサブスクライブします。

|タイトル  |[説明]  |
|---------|---------|
| [Azure Service Bus と Azure Event Grid の統合の例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid は、Service Bus トピックから関数アプリとロジック アプリにメッセージを送信します。 |
| [Azure Service Bus と Event Grid の統合の概要](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Service Bus と Event Grid の統合の概要です。 |
| [イベント スキーマ](event-schema-service-bus.md) | Service Bus イベントのフィールドを示します。 |

## <a name="storage"></a>Storage

BLOB の作成および削除のイベントに対応するために、Blob Storage のイベントをサブスクライブします。

|タイトル  |[説明]  |
|---------|---------|
| [Azure CLI で Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure CLI を使って Blob Storage イベントを送信する方法を示します。 |
| [PowerShell を使って Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure PowerShell を使って Blob Storage イベントを送信する方法を示します。 |
| [Blob Storage のイベント処理](../storage/blobs/storage-blob-event-overview.md) | Blob Storage と Event Grid の統合の概要です。 |
| [イベント スキーマ](event-schema-blob-storage.md) | Blob Storage イベントのフィールドを示します。 |

## <a name="next-steps"></a>次の手順

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
