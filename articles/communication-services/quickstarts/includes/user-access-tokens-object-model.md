---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: c0ba8e76e069bf9dc1c96aecdc670699c32b3c96
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945751"
---
## <a name="object-model"></a>オブジェクト モデル

ユーザー アクセス トークンを使用すると、アプリケーションのユーザーが Azure Communication Services リソースに直接接続することを許可できます。 サーバーでこれらのトークンを生成し、これらをクライアント アプリケーションに渡してから、これらを使用して Communication Services クライアント ライブラリを初期化します。 これを実現するには、ユーザーを認証し、ユーザー アクセス トークンを発行できる信頼されたサービス エンドポイントを構築する必要があります。 このサービスは、アプリケーション ユーザー ID と Azure Communication Services ユーザーの間の永続的なマッピングを維持する必要があります。

CommunicationIdentityClient クラスは、ユーザーとアクセス トークンに関連するすべての機能を提供します。 接続文字列を使用してそれをインスタンス化し、それを使用してユーザーを管理し、アクセス トークンを発行します。

> [!WARNING]
> トークンによりユーザーのリソースへのアクセス権が付与されるため、トークンは機密性の高いデータです。 そのため、侵害される前にトークンを保護することが非常に重要です。 アプリケーションの許可されているユーザーにのみアクセス トークンを発行する、信頼されたサービス エンドポイントを作成する必要があります。 ユーザー アクセス トークンをバッキング ストアにキャッシュする場合は、暗号化を使用することを強くお勧めします。

### <a name="access-token-scopes"></a>アクセス トークンのスコープ

スコープを使用すると、ユーザー アクセス トークンが許可できる Azure Communications Services の機能を厳密に指定できます。 スコープは個々のユーザー アクセス トークンに適用されます。 Azure Communication Services は、ユーザー アクセス トークンに対して次のスコープをサポートしています。

| 名前   | 説明                                                                         |
| ------ | ----------------------------------------------------------------------------------- |
| `Chat` | チャットに参加する機能を許可します                                         |
| `VoIP` | 通話クライアント ライブラリ* を使用して、VOIP 通話を発信および受信する機能を許可します |
| `Pstn` | 通話クライアント ライブラリ* を使用して、PSTN 通話を発信する機能を許可します           |

\* 機能はまだサポートされていませんが、まもなく使用可能になる予定です

ユーザーが特定の機能にアクセスできないようにするには、まず、望ましくないスコープを含む可能性がある[既存のすべてのアクセス トークンを取り消して](#revoke-user-access-tokens)から、より限定されたスコープのセットを持つ新しいトークンを発行する必要があります。
