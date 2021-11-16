---
title: Azure Active Directory を使用して Azure SignalR Service のアクセスを承認する
description: この記事では、Azure Active Directory を使用して Azure SignalR Service リソースへのアクセスを承認する方法について説明します。
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 0e644f792e47243f3c6924cd8546efb5661a9193
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477888"
---
# <a name="authorize-access-with-azure-active-directory-for-azure-signalr-service"></a>Azure Active Directory を使用して Azure SignalR Service のアクセスを承認する

Azure SignalR Service では、Azure Active Directory (Azure AD) を使用して SignalR リソースへの要求を承認することがサポートされています。 Azure AD を使用することで、ロールベースのアクセス制御 (RBAC) を使用してセキュリティ プリンシパルにアクセス許可を付与することができます<sup>[<a href="#security-principal">1</a>]</sup>。 セキュリティ プリンシパルは、OAuth 2.0 トークンを返す Azure AD によって認証されます。 その後、そのトークンを、SignalR リソースに対する要求を承認するために使用できます。

Azure AD を使用して SignalR に対する要求を承認すると、アクセス キー承認よりも優れたセキュリティと使いやすさを実現できます。 Microsoft では、必要最小限の特権でアクセスできるようにするために、可能な場合は SignalR リソースで Azure AD の認可を使用することをお勧めします。

<a id="security-principal"></a>
 *[1] セキュリティ プリンシパル: ユーザーやリソース グループ、アプリケーション、またはシステム割り当て ID やユーザー割り当て ID などのサービス プリンシパル。*

## <a name="overview-of-azure-ad-for-signalr"></a>Azure AD for SignalR の概要

セキュリティ プリンシパルから SignalR リソースにアクセスを試みる場合、その要求は認証されている必要があります。 Azure AD でリソースにアクセスするには、2 つの手順が必要です。 

1. まず、セキュリティ プリンシパルは、OAuth 2.0 トークンを返す Azure AD によって認証されている必要があります。 
2. 次に、そのトークンが SignalR リソースへの要求の一部として渡され、指定されたリソースへのアクセスを承認するためにサービスによって使用されます。

### <a name="client-side-authentication-while-using-azure-ad"></a>Azure AD 使用時のクライアント側認証

アクセス キーを使用すると、このキーはアプリ サーバー (または Function App) と SignalR リソースの間で共有されるため、SignalR サービスはクライアントからの接続要求を共有キーで認証することができます。 しかし、Azure AD を使って認証する場合は、共有キーがありません。 

この問題を解決するために、クライアント接続用のトークンに署名するために使用できる **一時アクセス キー** を導入しました。 ワークフローは 4 つの手順で構成されています。

1. まず、セキュリティ プリンシパルが自身を認証するために Azure に対して OAuth 2.0トークンを要求します。
2. 次に、セキュリティ プリンシパルにより SignalR Auth API が呼び出され、**一時アクセス キー** が取得されます。
3. 次に、セキュリティ プリンシパルにより、ネゴシエーション時のクライアント接続に使用する **一時アクセス キー** でクライアント トークンの署名が実行されます。
4. 最後に、クライアント トークンを使用して、クライアントが Azure SignalR リソースに接続します。

**一時アクセス キー** は 90 分で期限切れになるため、1 時間に 1 回は新しいアクセス キーを取得して古いアクセス キーをローテーションすることをお勧めします。 

このワークフローは、Microsoft の [Server SDK](https://github.com/Azure/azure-signalr) で構築されています。

## <a name="assign-azure-roles-for-access-rights"></a>アクセス権の Azure ロールを割り当てる

Azure Active Directory (Azure AD) では、[Azure ロールベースのアクセス制御](../role-based-access-control/overview.md)を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 Azure SignalR では、SignalR リソースへのアクセスに使用される一般的なアクセス許可セットを含む一連の Azure 組み込みロールが定義されます。 SignalR リソースにアクセスするためのカスタム ロールを定義することもできます。

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

## <a name="azure-built-in-roles-for-signalr-resources"></a>SignalR リソース用の Azure 組み込みロール

- [SignalR アプリ サーバー](../role-based-access-control/built-in-roles.md#signalr-app-server-preview)

    Websocket 接続作成 API と Auth API へのアクセス。
    
    これは、アプリ サーバーで最も一般的に使用されるロールです。

- [SignalR Service 所有者](../role-based-access-control/built-in-roles.md#signalr-service-owner)

    すべての REST API、WebSocket 接続作成 API、Auth API を含む、すべてのデータプレーン API へのフル アクセス。

    **サーバーレス モード** では、REST API のアクセス許可と Auth API のアクセス許可の両方が必要になるため、Azure AD による認証をサポートするためにこのロールを使用する必要があります。

- [SignalR REST API 所有者](../role-based-access-control/built-in-roles.md#signalr-rest-api-owner)

    データプレーン REST API へのフル アクセス。

    これは通常、接続を行ったり Auth API を呼び出したり "**せずに**"、接続やグループを管理する管理ツールを作成する場合に使用します。

- [SignalR REST API 閲覧者](../role-based-access-control/built-in-roles.md#signalr-rest-api-reader)

    データプレーン REST API への読み取り専用アクセス。

    これは通常、SignalR データプレーンの **READONLY** REST API "**のみ**" を呼び出す、接続やグループを管理する管理ツールを作成する場合に使用します。

## <a name="next-steps"></a>次の手順

Azure アプリケーションを作成し、Azure AD 認証を使用する方法については、以下を参照してください
- [Azure アプリケーションからの Azure AD を使用した Azure SignalR Service リソースへの要求を承認する](signalr-howto-authorize-application.md)

マネージド ID を構成し、Azure AD 認証を使用する方法については、以下を参照してださい
- [マネージド ID からの Azure AD を使用した Azure SignalR Service リソースへの要求を承認する](signalr-howto-authorize-managed-identity.md)

ロールとロールの割り当ての詳細については、以下を参照してください 
- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)。

カスタム役割を作成する方法については、以下を参照してください 
- [カスタム ロールの作成手順](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)