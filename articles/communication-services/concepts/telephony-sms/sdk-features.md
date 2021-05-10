---
title: Azure Communication Services の SMS SDK の概要
titleSuffix: An Azure Communication Services concept document
description: SMS SDK とそのオファリングの概要を取り上げます。
author: mikben
manager: jken
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c25dfea077510580daf2c1aab584ab9ff5caa7fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105930433"
---
# <a name="sms-sdk-overview"></a>SMS SDK の概要

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-phone-numbers.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure Communication Services の SMS SDK を使用して、アプリケーションに SMS メッセージングを追加できます。

## <a name="sms-sdk-capabilities"></a>SMS SDK の機能

次の一覧は、SDK で現在使用できる機能を示しています。

| 機能のグループ | 機能                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| コア機能 | SMS メッセージを送信および受信する                                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 送信されたメッセージの配信レポートを有効にする                                             | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | すべての文字セット (言語/Unicode のサポート)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 長いメッセージのサポート (最大 2,048 バイト)                                          | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 長いメッセージの自動連結                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 一度に複数の受信者にメッセージを送信する                                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | べき等のサポート                                                               | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | メッセージのカスタム タグ。                                                             | ✔️   | ✔️    | ✔️    | ✔️      |
| events            | Event Grid を使用して、受信メッセージと配信レポートを受信するように Webhook を構成する | ✔️   | ✔️    | ✔️    | ✔️      |
| 電話番号      | フリーダイヤル番号                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| PSTN 通話      | SMS 対応の無料電話番号に PSTN 通話機能を追加する                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [SMS の送信を開始する](../../quickstarts/telephony-sms/send.md)

次のドキュメントもご覧ください。

- 一般的な [SMS の概念](../telephony-sms/concepts.md)を理解する
- SMS 対応の[電話番号](../../quickstarts/telephony-sms/get-phone-number.md)を取得する
- [Azure Communication Services での電話番号の種類](../telephony-sms/plan-solution.md)
