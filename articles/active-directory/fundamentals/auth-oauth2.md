---
title: Azure Active Directory を使用した OAuth 2.0 認証
description: Azure Active Directory を使用した OAUTH 2.0 認証の実現に関するアーキテクチャ ガイダンス。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bb7b35dc4a0e41278fcadd8ed487a7da0f00fc4
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045958"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>Azure Active Directory を使用した OAuth 2.0 認証

OAuth 2.0 は、認可用の業界プロトコルです。 ユーザーは保護されたリソースへの制限付きアクセスを許可できます。 OAuth は、特にハイパーテキスト転送プロトコル (HTTP) を使用するように設計されているため、クライアントの役割がリソース所有者から分離されます。 クライアントでは、リソース所有者によって制御され、リソース サーバーでホストされるリソースへのアクセスが要求されます。 リソース サーバーでは、リソース所有者の承認を使用してアクセス トークンが発行されます。 クライアントはアクセス トークンを使用して、リソース サーバーでホストされる保護されたリソースにアクセスします。 

OAuth 2.0 は、OpenID Connect (OIDC) に直接関連しています。 OIDC は OAuth 2.0 上に構築された認証と認可の層であるため、OAuth 1.0 との下位互換性がありません。 Azure Active Directory (Azure AD) では、すべての OAuth 2.0 フローがサポートされています。 

## <a name="use-for"></a>用途:

リッチ クライアントおよび最新アプリのシナリオと RESTful Web API アクセス。

![アーキテクチャの図](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>システムのコンポーネント

* **[ユーザー]** :Web アプリケーション (アプリ) からサービスを要求します。 一般に、ユーザーはデータを所有し、そのデータまたはリソースへのアクセスをクライアントに許可する権限を持つリソース所有者です。 

* **Web ブラウザー**:ユーザーが操作する Web ブラウザーは、OAuth クライアントです。 

* **Web アプリ**:Web アプリ (またはリソース サーバー) は、リソースまたはデータが存在する場所です。 承認サーバーを信頼し、OAuth クライアントを安全に認証、承認します。 

* **Azure AD**:Azure AD は、ID プロバイダー (IdP) とも呼ばれる承認サーバーです。 ユーザーの情報、アクセス、および信頼関係に関するすべての処理が安全に行われます。 リソースへのアクセスの許可および取り消しを行うトークンを発行する責任があります。

## <a name="implement-oauth-20-with-azure-ad"></a>Azure AD を使用して OAuth 2.0 を実装する

* [Azure AD とアプリケーションの統合](../saas-apps/tutorial-list.md) 

* [Microsoft ID プラットフォームにおける OAuth 2.0 プロトコルと OpenID Connect プロトコル](../develop/active-directory-v2-protocols.md) 

* [アプリケーションの種類と OAuth2.0](../develop/v2-app-types.md) 

