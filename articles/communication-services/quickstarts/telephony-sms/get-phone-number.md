---
title: クイックスタート - Azure Communication Services を使用して電話番号を取得して管理する
description: Azure Communication Services を使用して電話番号を管理する方法について説明します
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: c03633425a962b1d67b61fc32be89d88fe08ef7b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114448584"
---
# <a name="quickstart-get-and-manage-phone-numbers"></a>クイックスタート: 電話番号の取得と管理

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

一般的な質問と問題:

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
