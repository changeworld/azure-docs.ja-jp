---
title: クイックスタート - Azure Communication Services を使用して電話番号を管理する
description: Azure Communication Services を使用して電話番号を管理する方法について説明します
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: 19bb79f9a4deaebfacc75918c46a5516d2d398be
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498197"
---
# <a name="quickstart-manage-phone-numbers"></a>クイックスタート: 電話番号を管理する

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

[!INCLUDE [Bulk Acquisition Instructions](../../includes/phone-number-special-order.md)]

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/phone-numbers-portal.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Azure portal](./includes/phone-numbers-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/phone-numbers-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/phone-numbers-python.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/phone-numbers-js.md)]
::: zone-end

## <a name="troubleshooting"></a>トラブルシューティング

よくある質問と問題:

- 電話の購入は、米国でのみサポートされています。 電話番号を購入する場合は、以下を確認してください。
  - 関連付けられている Azure サブスクリプションの請求先住所が米国内にあること。 現時点では、リソースを別のサブスクリプションに移動することはできません。
  - お使いの Communication Services リソースが米国のデータの場所にプロビジョニングされていること。 現時点では、リソースを別のデータの場所に移動することはできません。

- 電話番号を解放しても、請求サイクルが終了するまでその番号は解放されず、再購入することはできません。

- Communication Services リソースが削除されると同時に、そのリソースに関連付けられている電話番号が自動的にリリースされます。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、次の方法について学習しました。

> [!div class="checklist"]
> * 電話番号を購入する
> * 電話番号を管理する
> * 電話番号をリリースする

> [!div class="nextstepaction"]
> [SMS の送信](../telephony-sms/send.md)
> [通話の概要](../voice-video-calling/getting-started-with-calling.md)
