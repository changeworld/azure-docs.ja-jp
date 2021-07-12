---
title: Azure Communication Services における Teams の相互運用性の概要
titleSuffix: An Azure Communication Services quickstart
description: このクイックスタートでは、Azure Communication Chat SDK を使用して Teams の会議に参加する方法について説明します
author: askaur
ms.author: askaur
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-web-ios
ms.openlocfilehash: c49f2a51dda269c8addcd32c2c82564455bc9743
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113113099"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>クイックスタート: チャット アプリを Teams の会議に参加させる

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

> [!IMPORTANT]
> [チームのテナント相互運用性](../../concepts/teams-interop.md)を有効または無効にするには、[このフォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)を完成させてください。

Azure Communication Services の使用を開始するには、チャット ソリューションを Microsoft Teams に接続します。 

::: zone pivot="platform-web"
[!INCLUDE [Teams interop with JavaScript SDK](./includes/meeting-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Teams interop with iOS SDK](./includes/meeting-interop-swift.md)]
::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [チャットのヒーロー サンプル](../../samples/chat-hero-sample.md)を確認する
- [チャットのしくみ](../../concepts/chat/concepts.md)の詳細を確認する