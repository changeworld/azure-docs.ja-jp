---
title: Azure Communication Services の SMS クライアント ライブラリの概要
titleSuffix: An Azure Communication Services concept document
description: SMS クライアント ライブラリの概要とそのオファリングについて説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 6f0b5325ff95d2c2f34572239967151986621bcb
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937187"
---
# <a name="sms-client-library-overview"></a>SMS クライアント ライブラリの概要

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure Communication Services の SMS クライアント ライブラリを使用して、アプリケーションに SMS メッセージングを追加できます。

## <a name="sms-client-library-capabilities"></a>SMS クライアント ライブラリの機能

次の一覧は、クライアント ライブラリで現在使用できる機能を示しています。

| 機能のグループ | 機能                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| コア機能 | SMS メッセージを送信および受信する </br> "*サポートされている Unicode 絵文字*"                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 送信されたメッセージの配信レポートを受信する                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | すべての文字セット (言語/Unicode のサポート)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 長いメッセージのサポート (最大 2048 文字)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 長いメッセージの自動連結                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| events            | Event Grid を使用して、受信メッセージと配信レポートを受信するように Webhook を構成する | ✔️   | ✔️    | ✔️    | ✔️      |
| 電話番号      | フリーダイヤル番号                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| 規制        | オプトアウトの処理                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| 監視        | 送受信されたメッセージの使用状況を監視する                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| PSTN 通話      | SMS 対応の無料電話番号に PSTN 通話機能を追加する                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [SMS の送信の概要](../../quickstarts/telephony-sms/send.md)

次のドキュメントもご覧ください。

- 一般的な [SMS の概念](../telephony-sms/concepts.md)を理解する
- SMS 対応の[電話番号](../../quickstarts/telephony-sms/get-phone-number.md)を取得する
- [SMS ソリューションを計画する](../telephony-sms/plan-solution.md)
