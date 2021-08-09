---
title: カスタム Teams エンドポイント
titleSuffix: An Azure Communication Services concept document
description: カスタム Teams エンドポイントを構築する
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: acbd6a332d74a9b244f34230ac4b4eb591a1ab2d
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108065"
---
# <a name="custom-teams-endpoint"></a>カスタム Teams エンドポイント

> [!IMPORTANT]
> カスタム Teams エンドポイント エクスペリエンスを有効または無効にするには、[このフォーム](https://forms.office.com/r/B8p5KqCH19)に入力します。

Azure Communication Services を使用してカスタム Teams エンドポイントを構築し、Microsoft Teams クライアントやその他のカスタム Teams エンドポイントと通信できます。 カスタム Teams エンドポイントを利用し、Teams ユーザーのために音声、動画、会話、画面共有の機能をカスタマイズできます。

Azure Communication Services Identity SDK を使用し、AAD ユーザー トークンを Teams のアクセス トークンに交換できます。 次の図はマルチテナントのユース ケースを示したものです。Fabrikam は Contoso という企業の顧客になります。

## <a name="calling"></a>呼び出し 

音声、動画、画面共有の機能は Azure Communication Services Calling SDK から提供されます。 次の図は、呼び出し機能をカスタム Teams エンドポイントに統合するときに従うプロセスの概要を示しています。

![カスタム Teams エンドポイント エクスペリエンスのために呼び出し機能を有効にするプロセス](./media/teams-identities/teams-identity-calling-overview.png)

## <a name="chat"></a>チャット

Graph API を利用してチャット機能を任意で統合する目的でも、カスタム Teams エンドポイントを利用できます。 Graph API の詳細は[こちらのドキュメント](https://docs.microsoft.com/graph/api/channel-post-messages)でご覧ください。 


![カスタム Teams エンドポイント エクスペリエンスのためにチャット機能を有効にするプロセス](./media/teams-identities/teams-identity-chat-overview.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Teams アクセス トークンを発行する](../quickstarts/manage-teams-identity.md)

次のドキュメントもご覧ください。

- Teams の相互運用性の詳細については、[こちら](./teams-interop.md)を参照してください
