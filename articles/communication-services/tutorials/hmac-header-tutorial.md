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
ms.openlocfilehash: 8a9191180dcf4a16f586d109e820e63def566a42
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488734"
---
# <a name="sign-an-http-request"></a>HTTP 要求に署名する

このチュートリアルでは、HMAC 署名を使用して HTTP 要求に署名する方法について学習します。

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


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
