---
title: Azure Event Grid イベントに対するイベント ハンドラーとしてのイベント ハブ
description: Azure Event Grid イベントのイベント ハンドラーとしてイベント ハブを使用する方法について説明します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: c9ce4e7da51005dcb06c9df420d80f4d2c7b93e9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596021"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Azure Event Grid イベントに対するイベント ハンドラーとしてのイベント ハブ
イベント ハンドラーは、イベントの送信先となる場所です。 ハンドラーでは、イベントを処理するアクションが実行されます。 一部の Azure サービスは、イベントを処理するように自動的に構成されます。**Azure Event Hubs** はその 1 つです。 

Event Grid からのイベントが速すぎて、ソリューションで処理しきれない場合は、**Event Hubs** を使用します。 イベントがイベント ハブに格納されたら、アプリケーションでは独自のスケジュールでイベント ハブからイベントを取得して処理できます。 イベント処理をスケーリングして、受信イベントを処理することができます。

## <a name="tutorials"></a>チュートリアル
次の例を参照してください。 

|タイトル  |説明  |
|---------|---------|
| [クイック スタート: Azure CLI を使用してカスタム イベントを Azure Event Hubs にルーティングする](custom-event-to-eventhub.md) | アプリケーションで処理するためにカスタム イベントをイベント ハブに送信します。 |
| [Resource Manager テンプレート:Event Grid カスタム トピックを作成し、イベント ハブにイベントを送信する](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| カスタム トピックのサブスクリプションを作成する Resource Manager テンプレート。 Azure Event Hubs にイベントを送信します。 |

## <a name="message-properties"></a>メッセージのプロパティ
Event Grid からのイベントのイベント ハンドラーとして**イベント ハブ**を使用する場合は、次のメッセージ ヘッダーを設定します。 

| プロパティ名 | 説明 |
| ------------- | ----------- | 
| aeg-subscription-name | イベント サブスクリプションの名前。 |
| aeg-delivery-count | <p>イベントに対して行われた試行の回数。</p> <p>例:"1"</p> |
| aeg-event-type | <p>イベントの種類。</p><p> 例:"Microsoft.Storage.blobCreated"</p> | 
| aeg-metadata-version | <p>イベントのメタデータ バージョン。</p> <p>例:"1"。</p><p> **Event Grid のイベント スキーマ**の場合、このプロパティはメタデータのバージョンを表し、**クラウド イベント スキーマ**の場合は**仕様のバージョン**を表します。 </p>|
| aeg-data-version | <p>イベントのデータ バージョン。</p><p>例:"1"。</p><p>**Event Grid のイベント スキーマ**の場合、このプロパティはデータのバージョンを表し、**クラウド イベント スキーマ**の場合は適用されません。</p> |
| aeg-output-event-id | Event Grid イベントの ID。 |


## <a name="next-steps"></a>次のステップ
サポートされているイベント ハンドラーの一覧については、「[イベント ハンドラー](event-handlers.md)」を参照してください。 
