---
title: SMS に関する FAQ
titleSuffix: An Azure Communication Services concept document
description: SMS に関する FAQ
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: reference
ms.service: azure-communication-services
ms.openlocfilehash: 1ba7c730542adb74356d71f2482cce57e633cb65
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645775"
---
# <a name="sms-faq"></a>SMS に関する FAQ

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>顧客は、緊急の目的で Azure Communication Services を使用できますか?

Azure Communication Services は、米国で text-to-911 機能をサポートしませんが、連邦通信委員会 (FCC) の規則に基づいて、これをサポートする義務がある場合があります。  FCC の text-to-911 規則がサービスまたはアプリケーションに適用されるかどうかを評価する必要があります。 これらの規則の対象範囲内で、お客様には 911 テキスト メッセージを要求する緊急コール センターにそれらをルーティングする責任があります。 独自の text-to-911 配信モデルを自由に決定できますが、FCC で受け入れられる方法の 1 つは、ユーザーのモバイル デバイスでネイティブ ダイヤラーを自動的に起動し、基になるモバイル キャリアを通じて 911 テキストを配信することです。

## <a name="are-there-any-limits-on-sending-messages"></a>メッセージの送信に制限はありますか?

SLA に準拠した高品質のサービスを継続的に提供するために、Azure Communication Services では転送率の制限が適用されます (プリミティブごとに異なります)。 制限を超えて API を呼び出した開発者は、429 HTTP 状態コード応答を受信します。 転送率の制限を超える要件が会社にある場合は、phone@microsoft.com までメールでお問い合わせください。

SMS の転送率の制限:

|Operation|Scope|期間 (秒)| 制限 (要求数) | 1 分あたりのメッセージ単位|
|---------|-----|-------------|-------------------|-------------------------|
|メッセージを送信する|数ごと|60|200|200|

## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>Azure Communication Services では、無料電話番号のオプトアウトはどのように処理されますか?

米国の無料電話番号に使用されるオプトアウトは、米国の通信事業者によって管理、適用されます。
- STOP - テキスト メッセージの受信者がオプトアウトを希望する場合、無料電話番号に "STOP" を送信できます。 通信事業者は、STOP に対する既定の応答 "NETWORK MSG: You replied with the word "stop" which blocks all texts sent from this number. (ネットワーク メッセージ: "stop" という単語で応答しました。この番号から送信されたテキストはすべてブロックされます。) Text back "unstop" to receive messages again. (再度メッセージを受信するには、"unstop" というテキストを返信してください。)" を送信します。
- START または UNSTOP - 無料電話番号からのテキスト メッセージに対し、再度受信登録を希望する場合、受信者は、その無料電話番号に "START" または "UNSTOP" を送信できます。 通信事業者は、START と UNSTOP に対する既定の応答として、"NETWORK MSG: You have replied "unstop" and will begin receiving messages again from this number. (ネットワーク メッセージ: 応答として "unstop" が送信されました。この番号からのメッセージの受信が再開されます。) " を送信します。
- Azure Communication Services は STOP メッセージを検出し、以降その受信者に送信されるメッセージをすべてブロックします。 配信レポートには、"Sender blocked for given recipient. (送信者は特定の受信者に関してブロックされています。)" というステータス メッセージで配信に失敗したことが示されます。
- STOP、UNSTOP、START メッセージは、自分にリレー バックされます。 Azure Communication Services では、通信をオプトアウトした受信者にそれ以上メッセージ送信が試行されないように、これらのオプトアウトを監視して実行することが推奨されます。

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>Azure Communication Services を使用してメッセージを受信するにはどのようにしますか?

Azure Communication Services のお客様は、Azure Event Grid を使用して着信メッセージを受信できます。 メッセージを受信するように Event Grid を設定するには、こちらの[クイックスタート](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/handle-sms-events)に従います。

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>長いメッセージ (2048 文字超) は送受信できますか?

Azure Communication Services は、SMS での長いメッセージの送受信をサポートしています。 ただし、一部のワイヤレス キャリアまたはデバイスでは、長いメッセージを受信すると、動作が異なる場合があります。

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>固定電話番号に送信されたメッセージはどのように扱われますか?

米国では、Azure Communication Services は固定電話番号を確認せず、配信のために通信事業者に送信しようとします。 固定電話番号に送信されたメッセージの料金は、お客様に請求されます。 

## <a name="can-i-send-messages-to-multiple-recipients"></a>複数の受信者にメッセージを送信できますか?


はい。1 つの要求を複数の受信者に送信できます。 複数の受信者にメッセージを送信するには、こちらの[クイックスタート](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/send?pivots=programming-language-csharp)に従います。
