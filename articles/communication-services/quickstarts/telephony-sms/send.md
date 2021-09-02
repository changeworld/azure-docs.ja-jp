---
title: クイックスタート - SMS メッセージを送信する
titleSuffix: An Azure Communication Services quickstart
description: Azure Communication Services を使用して SMS メッセージを送信する方法について説明します。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 1ace517eefe9b9f579de2975a1809278d5a5afbb
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257749"
---
# <a name="quickstart-send-an-sms-message"></a>クイック スタート:SMS メッセージを送信する

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

> [!IMPORTANT]
> 米国の電話番号との間で携帯ショートメール (SMS) メッセージを送受信することができます。 他の地域の電話番号はまだ、Communication Services の SMS ではサポートされていません。
> 詳細については、 **[電話番号の種類](../../concepts/telephony-sms/plan-solution.md)** に関すページを参照してください。

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET SDK](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript SDK](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python SDK](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java SDK](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>トラブルシューティング

SMS 配信に関連する問題のトラブルシューティングを行うために、[Event Grid で配信レポートを有効にして](./handle-sms-events.md)配信の詳細を取得できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Azure Communication Services を使用して SMS メッセージを送信する方法について説明しました。

> [!div class="nextstepaction"]
> [SMS と配信レポートのイベントを受信する](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [電話番号の種類](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [SMS に関する詳細](../../concepts/telephony-sms/concepts.md)
