---
title: Azure Active Directory を使用して Azure Web PubSub のアクセスを認可する
description: この記事では、Azure Active Directory を使用して Azure Web PubSub サービス リソースへのアクセスを認可する方法について説明します。
author: terencefan
ms.author: tefa
ms.date: 11/08/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: 6682328d948d66085799ffe8f6df93adf732c04d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710427"
---
# <a name="authorize-access-to-web-pubsub-resources-using-azure-active-directory"></a>Azure Active Directory を使用して Web PubSub リソースへのアクセスを認可する
Azure Web PubSub サービスでは、Azure Active Directory (Azure AD) を使用して Web PubSub リソースへの要求を認可することがサポートされています。 Azure AD を使用することで、ロールベースのアクセス制御 (RBAC) を使用してセキュリティ プリンシパルにアクセス許可を付与することができます<sup>[<a href="#security-principal">1</a>]</sup>。 セキュリティ プリンシパルは、OAuth 2.0 トークンを返す Azure AD によって認証されます。 その後、Web PubSub リソースに対する要求を認可するためにそのトークンを使用できます。

Azure AD を使用して Web PubSub に対する要求を認可すると、アクセス キーによる認可よりも優れたセキュリティと使いやすさを実現できます。 Microsoft では、必要最小限の特権でアクセスできるようにするために、可能な場合は Web PubSub リソースで Azure AD の認可を使用することをお勧めします。

<a id="security-principal"></a>
 *[1] セキュリティ プリンシパル: ユーザーやリソース グループ、アプリケーション、またはシステム割り当て ID やユーザー割り当て ID などのサービス プリンシパル。*

## <a name="overview-of-azure-ad-for-web-pubsub"></a>Azure AD for Web PubSub の概要

セキュリティ プリンシパルが Web PubSub リソースへのアクセスを試みる場合、その要求は認可されている必要があります。 Azure AD でリソースにアクセスするには、2 つの手順が必要です。 

1. まず、セキュリティ プリンシパルは、OAuth 2.0 トークンを返す Azure によって認証されている必要があります。 
2. 次に、そのトークンが Web PubSub リソースへの要求の一部として渡され、指定されたリソースへのアクセスを認可するためにサービスによって使用されます。

### <a name="client-side-authentication-while-using-azure-ad"></a>Azure AD 使用時のクライアント側認証

アクセス キーを使用する場合、キーはネゴシエーション サーバー (または関数アプリ) と Web PubSub リソースの間で共有されます。つまり、Web PubSub サービスでは共有キーを使用してクライアント接続要求を認証できます。 ただし、Azure AD を使用して認可する場合は、アクセス キーがありません。 

この問題を解決するために、Azure Web PubSub サービスへの接続に使用できるクライアント トークンを生成するための REST API を提供しています。

1. まず、ネゴシエーション サーバーによって、それ自体を認証するために Azure に対して **Aad トークン** が要求されます。
1. 次に、ネゴシエーション サーバーでは、**Aad トークン** を使用して Web PubSub 認証 API を呼び出して **クライアント トークン** を取得し、それをクライアントに返します。
1. 最後に、クライアントでは、**クライアント トークン** を使用して、Azure Web PubSub サービスに接続します。

Microsoft では、サポートされているプログラミング言語用のヘルパー関数 (GenerateClientAccessUri など) を提供しています。

## <a name="assign-azure-roles-for-access-rights"></a>アクセス権の Azure ロールを割り当てる

Azure Active Directory (Azure AD) では、[Azure ロールベースのアクセス制御](../role-based-access-control/overview.md)を通じて、セキュリティで保護されたリソースへのアクセス権が認可されます。 Azure Web PubSub では、Web PubSub リソースへのアクセスに使用される一般的なアクセス許可セットを含む一連の Azure 組み込みロールが定義されます。 Web PubSub リソースにアクセスするためのカスタム ロールを定義することもできます。

### <a name="resource-scope"></a>リソースのスコープ

セキュリティ プリンシパルに Azure RBAC ロールを割り当てる前に、セキュリティ プリンシパルに必要なアクセスのスコープを決定しなければならない場合があります。 その場合はできるだけ狭いスコープでのみ付与することが推薦されます。 より広い範囲で定義されている Azure RBAC ロールは、その下のリソースによって継承されます。

Azure SignalR リソースへのアクセスのスコープは、次のレベルで指定できます (最も狭いスコープから順に示します)。

- **個々のリソース。** 

  このスコープでは、ロールの割り当てはターゲット リソースにのみ適用されます。

- **リソース グループ。** 

  このスコープでは、ロールの割り当てがリソース グループ内のすべてのリソースに適用されます。

- **サブスクリプション。**

  このスコープでは、ロールの割り当てがサブスクリプションにあるすべてのリソース グループ内のすべてのリソースに適用されます。

- **管理グループ。** 

  このスコープでは、ロールの割り当てが、管理グループのすべてのサブスクリプションにあるすべてのリソース グループ内のすべてのリソースに適用されます。

## <a name="azure-built-in-roles-for-web-pubsub-resources"></a>Web PubSub リソース用の Azure 組み込みロール

- `Web PubSub Service Owner`

    データプレーンのアクセス許可へのフル アクセス (読み取り/書き込み REST API や認証 API を含む)。

    これは、アップストリーム サーバーを構築するために最も一般的に使用されるロールです。

- `Web PubSub Service Reader`

    Web PubSub リソースへの読み取り専用 REST API アクセス許可を付与するために使用します。

    これは通常、Web PubSub データプレーンの **読み取り専用** REST API **のみ** を呼び出す管理ツールを作成する場合に使用します。

## <a name="next-steps"></a>次のステップ

Azure アプリケーションを作成し、AAD 認証を使用する方法については、以下を参照してください
- [Azure アプリケーションからの Web PubSub リソースへの要求を Azure AD を使用して認可する](howto-authorize-from-application.md)

マネージド ID を構成し、Azure AD 認証を使用する方法については、以下を参照してださい
- [マネージド ID からの Web PubSub リソースへの要求を Azure AD を使用して認可する](howto-authorize-from-managed-identity.md)

ロールとロールの割り当ての詳細については、以下を参照してください 
- [Azure のロールベースのアクセス制御とは](../role-based-access-control/overview.md)

カスタム ロールを作成する方法については、以下を参照してください 
- [カスタム ロールの作成手順](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)