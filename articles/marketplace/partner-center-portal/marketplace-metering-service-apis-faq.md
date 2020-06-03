---
title: 測定サービス API に関する FAQ - Microsoft コマーシャル マーケットプレース
description: Microsoft AppSource および Azure Marketplace の SaaS オファー用の測定サービス API についてよく寄せられる質問。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: eb27089777baaaa7a29e020318fbc7635792af2d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857904"
---
# <a name="marketplace-metering-service-apis---faq"></a>Marketplace の測定サービス API - FAQ

従量制課金が含まれる SaaS サービスに Azure ユーザーが登録した場合、その顧客が使用している請求ディメンションごとに使用量を追跡します。 顧客が選択した条件に対して設定されている含まれる数量を消費量が超えた場合、サービスでは Microsoft に対する使用状況イベントが生成されます。

## <a name="emit-usage-events"></a>使用状況イベントを生成する

>[!Note]
>このセクションは、少なくとも 1 つのプランに対してオファーの発行時に測定サービス ディメンションが定義されている SaaS オファーにのみ適用されます。

![使用状況イベントを生成する](media/isv-emits-usage-event.png)

使用状況イベントを生成するための API コントラクトについては、[SaaS バッチ使用状況イベント API](./marketplace-metering-service-apis.md#batch-usage-event) に関するページをご覧ください。

### <a name="how-often-is-it-expected-to-emit-usage"></a>使用状況はどのくらいの頻度で生成されますか?

最善の場合で、過去 1 時間に使用量がある場合にのみ、1 時間ごとに過去 1 時間の使用状況が生成されます。

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>イベントが発生してから使用状況イベントが Microsoft に送信されるまでの最大遅延時間はどれくらいですか?

最善の場合で、使用状況イベントは、過去 1 時間に発生したイベントに対して 1 時間ごとに生成されます。 ただし、遅延が想定されます。 許容される最大遅延時間は 24 時間で、これを過ぎると使用状況イベントは受け入れられません。

たとえば、使用状況イベントがある日の午後 1 時に発生した場合、翌日の午後 1 時までは、このイベントに関連付けられた使用状況イベントが送信される可能性があります。 使用状況を送信するシステムでダウン タイムが発生した場合、復旧した後、忠実性を損なうことなく使用状況が発生した時間間隔で使用状況イベントが送信されます。

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>同じ時間に複数の使用状況イベントを送信するとどうなりますか?

1 時間の間隔で受け入れられる使用状況イベントは 1 つだけです。 時間間隔は 0 分から始まり、59 分で終わります。  同じ時間間隔で複数の使用状況イベントが生成された場合、後続の使用状況イベントは重複として削除されます。

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>既に登録が解除されている SaaS サブスクリプションの使用状況を生成するとどうなりますか?

SaaS サブスクリプションが削除された後で Marketplace プラットフォームに対して生成された使用状況イベントは受け入れられません。

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>アクティブなサブスクリプションと登録解除されたサブスクリプションを含む、すべての SaaS サブスクリプションの一覧を取得できますか?

はい。`GET /saas/subscriptions` API を呼び出すと、すべての SaaS サブスクリプションの一覧が含まれます。 各 SaaS サブスクリプションに対する応答の状態フィールドで、サブスクリプションがアクティブであるか、または登録解除されているかが、示されています。 サブスクリプションの一覧表示を呼び出すと、最大 100 個のサブスクリプションが返されます。

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Marketplace の測定サービスで障害が発生した場合はどうなりますか?

ISV がカスタム測定を送信し、エラーを受信した場合、ISV は待機してから再試行する必要があります。

エラーが引き続き発生する場合は、その後 1 時間にわたってそのカスタム測定を再送信します (数量を累積します)。 エラー以外の応答を受信するまでこのプロセスを継続します。

## <a name="next-steps"></a>次のステップ

- 詳細については、[Marketplace の測定サービス API](./marketplace-metering-service-apis.md) に関する記事をご覧ください。
