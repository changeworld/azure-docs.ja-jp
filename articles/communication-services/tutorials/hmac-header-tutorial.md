---
title: C# を使用して HTTP 要求に署名する方法について学ぶ
titleSuffix: An Azure Communication Services tutorial
description: C# で Azure Communication Services の HTTP 要求に署名する方法について学習します。
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 06/30/2021
ms.topic: tutorial
ms.service: azure-communication-services
ms.openlocfilehash: a3317e483320c55eca6b2ee7edcb4b6671fec7f9
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123256282"
---
# <a name="sign-an-http-request"></a>HTTP 要求に署名する

このチュートリアルでは、HMAC 署名を使用して HTTP 要求に署名する方法について学習します。

[!INCLUDE [Sign an HTTP request C#](./includes/hmac-header-csharp.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして削除するには、リソースまたはリソース グループを削除します。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 [Azure Communication Services のリソースのクリーンアップ](../quickstarts/create-communication-resource.md#clean-up-resources)と [Azure Functions のリソースのクリーンアップ](../../azure-functions/create-first-function-vs-code-csharp.md#clean-up-resources)に関する詳細を確認できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [音声通話をアプリに追加する](../quickstarts/voice-video-calling/getting-started-with-calling.md)

次の記事もご覧ください。

- [チャットをアプリに追加する](../quickstarts/chat/get-started.md)
- [ユーザー アクセス トークンを作成する](../quickstarts/access-tokens.md)
- [クライアントとサーバーのアーキテクチャについて学習する](../concepts/client-and-server-architecture.md)
- [認証について学習する](../concepts/authentication.md)
