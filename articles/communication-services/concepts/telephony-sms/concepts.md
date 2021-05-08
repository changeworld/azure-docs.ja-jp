---
title: Azure Communication Services での SMS の概念
titleSuffix: An Azure Communication Services concept document
description: Communication Services SMS の概念について説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e879b7938eb778d4cdbbef4a970325501a6124db
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932150"
---
# <a name="sms-concepts"></a>SMS の概念

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-phone-numbers.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure Communication Services では、Communication Services SMS SDK を使用して SMS テキスト メッセージを送受信できます。 これらの SDK を使用して、カスタマー サービスのシナリオ、予定リマインダー、2 要素認証、その他のリアルタイム通信のニーズをサポートできます。 Communication Services SMS を使用すると、メッセージを確実に送信しながら、配信性と反応率のメトリクスを明らかにすることができます。

Azure Communication Services SMS SDK の主な機能を次に示します。

-  アプリケーションに SMS 機能を追加するための **シンプルな** セットアップ エクスペリエンス。
- 米国内での A2P (アプリケーション対人) ユース ケースのフリー ダイヤル番号で **高速** メッセージをサポート。
- **一括メッセージング** をサポートしているため、一度に複数の受信者にメッセージを送信可能。
- カスタマー サポート、アラート、予定リマインダーなどのシナリオをサポートする **双方向** の会話。
- アプリケーションから送信されるメッセージのリアルタイム配信レポートを備えた **信頼性の高い配信**。
- SMS の使用パターンを追跡するための **分析**。
- フリー ダイヤル番号のオプトアウトを自動的に検出して配慮するための **オプトアウト** 処理のサポート。 米国の無料電話番号に使用されるオプトアウトは、米国の通信事業者によって管理、適用されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [SMS の送信を開始する](../../quickstarts/telephony-sms/send.md)

次のドキュメントもご覧ください。

- [SMS SDK](../telephony-sms/sdk-features.md) について理解を深める
- SMS 対応の[電話番号](../../quickstarts/telephony-sms/get-phone-number.md)を取得する
- [Azure Communication Services での電話番号の種類](../telephony-sms/plan-solution.md)
