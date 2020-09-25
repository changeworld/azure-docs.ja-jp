---
title: Azure Communication Services での SMS の概念
titleSuffix: An Azure Communication Services concept document
description: Communication Services SMS の概念について説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e5cfc1e27bae10a1c67e4506afe9db825664785f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945310"
---
# <a name="sms-concepts"></a>SMS の概念

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure Communication Services では、Communication Services SMS クライアント ライブラリを使用して SMS テキスト メッセージを送受信できます。 これらのクライアント ライブラリを使用して、カスタマー サービスのシナリオ、予定リマインダー、2 要素認証、その他のリアルタイム通信のニーズをサポートできます。 Communication Services SMS を使用すると、メッセージを確実に送信しながら、キャンペーンに関連する配信性と反応率の分析情報を明らかにすることができます。

Azure Communication Services SMS クライアント ライブラリの主な機能を次に示します。

-  アプリケーションに SMS 機能を追加するための**シンプルな**セットアップ エクスペリエンス。
- 米国内での A2P (アプリケーション対人) ユース ケースのフリー ダイヤル番号で**高速**メッセージをサポート。
- カスタマー サポート、アラート、予定リマインダーなどのシナリオをサポートする**双方向**の会話。
- アプリケーションから送信されるメッセージのリアルタイム配信レポートを備えた**信頼性の高い配信**。
- 使用パターンと顧客エンゲージメントを追跡するための**分析**。
- フリー ダイヤル番号のオプトアウトを自動的に検出して配慮するための**オプトアウト**処理のサポート。 Communication Services は、STOP と START メッセージを検出し、次の既定の応答をエンド ユーザーに送信します。 
  - STOP - *"You have successfully been unsubscribed to messages from this number. (この番号からメッセージへの登録を正常に解除しました。)Reply START to resubscribe. (再登録するには、START と返信してください。)"*
  - START - *“You have successfully been re-subscribed to messages from this number. (この番号からメッセージに正常に再登録しました。)Reply STOP to unsubscribe. (登録を解除するには、STOP と返信してください。)”*
  - STOP と START メッセージは、あなたにリレーされます。 Azure Communication Services は、通信をオプトアウトした受信者にそれ以上メッセージが送信されないように、これらのオプトアウトを監視して実装することを推奨しています。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [SMS の送信を開始する](../../quickstarts/telephony-sms/send.md)

次のドキュメントもご覧ください。

- [SMS クライアント ライブラリ](../telephony-sms/sdk-features.md)について理解する
- SMS 対応の[電話番号](../../quickstarts/telephony-sms/get-phone-number.md)を取得する
- [SMS ソリューションを計画する](../telephony-sms/plan-solution.md)
