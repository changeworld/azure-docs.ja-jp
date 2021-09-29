---
title: Azure Communication Services で Azure Functions を使用して、信頼できるユーザー アクセス サービスを構築する
titleSuffix: An Azure Communication Services tutorial
description: Azure Functions を使用して Communication Services 用の信頼できるユーザー アクセス サービスを作成する方法について説明します
author: ddematheu2
manager: chpalm
services: azure-communication-services
ms.author: dademath
ms.date: 06/30/2021
ms.topic: tutorial
ms.service: azure-communication-services
ms.subservice: identity
ms.openlocfilehash: 1c6a5f215d583069884f7c7c0e23b9e4b553bf9c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671509"
---
# <a name="build-a-trusted-user-access-service-using-azure-functions"></a>Azure Functions を使用して、信頼できるユーザー アクセス サービスを構築する

この記事では、Azure Functions を使用して、信頼できるユーザー アクセス サービスを構築する方法について説明します。

> [!IMPORTANT]
> このチュートリアルの最後で作成されるエンドポイントは、セキュリティで保護されていません。 セキュリティの詳細については、[Azure Functions のセキュリティ](../../azure-functions/security-concepts.md)に関する記事を必ず参照してください。 不正なアクターがトークンをプロビジョニングできないように、エンドポイントにセキュリティを追加する必要があります。

[!INCLUDE [Trusted Service JavaScript](./includes/trusted-service-js.md)]

## <a name="securing-azure-function"></a>Azure Functions のセキュリティ保護

ユーザーのアクセス トークンをプロビジョニングするために信頼されたサービスを設定する一環として、そのエンドポイントのセキュリティを考慮して、不正なアクターがサービスのトークンをランダムに作成できないようにする必要があります。 Azure Functions には、さまざまな種類の認証ポリシーを使用してエンドポイントをセキュリティで保護するために使用できる組み込みのセキュリティ機能が用意されています。 詳細は、[Azure Functions のセキュリティ](../../azure-functions/security-concepts.md)に関する記事を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 [Azure Communication Service のリソースのクリーンアップ](../quickstarts/create-communication-resource.md#clean-up-resources)と [Azure Functions のリソースのクリーンアップ](../../azure-functions/create-first-function-vs-code-csharp.md#clean-up-resources)に関する詳細を確認できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のセキュリティについて学習する](../../azure-functions/security-concepts.md)

> [!div class="nextstepaction"]
> [音声通話をアプリに追加する](../quickstarts/voice-video-calling/getting-started-with-calling.md)

次のことも実行できます。

- [チャットをアプリに追加する](../quickstarts/chat/get-started.md)
- [ユーザー アクセス トークンを作成する](../quickstarts/access-tokens.md)
- [クライアントとサーバーのアーキテクチャについて学習する](../concepts/client-and-server-architecture.md)
- [認証について学習する](../concepts/authentication.md)