---
title: Azure Communication Services の SMS SDK の概要
titleSuffix: An Azure Communication Services concept document
description: SMS SDK とそのオファリングの概要を取り上げます。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: prakulka
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: sms
ms.openlocfilehash: c3564a7c66002241a6b678d7314c4abe92cd3d24
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609633"
---
# <a name="sms-sdk-overview"></a>SMS SDK の概要

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
