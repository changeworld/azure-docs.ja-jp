---
title: SaaS サービスでの Webhook の実装 | Microsoft コマーシャル マーケットプレース
description: Fulfillment API バージョン 2 を使用して SaaS サービスに Webhook を実装する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: 6cf50327719b434cda7d5df4e3df99b9d96a1dcd
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063884"
---
# <a name="implementing-a-webhook-on-the-saas-service"></a>SaaS サービスでの Webhook の実装

パートナー センターで取引可能な SaaS オファーを作成する場合、パートナーは、HTTP エンドポイントとして使用される **接続 Webhook** の URL を指定します。  この Webhook は、Microsoft 側で発生する次のイベントを公開元に通知するために、POST HTTP 呼び出しを使用して Microsoft によって呼び出されます。

* SaaS サブスクリプションが *Subscribed* 状態である場合:
    * ChangePlan
    * ChangeQuantity
    * 更新
    * [中断]
    * サブスクライブ解除
* SaaS サブスクリプションが *Suspended* 状態である場合:
    * Reinstate
    * サブスクライブ解除

発行者は、SaaS サブスクリプションの状態を Microsoft 側と一貫性のある状態に保つために、SaaS サービスに Webhook を実装する必要があります。  SaaS サービスでは、Webhook 通知に基づいてアクションを実行する前に、Get Operation API を呼び出して、Webhook 呼び出しとペイロード データを検証および承認する必要があります。  Webhook 呼び出しが処理されるとすぐに、発行者から Microsoft に HTTP 200 を返す必要があります。  この値は、Webhook 呼び出しが発行者によって正常に受信されたことを確認します。

> [!IMPORTANT]
> Webhook URL サービスは、24 時間 365 日稼働し、いつでも Microsoft からの新しい呼び出しを受け取る準備ができている必要があります。  Microsoft には、Webhook 呼び出しの再試行ポリシー (再試行は 8 時間で 500 回) があります。ただし、公開元が呼び出しを受け入れて応答を返さない場合、Webhook で通知される操作は最終的に Microsoft 側で失敗します。

*購入イベントの Webhook ペイロードの例:*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": "25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

*サブスクリプション復帰イベントの Webhook ペイロードの例:*

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": "20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "InProgress"
}
```

*更新イベントの Webhook ペイロードの例:*

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer1 ",
  "planId": "silver",
  "quantity": "25",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Renew",
  "status": "Success"
}
```

## <a name="development-and-testing"></a>開発とテスト

開発プロセスを開始するには、発行者側でダミー API 応答を作成することをお勧めします。  これらの応答は、この記事に記載されているサンプル応答に基づくことができます。

発行者がエンドツーエンド テストの準備ができたら、次のようになります。

* SaaS オファーを限られたプレビュー対象ユーザーに公開し、プレビュー段階のままにします。
* テスト中に実際の請求費用が発生しないように、プランの価格を 0 に設定します。  別の方法として、0 以外の価格を設定し、すべてのテスト購入を 24 時間以内に取り消すこともできます。
* 実際の顧客のシナリオをシミュレートするために、すべてのフローがエンド ツー エンドで呼び出されていることを確認します。
* パートナーが購入および請求の完全なフローをテストする必要がある場合は、0 ドルより高い価格のオファーを使用して、それを行ってください。  購入に対して請求が行われ、請求書が生成されます。

購入フローは、オファーが公開されている場所に応じて、Azure portal または Microsoft AppSource サイトからトリガーできます。

"*プラン変更*"、"*数量変更*"、"*登録解除*" の各アクションは、発行者側からテストされます。  Microsoft 側からは、Azure portal と管理センター (Microsoft AppSource での購入が管理されているポータル) の両方から、"*登録解除*" をトリガーできます。  "*数量とプランの変更*" は、管理センターからのみ実行できます。

## <a name="get-support"></a>サポートを受ける

発行者サポート オプションについては、「[パートナー センターでの商業マーケットプレース プログラムのサポート](../support.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

コマーシャル マーケットプレースでの SaaS オファーのその他のオプションについては、[コマーシャル マーケットプレースの測定サービス API](../marketplace-metering-service-apis.md) を参照してください。

[さまざまなプログラミング言語のクライアントを確認して、サンプルを使用します](https://github.com/microsoft/commercial-marketplace-samples)。
