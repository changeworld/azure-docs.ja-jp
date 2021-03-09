---
title: HTTP 要求に署名する方法 - C#
titleSuffix: An Azure Communication Services tutorial
description: C# で HTTP 要求通信サービスに署名する方法について学習します
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 01/15/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 2ff787c88c044929bb7eec063f4e8b3906813964
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656994"
---
# <a name="sign-an-http-request"></a>HTTP 要求に署名する

このチュートリアルでは、HMAC 署名を使用して HTTP 要求に署名する方法について学習します。

[!INCLUDE [Sign an HTTP request C#](./includes/hmac-header-csharp.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 [Azure Communication Service のリソースのクリーンアップ](../quickstarts/create-communication-resource.md#clean-up-resources)と [Azure Functions のリソースのクリーンアップ](../../azure-functions/create-first-function-vs-code-csharp.md#clean-up-resources)に関する詳細を確認できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [音声通話をアプリに追加する](../quickstarts/voice-video-calling/getting-started-with-calling.md)

次のことも実行できます。

- [チャットをアプリに追加する](../quickstarts/chat/get-started.md)
- [ユーザー アクセス トークンを作成する](../quickstarts/access-tokens.md)
- [クライアントとサーバーのアーキテクチャについて学習する](../concepts/client-and-server-architecture.md)
- [認証について学習する](../concepts/authentication.md)
