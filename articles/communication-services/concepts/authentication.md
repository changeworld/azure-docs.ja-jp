---
title: Azure Communication Services に対する認証
titleSuffix: An Azure Communication Services concept document
description: アプリケーションまたはサービスが Communication Services に対して認証を行うさまざまな方法について説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 9edfb63f5ce43ed325b4c4a1fa67e0e9ca52dc89
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110867"
---
# <a name="authenticate-to-azure-communication-services"></a>Azure Communication Services に対する認証

クライアントと Azure Communication Services とのやり取りはすべて認証される必要があります。 一般的なアーキテクチャでは、認証に [クライアントとサーバーのアーキテクチャ](./client-and-server-architecture.md)、*アクセス キー*、または *マネージド ID* を使用します。

別の種類の認証では、*ユーザー アクセス トークン* を使用して、ユーザーが参加する必要があるサービスに対して認証を行います。 たとえば、チャットまたは呼び出しサービスは、 *ユーザーアクセス トークン* を利用して、ユーザーをスレッドに追加し、相互に会話できるようにします。

## <a name="authentication-options"></a>認証オプション

次の表は、Azure Communication Services の SDK とその認証オプションを示しています。

| SDK    | 認証オプション                               |
| ----------------- | ----------------------------------------------------|
| ID          | アクセス キーまたはマネージド ID                      |
| SMS               | アクセス キーまたはマネージド ID                      |
| 電話番号     | アクセス キーまたはマネージド ID                      |
| 呼び出し           | ユーザー アクセス トークン                                   |
| チャット              | ユーザー アクセス トークン                                   |

ここでは、各認証オプションについて簡単に説明します。

### <a name="access-key"></a>アクセス キー

アクセス キー認証は、信頼できるサービス環境で実行されているサービス アプリケーションに適しています。 アクセス キーは、Azure Communication Services ポータルで確認できます。 サービス アプリケーションでは、これを資格情報として使用して、対応する SDK を初期化します。 [ID SDK](../quickstarts/access-tokens.md)での使用方法については、例を参照してください。 

アクセス キーはリソースの接続文字列の一部なので、接続文字列を使用した認証は、アクセス キーを使用した認証に相当します。

アクセス キーを使用して ACS の API を手動で呼び出す場合は、要求に署名する必要があります。 要求に署名する方法は、[チュートリアル](../tutorials/hmac-header-tutorial.md)の中で詳しく説明します。

### <a name="managed-identity"></a>マネージド ID

マネージド ID は、他の認可オプションよりも優れたセキュリティと使いやすさを提供します。 たとえば、Azure AD を使用すると、アクセス キーの認証と同様に、アカウント アクセス キーをコードに保存する必要がなくなります。 通信サービス アプリケーションでは引き続きアクセス キー認証を使用できますが、Microsoft では、可能な限り Azure AD に移行することをお勧めします。 

マネージド ID を設定するには、 [Azure CLI から登録済みアプリケーションを作成します](../quickstarts/managed-identity-from-cli.md)。 次に、エンドポイントと資格情報を使用して、SDK を認証できます。 [マネージド ID](../quickstarts/managed-identity.md)の使用例を参照してください。

### <a name="user-access-tokens"></a>ユーザー アクセス トークン

ユーザー アクセス トークンは、ID SDK を使用して生成され、ID SDK で作成されたユーザーに関連付けられます。 [ユーザーを作成し、トークンを生成する](../quickstarts/access-tokens.md)方法の例を参照してください。 次に、ユーザー アクセス トークンを使用して、チャットまたは呼び出し SDK で会話に追加された参加者を認証します。 詳細については、「[チャットをアプリに追加する](../quickstarts/chat/get-started.md)」を参照してください。 ユーザー アクセス トークン認証は、セキュリティで保護された Azure リソースではなくユーザーの認証に使用するという点で、アクセス キーやマネージド ID 認証と異なります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Communication Services のリソースを作成して管理する](../quickstarts/create-communication-resource.md)
> [Azure CLI から Azure Active Directory マネージド ID アプリケーションを作成する](../quickstarts/managed-identity-from-cli.md) 
> [ユーザー アクセス トークンを作成する](../quickstarts/access-tokens.md)

詳細については、次の記事を参照してください。
- [クライアントとサーバーのアーキテクチャについて学習する](../concepts/client-and-server-architecture.md)
