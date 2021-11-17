---
title: クイックスタート - アプリに通話の複合を追加する
titleSuffix: An Azure Communication Services quickstart
description: このクイックスタートでは、UI ライブラリから通話の複合の使用を開始する方法について説明します
author: jorgegarc
ms.author: jorgegarc
ms.date: 10/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: a8bd62d55dfe4cf85b4af01bb93d9a4ea44a0a40
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134028"
---
# <a name="quickstart-get-started-with-calling-composite"></a>クイックスタート: 通話の複合の使用を開始する 

UI ライブラリを使用して通信エクスペリエンスをアプリケーションに簡単に統合することにより、Azure Communication Services の使用を開始します。 このクイックスタートでは、通話の複合を Android または iOS アプリケーションに統合する方法について説明します。

UI ライブラリによって、完全な通信エクスペリエンスがアプリケーションに提供されます。 これにより、必要な通話への接続と、バックグラウンドでの通話の設定が行われます。 開発者が検討する必要があるのは、エクスペリエンス内のどこで通信エクスペリエンスを開始するのかだけです。 複合では、デバイスの設定、通話への参加、他の参加者の提供を通じてユーザーを支援します。

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

::: zone pivot="platform-android"
[!INCLUDE [UI Library with Android](./includes/get-started-call/android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [UI Library with iOS](./includes/get-started-call/ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。

リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。
詳細については、[リソースのクリーンアップ](../create-communication-resource.md#clean-up-resources)に関する記事を参照してください。
