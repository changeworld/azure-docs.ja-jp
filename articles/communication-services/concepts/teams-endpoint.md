---
title: カスタム Teams エンドポイントを構築する
titleSuffix: An Azure Communication Services concept document
description: この記事では、カスタム Teams エンドポイントを構築する方法について説明します。
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 2038efe285f36466b0b562d4d87fbd172a44dd42
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469591"
---
# <a name="build-a-custom-teams-endpoint"></a>カスタム Teams エンドポイントを構築する

> [!IMPORTANT]
> カスタム Teams エンドポイント エクスペリエンスを有効または無効にするには、[このフォームを完成させて送信してください](https://forms.office.com/r/B8p5KqCH19)。

Azure Communication Services を使用してカスタム Teams エンドポイントを構築し、Microsoft Teams クライアントやその他のカスタム Teams エンドポイントと通信できます。 カスタム Teams エンドポイントを利用し、Teams ユーザーのために音声、動画、会話、画面共有の機能をカスタマイズできます。

Azure Communication Services Identity SDK を使用して、Teams ユーザーの Azure Active Directory (Azure AD) アクセス トークンを Communication Identity アクセス トークンと交換できます。 次のセクションの図は、架空の会社 Fabrikam が架空の会社 Contoso の顧客であるマルチテナントのユース ケースを示しています。

## <a name="calling"></a>呼び出し 

音声、動画、画面共有の機能は Azure Communication Services Calling SDK から提供されます。 次の図は、呼び出し機能をカスタム Teams エンドポイントに統合するときに従うプロセスの概要を示しています。

![カスタム Teams エンドポイント エクスペリエンスのために呼び出し機能を有効にするプロセスの図。](./media/teams-identities/teams-identity-calling-overview.svg)

## <a name="chat"></a>チャット

Graph API を利用してチャット機能を任意で統合する目的でも、カスタム Teams エンドポイントを利用できます。 Graph API の詳細については、[チャットのリソースの種類](/graph/api/channel-post-messages)に関するドキュメントを参照してください。 

![カスタム Teams エンドポイント エクスペリエンスでチャット機能を有効にするプロセスの図。](./media/teams-identities/teams-identity-chat-overview.png)

## <a name="azure-communication-services-permissions"></a>Azure Communication Services のアクセス許可

### <a name="delegated-permissions"></a>デリゲートされたアクセス許可

|   アクセス許可    |  表示文字列   |  説明 | 管理者の同意が必要 | Microsoft アカウントがサポートされている |
|:--- |:--- |:--- |:--- |:--- |
| _`https://auth.msft.communication.azure.com/VoIP`_ | Teams での呼び出しを管理する | Teams の呼び出しの開始、参加、転送、または終了を行い、呼び出しのプロパティの更新します。 | いいえ | いいえ |

### <a name="application-permissions"></a>アプリケーションのアクセス許可

なし。

### <a name="roles-for-granting-consent-on-behalf-of-a-company"></a>会社に代わって同意を付与するロール

- 全体管理者
- アプリケーション管理者 (プライベート プレビューのみ)
- クラウド アプリケーション管理者 (プライベート プレビューのみ)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Teams アクセス トークンを発行する](../quickstarts/manage-teams-identity.md)

Teams の相互運用性の詳細については、[こちら](./teams-interop.md)を参照してください。
