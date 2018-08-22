---
title: Azure Event Grid による配信と再試行
description: Azure Event Grid がイベントをどのように配信し、未配信メッセージをどのように処理するかについて説明します。
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: tomfitz
ms.openlocfilehash: b34386a7b416d6f7d8b008a9cb5ef142948a370f
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005397"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid によるメッセージの配信と再試行 

この記事では、配信が確認されないときに Azure Event Grid がイベントをどのように処理するかについて説明します。

Event Grid は、持続性のある配信を提供します。 各サブスクリプションに対して、最低 1 回は各メッセージを配信します。 イベントは、各サブスクリプションの登録済みの Webhook にすぐに送信されます。 1 回目の配信で、Webhook がイベントの受信を 60 秒以内に確認しなかった場合、Event Grid はそのイベントの配信を再試行します。 

現時点では、Event Grid はサブスクライバーへ各イベントを個別に送信します。 サブスクライバーは、イベント 1 つが格納された配列を受け取ります。

## <a name="message-delivery-status"></a>メッセージの配信状態

Event Grid は、HTTP 応答コードを使用してイベントの受信を確認します。 

### <a name="success-codes"></a>成功コード

次の HTTP 応答コードは、イベントが Webhook に正常に配信されていることを示します。 Event Grid は、配信が完了したとみなします。

- 200 OK
- 202 受理されました

### <a name="failure-codes"></a>エラー コード

次の HTTP 応答コードは、イベントの配信が失敗したことを示します。 

- 400 Bad Request
- 401 権限がありません
- 404 見つかりません
- 408 要求がタイムアウトしました
- 414 URI が長すぎます
- 429 Too Many Requests
- 500 内部サーバー エラー
- 503 サービス利用不可
- 504 ゲートウェイ タイムアウト

Event Grid でエンドポイントを一時的に利用できないことを示すエラーが受信された場合、または以降の要求が成功する可能性がある場合は、イベントの送信が再試行されます。 Event Grid で配信が成功しないことを示すエラーが受信され、[配信不能エンドポイントが構成されている](manage-event-delivery.md)場合、配信不能エンドポイントにイベントが送信されます。 

## <a name="retry-intervals-and-duration"></a>再試行間隔と期間

Event Grid は、イベント配信に対して指数バックオフ再試行ポリシーを使用します。 Webhook が応答しない、またはエラー コードが返らない場合、Event Grid は、次のスケジュールで配信を再試行します。

1. 10 秒
2. 30 秒
3. 1 分
4. 5 分
5. 10 分
6. 30 分
7. 1 時間

Event Grid は、すべての再試行間隔に小さなランダム化を追加します。 1 時間より後は、イベント配信が 1 時間に 1 回再試行されます。

Event Grid では、既定で 24 時間内に配信されないすべてのイベントが有効期限切れになります。 イベント サブスクリプションの作成時には、[再試行ポリシーをカスタマイズする](manage-event-delivery.md)ことができます。 配信の最大試行回数 (既定値は 30) と、イベントの有効期限 (既定値は 1,440 分) を指定します。

## <a name="dead-letter-events"></a>配信不能イベント

Event Grid がイベントを配信できない場合は、配信不能イベントをストレージ アカウントに送信できます。 このプロセスは配信不能処理と呼ばれます。 配信されなかったイベントを表示するために、配信不能の場所からそのイベントをプルすることができます。 詳細については、「[Event Grid の配信設定の管理](manage-event-delivery.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* イベント配信のステータスを表示するには、[Event Grid によるメッセージの配信の監視](monitor-event-delivery.md) に関する記事をご覧ください。
* イベント配信オプションをカスタマイズする場合は、「[Event Grid の配信設定の管理](manage-event-delivery.md)」を参照してください。
* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。