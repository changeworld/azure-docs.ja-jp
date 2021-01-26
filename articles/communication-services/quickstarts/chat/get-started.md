---
title: クイックスタート - チャットをアプリに追加する
titleSuffix: An Azure Communication Services quickstart
description: このクイックスタートでは、Communication Services チャットをアプリに追加する方法について説明します。
author: fanche
manager: phans
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 2d0d80be367dda3689566dec2ade6fd7fc7c01fc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "91665367"
---
# <a name="quickstart-add-chat-to-your-app"></a>クイック スタート:チャットをアプリに追加する

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Communication Services チャット クライアント ライブラリを使用してリアルタイム チャットをアプリケーションに追加することによって、Azure Communication Services の使用を開始します。 このクイックスタートでは、チャット クライアント ライブラリを使用して、ユーザーが相互に会話できるようにするチャット スレッドを作成します。 チャットの概念の詳細については、 [チャットの概念に関するドキュメント](../../concepts/chat/concepts.md)を参照してください。

::: zone pivot="programming-language-javascript"
[!INCLUDE [Chat with JavaScript client library](./includes/chat-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Chat with Python client library](./includes/chat-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Chat with Java client library](./includes/chat-java.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Chat with C# client library](./includes/chat-csharp.md)]
::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、次の方法について説明しました。

> [!div class="checklist"]
> * チャット クライアントを作成する
> * 2人のユーザーでスレッドを作成する
> * メッセージをスレッドに送信する
> * スレッドからメッセージを受信する
> * スレッドからユーザーを削除する

> [!div class="nextstepaction"]
> [チャット ヒーロー アプリを試す](../../samples/chat-hero-sample.md)

次のことも実行できます。

 - [チャットの概念](../../concepts/chat/concepts.md)について学習する
 - [チャット クライアント ライブラリ](../../concepts/chat/sdk-features.md)について理解する
