---
title: Azure Communication Services における Teams の相互運用性の概要
titleSuffix: An Azure Communication Services quickstart
description: このクイックスタートでは、Azure Communication Chat SDK を使用して Teams の会議に参加する方法について説明します
author: askaur
ms.author: askaur
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android-windows
ms.openlocfilehash: 41fdb0a4634d2b6f8f0cdaed37af17cbfba9044d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252138"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>クイックスタート: チャット アプリを Teams の会議に参加させる



Azure Communication Services の使用を開始するには、チャット ソリューションを Microsoft Teams に接続します。 

::: zone pivot="platform-web"
[!INCLUDE [Teams interop with JavaScript SDK](./includes/meeting-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Teams interop with iOS SDK](./includes/meeting-interop-swift.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Teams interop with Android SDK](./includes/meeting-interop-android.md)]
::: zone-end

::: zone pivot="platform-windows"
[!INCLUDE [Teams interop with CSharp SDK](./includes/meeting-interop-windows.md)]
::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [チャットのヒーロー サンプル](../../samples/chat-hero-sample.md)を確認する
- [チャットのしくみ](../../concepts/chat/concepts.md)の詳細を確認する
