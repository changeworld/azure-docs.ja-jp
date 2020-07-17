---
title: Azure Event Grid イベントに対するイベント ハンドラーとしての Azure 関数
description: Event Grid イベントのイベント ハンドラーとして Azure Functions を使用する方法について説明します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 3ff3c0013cb7a373461b997b9922612763461b8d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595651"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Event Grid イベントに対するイベント ハンドラーとしての Azure 関数

イベント ハンドラーは、イベントの送信先となる場所です。 ハンドラーでは、イベントを処理するアクションが実行されます。 一部の Azure サービスは、イベントを処理するように自動的に構成されます。**Azure Functions** はその 1 つです。 

サーバーレス アーキテクチャで Event Grid からのイベントに応答するには、**Azure Functions** を使用します。 ハンドラーとして Azure 関数を使用する場合は、汎用 HTTP トリガーではなく Event Grid トリガーを使用します。 Event Grid では、Event Grid トリガーが自動的に検証されます。 汎用 HTTP トリガーの場合は、[検証応答](webhook-event-delivery.md)を開発者自身が実装する必要があります。

関数での Event Grid トリガーの使用の詳細については、「[Azure Functions の Event Grid トリガー](../azure-functions/functions-bindings-event-grid.md)」を参照してください。

## <a name="tutorials"></a>チュートリアル

|タイトル  |説明  |
|---------|---------|
| [クイック スタート: 関数を使用したイベントの処理](custom-event-to-function.md) | 処理するカスタム イベントを関数に送信します。 |
| [チュートリアル: Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する](resize-images-on-storage-blob-upload-event.md) | ユーザーは、Web アプリを使ってストレージ アカウントにイメージをアップロードします。 ストレージ BLOB が作成されると、Event Grid によって関数アプリにイベントが送信され、アップロードされたイメージのサイズが変更されます。 |
| [チュートリアル: ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md) | Event Hubs によってキャプチャ ファイルが作成されると、Event Grid が関数アプリにイベントを送信します。 アプリは Capture ファイルを取得し、データ ウェアハウスにデータを移行します。 |
| [チュートリアル:Azure Service Bus の Azure Event Grid への統合の例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid では、Service Bus トピックからのメッセージが、関数アプリとロジック アプリに送信されます。 |


## <a name="next-steps"></a>次のステップ
サポートされているイベント ハンドラーの一覧については、「[イベント ハンドラー](event-handlers.md)」を参照してください。 
